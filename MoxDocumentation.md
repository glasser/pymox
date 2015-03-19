# Introduction #

Mox is a mock object framework for Python.

Mox is based on [EasyMock](http://www.easymock.org/), a Java mock object framework.

Mox will make mock objects for you, so you don't have to create your
own! It mocks the public/protected interfaces of Python objects.  You
set up your mock object's expected behavior using a domain-specific
language (DSL), which makes your tests easy to understand and refactor!

# Installing Mox #

[Download the latest version of Mox](http://code.google.com/p/pymox/downloads/list?q=label:Featured).
Expand the tarball, and run:

```
$ python setup.py install
```

To run Mox's own tests, run:

```
$ python mox_test.py
```

# Core concepts #

When you create a mock object, it is in _record mode_.  You record the
behavior you expect by calling the expected methods on the mock
object.  Once you have recorded the expected behavior, you switch the
mock into _replay mode_.  Then you start your actual test code.
After your testing is done (usually along with other assertions)
you _verify_ that all recorded (expected) interactions
occurred.

Mox is strict.  That means that it expects the methods to be called in the order they are recorded, on a per-mock level. This is helpful to ensure that a database connection is not closed before it is used, etc.

## Workflow overview ##

  * Create mock (in record mode)
  * Set up expectations
  * Put mock into replay mode
  * Run test
  * Verify expected interactions with the mock occurred

# Basic Usage #

## General Usage Example ##

```
import unittest
import mox

class DaoUnitTest(unittest.TestCase):

  def setUp(self):
    # Create an instance of Mox
    self.person_mocker = mox.Mox()

  def testUsingMox(self):
    # Create a mock PersonDao
    dao = self.person_mocker.CreateMock(PersonDao)

    # Return a value when this method is called
    dao.InsertPerson(test_person).AndReturn(test_primary_key)

    # Void method
    dao.UpdatePerson(test_person)

    # Raise an exception when this is called
    dao.DeletePerson(unknown_person).AndRaise(UnknownPersonError('id not found'))

    # Put all mocks created by mox into replay mode
    self.person_mocker.ReplayAll()

    # Run the test
    ret_pk = dao.InsertPerson(test_person)
    dao.UpdatePerson(test_person)
    self.assertRaises(UnknownPersonError, dao, unknown_person)

    # Verify all mocks were used as expected, and tests ran properly
    self.person_mocker.VerifyAll()
    self.assertEquals(test_primary_key, ret_pk)
```

Or to create a single mock object directly without the
`Mox()` factory:

```
dao = mox.MockObject(PersonDao)
dao.InsertPerson(test_person).AndReturn(test_primary_key)
dao.UpdatePerson(test_person)
dao.DeletePerson(unknown_person).AndRaise(UnknownPersonError('id not found'))
mox.Replay(dao)
...
mox.Verify(dao)
```

Optionally, you can make your test case be a subclass of
`mox.MoxTestBase`; this will automatically create a mock object factory
in `self.mox`, and will automatically verify all mock objects and
unset stubs at the end of each test.

## Unexpected Behavior ##

Occasionally, snakes on a plane happen: some code uses your mock
object in a way you weren't expecting it to.  Fortunately for everyone
involved, Mox will let you know exactly what unexpected event
happened.

### Unexpected method call ###

```
  dao = self.person_mocker.CreateMock(PersonDao)
  dao.InsertPerson(test_person).AndReturn(test_primary_key)
  self.person_mocker.ReplayAll()
  ret_pk = dao.InsertPerson(other_person)
  self.person_mocker.VerifyAll()
```
raises
```
  Unexpected method call: InsertPerson(other_person) -> None.  Expecting: InsertPerson(test_person) -> test_primary_key
```

### Unknown method call ###
```
  dao = self.person_mocker.CreateMock(PersonDao)
  dao.InsertPersonZ(test_person).AndReturn(test_primary_key)
  self.person_mocker.ReplayAll()
```
raises
```
  Method called is not a member of the object: dao.InsertPersonZ(test_person)
```

### Expected method call (but it didn't happen) ###
```
  dao = self.person_mocker.CreateMock(PersonDao)
  dao.InsertPerson(test_person).AndReturn(test_primary_key)
  self.person_mocker.ReplayAll()
  self.person_mocker.VerifyAll()
```
raises
```
Verify: Expected methods never called:
  0.  InsertPerson(test_person) -> test_primary_key
```

### Threading issues ###

Mock objects created by Mox are not thread-safe.  If you are replaying mocks in a multi-threaded environment, please guard the mocks via mutex.

Specifically, the code to validate that the current call matches the recorded call can result in a race condition.

Hopefully soon there will be an option to make the mocks thread safe!

# Advanced Usage #

Believe it or not, there are other features as well!

## In Any Order ##

Unfortunately, there are some things that are non-deterministic, such
as iterating over the keys of a dictionary.  For these cases, you'll
want to group your un-ordered method calls together.  This creates a
group of method calls that are unordered with respect to each other,
but ordered with respect to other expectations.  For example:

```
  dao.OpenConnection()
  dao.Call(1).InAnyOrder().AndReturn('one')
  dao.Call(2).InAnyOrder().AndReturn('two')
  dao.Call(3).InAnyOrder().AndReturn('three')
  dao.CloseConnection()
  mox.Replay(mock)
```

The `Call` methods can occur in any order, but they must all occur
after `OpenConnection` and before `CloseConnection`.

It is also possible to have two consecutive groups of `InAnyOrder`.
In order to differentiate between the two groups, you would give names
to one or both of the groups.

```
  dao.OpenConnection()
  dao.Foo(1).InAnyOrder('foo').AndReturn('one')
  dao.Foo(2).InAnyOrder('foo').AndReturn('two')
  dao.Foo(3).InAnyOrder('foo').AndReturn('three')
  dao.Bar('one').InAnyOrder('foo').AndReturn(1)
  dao.Bar('two').InAnyOrder('bar').AndReturn(2)
  dao.Bar('three').InAnyOrder('baz').AndReturn(3)
  dao.CloseConnection()
  mox.Replay(mock)
```

The `Foo` calls can still occur in any order, but they must all occur
before the unordered `Bar` calls occur.

## Stub Out ##

Often, the class you're testing has one method that delegates to a lot
of other complex methods.  The delegation logic can be complicated, so
you only want to test that, without having to record expectations for
all of the work done by the submethods.  For example:

```
class MyRequestHandler(object):
  def HandleRequest(self, request):
    if request.IsExternal():
      self.Authenticate(request)
      self.Authorize(request)
      self.Process(request)
    else:
      self.ProcessInternal(request)
```

Here, `Authenticate`, `Authorize` and `Process` are all expensive, and
have tons of logic in them.  You don't really want or need to test
what they do; you just need to test that they're called.  But the
`MyRequestHandler` isn't a mock object here: it's the actual object
you're testing.  So what do you do...?!  Use `StubOutWithMock`!

```
handler = MyRequestHandler()
m = mox.Mox()
m.StubOutWithMock(handler, "Authenticate")
m.StubOutWithMock(handler, "Authorize")
m.StubOutWithMock(handler, "Process")
handler.Authenticate(IsA(Request))
handler.Authorize(IsA(Request))
handler.Process(IsA(Request))
m.ReplayAll()

handler.HandleRequest(request)

m.UnsetStubs()
m.VerifyAll()
```

Note: If `UnsetStubs()` was called after `Verify()` and `Verify()`
raises an exception because it fails then the rest of your tests may
end up in a strange state.  You should either call it before
`Verify()` or -- even better -- call it in `tearDown()` which gets
executed regardless of whether `Verify()` fails or succeeds.  (If you
use `mox.MoxTestBase`, this is taken care of for you.)

## Comparators ##

If you aren't able to pass a argument which is equal (according to
`__eq__`) to the expected argument when you're recording mock
behavior, you probably want to use a Comparator.

  * `IsA(class)` -- Check if the parameter is an instance of the given class
```
  dao.InsertUser(IsA(Person))
```
  * `StrContains(string)` - Check if the parameter contains the given
> substring
```
  dao.RunSql(StrContains('WHERE id=%d' % expected_id))
```

  * `Regex(pattern [, flags])` - Check if the parameter matches the
> given regular expression
```
  dao.RunSql(Regex(r'WHERE.*\s+id=%d' % expected_id, flags=re.IGNORECASE))
```

  * `In(value)` - Check if the parameter (list, tuple, or dict) contains the given value
```
  dao.BulkInsert(In(test_person))
```

  * `ContainsKeyValue(key, value)` - Check if the parameter contains the given key/value pair
```
  dao.BulkInsert(ContainsKeyValue(test_id, test_person))
```

  * `Func(callable)` - Validate the parameter with the given callable.  This can be used for more complex checking. The callable must take 1 argument and return a bool.
```
  dao.InsertAuditRecord(Func(IsValidAudit))
```

  * `IsAlmost(value [, places])` - Check if the parameter is equal to a given value up to a certain number of decimal places.  Useful for floating point numbers.
```
  dao.AddInterestToAccount(IsAlmost(0.05))
```

  * `SameElementsAs(sequence)` - Check if the sequence returned has the same elements as the given sequence.  Useful for lists that may be generated with non-deterministic order.
```
  dao.ProcessUsers(SameElementsAs([person1, person2]))
```

  * `IgnoreArg()` - Ignore an argument.
```
  # Check first and third arguments; but ignore 2nd argument.
  dao.UpdateUser(3, IgnoreArg(), 'admin')
```

  * `And()` and `Or()`: combine comparators.  These both take a variable number of comparators.
```
  dao.BulkInsert(And(In(test_person), IsA(list)))
```

You can write your own comparators.  It's easy!

## `MockAnything` ##

Some classes do not provide public interfaces; for example, they might
use `__getattribute__` to dynamically create their interface.  For
these classes, you can use a `MockAnything`.  It does not enforce any
interface, so any call your heart desires is valid.  It works in the
same record-replay-verify paradigm.  Don't use this unless you
absolutely have to!  You can create a `MockAnything` with the
`CreateMockAnything` method of your `Mox` instance like so:
```
m = mox.Mox()
mock = m.CreateMockAnything()
mock.AnyMethod()
```

You may also create a `MockAnything` instance directly, but then you must call `mox.Replay()` and `mox.Verify()` on it, instead of using the Mox factory methods.
```
mock = mox.MockAnything()
mock.AnyMethod()
mox.Replay(mock)

mock.AnyMethod()

mox.Verify(mock)
```

## Attributes ##

Some classes automatically create attributes on creation.  If you stub
out a class, then these attributes will not be created.  You have to
define these attributes in your `MockObject` on your mock setup.

```
m = mox.Mox()

fake_axis = m.CreateMock(MyAxis)

fake_chart = m.CreateMock(MyChart)
fake_chart.axis = fake_axis
```

## Mock a class ##

You may have code that doesn't use dependency injection, and just creates objects directly. You may also want to mock those objects.  Thankfully this is possible with Mox.

For example, to stub out the foo.bar module which contains the Baz class that your code creates directly:

```
  # Mock out the class using Mox.
 self.mox.StubOutClassWithMocks(foo.bar.Baz)
  # Record that the creation of Baz should return a mock baz.
 mock_baz = foo.bar.Baz()
```


## Side Effects ##

Sometimes the behavior of the code you are testing is dependent on
some side effect of the object you are mocking.  Some examples of when
this is the case are when real object might treat some object as an
"out" or "in/out" parameter or the real object is meant to change some
shared resource that modifies the behavior of your testing unit.  It
is possible to simulate these side effects by using
`WithSideEffects`.

```
  # This function will be passed to WithSideEffects; when
  # GetWaitingMessages is called on the mock, this function will be
  # called with the same arguments as GetWaitingMessages.
  def add_messages(message_list):
    message_list += ['message 1', 'message 2']

  message_appender = mox.MockObject(PendingMessages)
  message_appender.GetWaitingMessages(['message 0']).WithSideEffects(add_messages).AndReturn(2)
  mox.Replay(message_appender)

  messages = ['message 0']
  new_messages = message_appender.GetWaitingMessages(messages)
  mox.Verify(message_appender)
  assertEquals(['message 0', 'message 1', 'message 2'], messages)
```

## Callbacks ##

Mocking a callback should be pretty straight forward.
```
m = mox.Mox()
mock_callback = m.CreateMockAnything() # MockAnything is callable
test_object.SetCallback(mock_callback)
mock_callback(42) # Expect this to be called.

m.ReplayAll()
test_object.DoStuff() # Which in turn calls mock_callback...
m.VerifyAll()
```

## Misc ##

I've seen code that likes to access class variables through instances,
so I've added support for this.

```
 print 'this is silly, but it happens:', mock_obj.MY_CLASS_VARIABLE
```

There is support for comparing mock objects.  This could be helpful for
testing that your mock got injected into the proper places:

```
  dao.set_db(mock_db)
  self.assertEquals(mock_db, dao._MyDAO__db)
```

I've also seen code that likes to verify if an object is false, for
example:

```
  def myMethod(self, foo, bar=None):
    if not bar:
      # use internal default
```

To deal with this, you can make your mock expect `__nonzero__`, so you
can safely inject your mock into this object.  Hurray!

# Examples #

## Basic Example ##
Let's say you have this class, and you'd like to test it:

```
class PersonManager(object):

  def __init__(self, person_dao):
    self._dao = person_dao

  def CreatePerson(self, person, user):
    """Create a Person"""

    if user != 'stevepm':
      raise Exception('no way, jose')

    try:
      self._dao.InsertPerson(person)
    except PersistenceException, e:
      raise BusinessException('error talking to db', e)
```

And you have the class `PersonManager` depends on:

```
class PersonDao(object):

  def __init__(self, db):
    self._db = db

  def InsertPerson(self, person):
    self._db.Execute('INSERT INTO Person(name) VALUES ("%s")' % person)
```

So now you can write the test:

```
class PersonManagerTest(unittest.TestCase):

  def setUp(self):
    self.mox = mox.Mox()
    self.dao = self.mox.CreateMock(PersonDao)
    self.manager = PersonManager(self.dao)

  def testCreatePersonWithAccess(self):
    self.dao.InsertPerson(test_person)
    self.mox.ReplayAll()
    self.manager.CreatePerson(test_person, 'stevepm')
    self.mox.VerifyAll()

  def testCreatePersonWithDbException(self):
    self.dao.InsertPerson(test_person).AndRaise(PersistenceException('Snakes!'))
    self.mox.ReplayAll()
    self.assertRaises(BusinessException, self.manager.CreatePerson, test_person, 'stevepm')
    self.mox.VerifyAll()
```

Pretty cool, huh?

## Extending The Basic Example ##

Now let's say you want to have your DAO return the new primary key for
the person, and your manager class would like to verify that the
primary key is greater than some number.  Who knows, it's a toy
example! :) You would change your code as follows:

