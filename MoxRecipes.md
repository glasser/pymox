# Introduction #

Here we provide some common Mox recipes that people have found useful. This section's constantly under development; if you find a better way of implementing a recipe below, please let us know in the comments.

## Set up your Mox test classes in a sane way ##

Note: many other recipes assume you've done this.

```
  def setUp(self):
    self.mox = mox.Mox()

  def tearDown(self):
    self.mox.UnsetStubs()
```

## Stub out a method called from a constructor in the same class ##

TODO(damien): Write a public example here.

## Stub out a static method in the class under test ##

```
  def testFoo(self):

    orig_method = module.class.StaticMethod

    static_stub = staticmethod(lambda *args, **kwargs: None)
    module.class.StaticMethod = static_stub

    self.mox.ReplayAll()

    ...

    self.mox.VerifyAll()

    module.class.StaticMethod = orig_method
```

## Mock a module-level function in a different module ##

```
  def testFoo(self):

    self.mox.StubOutWithMock(module_to_mock, 'FunctionToMock')
    module_to_mock.FunctionToMock().AndReturn(foo)

    self.mox.ReplayAll()
    ...
    self.mox.VerifyAll()
```

## Stub out a class in a different module ##

TODO(damien): Write a public example here.


## Mock a method in the class under test. ##

TODO(damien): Investigate this further. Maybe stubbing out call would help?

```
  def testFoo(self):

    # Note the difference: we instantiate the class *before* Replaying.
    foo_instance = module_under_test.ClassUnderTest()
    self.mox.StubOutWithMock(foo_instance, 'MethodToStub')
    foo_instance.MethodToStub().AndReturn('foo')

    ...

    self.mox.ReplayAll()
    ...
    self.mox.VerifyAll()
```

## Mock a generator in the class under test ##

```
  def testFoo(self):

    ...

    foo_instance = module_under_test.ClassUnderTest()
    self.mox.StubOutWithMock(foo_instance, 'GeneratorToStub')

    mygen = (x for x in [1, 2, 3])
    foo_instance.MethodToStub(mox.IsA(object)).AndReturn(mygen)

    ...

    self.mox.ReplayAll()

    ...

    self.mox.VerifyAll()
```

## Mocking datetime.datetime.now ##

```
import datetime
import mox

m = mox.Mox()

# Stub out the datatime.datetime class.
m.StubOutWithMock(datetime, 'datetime')

# Record a call to 'now', and have it return the value '1234'
datetime.datetime.now().AndReturn(1234)

# Set the mocks to replay mode
m.ReplayAll()
  
# This will return '1234'
datetime.datetime.now()
  
# Verify the time was actually checked.
m.VerifyAll()

# Return datetime.datetime to its default (non-mock) state.
m.UnsetStubs()
```

Alternatively, rewrite your code so that you can mock out datetime.now without Mox:

```
def FunctionBeingTested(now=datetime.datetime.now):
  DoSomethingWith(now())

# in test code
def MyNow(): return 1234
FunctionBeingTested(now=MyNow)
```