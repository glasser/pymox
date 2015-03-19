# Comparing Mox to existing Python mock libraries #

An engineer has a couple of options when picking a mock object library to use for testing.  In this document I will do my best to outline the differences between Mox and other open source alternatives, and why you may or may not prefer to use Mox.

# Other options #

Believe it or not, similar code does exist.

  * The Python Mock Module (http://python-mock.sourceforge.net/): This was the first mock module I used in Python, and arguably the reason why I wrote Mox.

  * pMock (http://pmock.sourceforge.net/): Inspired by jMock, the other side of Java inspired coin.

  * Python Mocker (http://labix.org/mocker): The most similar to Mox.

# Simple use-case comparison #

For now, let's just describe the basic use-case for a mock object.  Let's pretend that we have a class that generates random greeting in a very complex, expensive, way.

```
class Greeter(object):
  def Get(self):
    # expensive code here
    return generated_greeting
```

## Mox ##

```
mock_greeter = mox.MockAnything()
mock_greeter.Get().AndReturn("hello")

mox.Replay(mock_greeter)
assertEquals("hello", mock_greeter.Get())
mox.Verify(mock_greeter)
```

## Python Mock Module ##

Python Mock Module uses a dictionary to define the expected behavior, and verify methods were called.  Using strings for method names seems very fragile to me.  It would also complicate any auto-magic refactoring you might do.

```
mock_greeter = mock.Mock( {"Get" : "hello" })
assertEquals("hello", mock_greeter.Get())
mock.mockCheckCall(self, 0, "Get")
```

## pMock ##

pMock is extremely verbose, and probably very flexible.  Because it lacks a replay state, any calls to the mock that weren't recorded before hand might silently be recorded as void method calls.

```
mock_greeter = pmock.Mock()
mock_greeter.expects(pmock.once()).Get().will(pmock.return_value("hello"))
assertEquals("hello", mock_greeter.Get())
mock_greeter.verify()
```

## Mocker ##

Mocker is very similar to Mox, but with Mocker the return value is set on the `mocker` module rather than the mock itself, which seems awkward to me.

```
mock_greeter = mocker.mock()
mock_greeter.Get()
mocker.result("hello")

mocker.replay()
assertEquals("hello", mock_greeter.Get())
mocker.verify()
```

# Mocking real objects, and caring #

Some of the libraries listed above don't (seem to) offer support for a mock enforcing the interface of the mocked-class.  This kind of enforcement can be very helpful.

## Mox ##

Mox's MockObject enforces the interface of the supplied object.

```
mock_greeter = mox.MockObject(Greeter)
mock_greeter.Get().AndReturn("hello")

mox.Replay(mock_greeter)
assertEquals("hello", mock_greeter.Get())
mox.Verify(mock_greeter)
```

## Python Mock Module ##

```
mock_greeter = mock.Mock({"Get", "hello"}, Greeter)
assertEquals("hello", mock_greeter.Get())
mock.mockCheckCall(self, 0, "Get")
```

## pMock ##

Does not seem to support this

## Mocker ##

Hurray, Mocker does this.

```
mock_greeter = mocker.mock(Greeter)
mock_greeter.Get()
mocker.result("hello")

mocker.replay()
assertEquals("hello", mock_greeter.Get())
mocker.verify()
```

# Ordering / unordering of calls #

Sometimes it is necessary for calls to be ordered (opening datacase connection before issuing a query), or un-ordered (iterating over a dictionary is non-deterministic).  Handling this is done differently in the different libraries.

## Mox ##
Mox imposes ordering by default, because determinism is a good thing (tm) in tests.  Methods are expected to be called in the order they are recorded in.  In the case that you need to iterate over a dictionary, or do other operations with undefined order, you may use unordered groups

```
# Iterating over dictionary keys
mock_intToStr.Convert(1).InAnyOrder().andReturn("one")
mock_intToStr.Convert(2).InAnyOrder().andReturn("two")
```

## Python Mock Module ##
The Python Mock Module doesn't impose any ordering on calls, they're just dictionary lookups.  The only ordering is done through `mockCheckCall` or `mockSetExpectation`.  I'm not sure if a lack of ordering is even possible, or specifying different return values based on parameters. Ugh.

```
TODO?
```

## pMock ##

By default, the calls are unordered, but order can be defined by labeling calls

```
mock_db.expects(pmock.once()).OpenConnection().id("open")
mock_db.expects(pmock.once()).Query().id("query").after("open")
```

## Mocker ##

Like the other libraries, order is not enforced.  Instead of using method names, it uses block notation, which seems pretty neat.

```
with mocker:order():
  mock_db.OpenConnection()
  mock_db.Query()
```

# Raising exceptions #

Often times you'll want to test that your code not only works properly, but fails elegantly.  In these cases, you would like your mock to return some unexpected value (easy to do), or raise an exception.

## Mox ##

```
mock_greeter.Get().AndRaise(Exception("no greetings left"))
mox.Replay(mock_greeter)
assertRaises(Exception, mock_greeter.Get)
mox.Verify(mock_greeter)
```

## Python Mock Module ##

Once again, strings are used for method names, and verification is done by hand.

```
mock_greeter.mockSetExpectation('Get', expectException(Exception))
assertRaises(Exception, mock_greeter.Get)
mock.mockCheckCall(self, 0, "Get")
```

## pMock ##

```
mock_greeter.expects(pmock.once()).Get().will(pmock.raise_exception(Exception("no greetings left")))
assertRaises(Exception, mock_greeter.Get)
mock_greeter.verify()
```

## Mocker ##

```
mock_greeter.Get()
mocker.throw(Exception("no greetings left")

mocker.replay()
assertRaises(Exception, mock_greeter.Get)
mocker.verify()
```