```
  def CreatePerson(self, person, user):
    """Creates a Person."""

    if user != 'stevepm':
      raise Exception('no way, jose')

    try:
      primary_key = self._dao.InsertPerson(person)
    except PersistenceException e:
      raise BusinessException('error talking to db', e)

    if primary_key < MIN_PRIMARY_KEY_VALUE:
      self._dao.DeletePerson(primary_key)
      raise BusinessException('primary key too small')
```

```
  def InsertPerson(self, person):
    return db.Execute('INSERT INTO Person(name) VALUES ("%s")' % person)

  def DeletePerson(self, person_id):
    db.Execute('DELETE FROM Person WHERE ...' % person_id)
```

Now you can modify your test:
```
  def testCreatePersonWithAccess(self):
    self.dao.InsertPerson(test_person).AndReturn(HUGE_PRIMARY_KEY)
    self.mox.ReplayAll()
    self.manager.CreatePerson(test_person, 'stevepm')
    self.mox.VerifyAll()
```

And add the new test:
```
  def testCreatePersonWithSmallPrimaryKey(self):
    self.dao.InsertPerson(test_person).AndReturn(TINY_PRIMARY_KEY)
    self.dao.DeletePerson(TINY_PRIMARY_KEY)
    self.mox.ReplayAll()
    self.assertRaises(BuisnessException, self.manager.CreatePerson, test_person, 'stevepm')
    self.mox.VerifyAll()
```

## Complicating Things Even More... ##

Ugh, now let's say it is up to your manager to pass some audit trail
object to the DAO, which the DAO handles appropriately.  Let's not
worry about the impl, since we're really just dealing with public
interfaces.  The new DAO interface is:

```
  def InsertPerson(self, person, audit_trail_obj):
```

And the manager now looks like this:

```
  def CreatePerson(self, person, user):
    """Create a Person"""

    if user != 'stevepm':
      raise Exception('no way, jose')

    audit_record = AuditRecord(user)

    try:
      primary_key = self._dao.InsertPerson(person, audit_record)
    except PersistenceException e:
      raise BusinessException('error talking to db', e)

    if primary_key < MIN_PRIMARY_KEY_VALUE:
      self._dao.DeletePerson(primary_key)
      raise BusinessException('primary key too small')
```

Oh now, how do we setup our expected call to `dao.InsertPerson` now that
a parameter is out of our control?!  Have no fear, Mox is here!  There
are Comparators that can be used to check the equivalency of method
parameters.  You can even mix and match then with real parameters, as
you'll see below.

```
  def testCreatePersonWithAccess(self):
    self.dao.InsertPerson(test_person, IsA(AuditRecord)).AndReturn(HUGE_PRIMARY_KEY)
    self.mox.ReplayAll()
    self.manager.CreatePerson(test_person, 'stevepm')
    self.mox.VerifyAll()
```

There are all kinds of other comparators for simple parameter checking.  If you have
complex logic to check the value, you can even use a callable to verify it.

```
def testCreatePersonWithAccess(self):
  self.dao.InsertPerson(test_person, Func(ValidAuditRecord)).AndReturn(HUGE_PRIMARY_KEY)
  self.mox.ReplayAll()
  self.manager.CreatePerson(test_person, 'stevepm')
  self.mox.VerifyAll()

def ValidAuditRecord(audit_record):
  return (audit_record.user() == 'stevepm' and audit_record.type() == 'insert')
```