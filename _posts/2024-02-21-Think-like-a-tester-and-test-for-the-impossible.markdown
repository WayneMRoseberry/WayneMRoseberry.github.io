The other day, a friend of mine asked me a question about a discussion they
were having at his place of work. It regarded some code where he had implemented
some correction to invalid data, and had decided to put unit tests to check
that the correction happened. He had received feedback to remove the unit
tests, on the grounds that the condition was impossible.

> Do you think unit tests should test current or correct behavior?
>
> John
>
> I.E. there is a combination of inputs that in practice never happens. Ideally the code should assert that is the case, but it does not and we are adding new test coverage.
> Should there be a unit test asserting the behavior of the code in the incorrect case or should the behavior be left undefined by the test suite.

This is not an easy question to answer. I am going to use a situation similar to what my
friend described to consider different ways of looking at this. Here are the main
points regarding the unit in question and the conditions under which it operates

1. The unit receives as input instructions to process items
2. There are two types of items the code processes, CheckEntity and CheckAudit
3. There are different categories of operation, `add, update, execute_check`
4. The system is built such that `type==CheckEntity AND category==execute_check` is expected to never happen
5. If there were an instance that `type==CheckEntity AND category==execute_check` did occur, it creates an ambiguous data state that confuses further processing

An example input might look like this:

```
{
  "category": "execute_check",
  "data": CheckAudit {
    ...
  }
}
```

Lets pretend the unit under test is a static method called `Processor.processOperation()`. The question is whether there
ought to be a unit test that does the following:

```
// the pattern in this test is vague and wouldn't run because we
// haven't discussed the actual implementation and expected behavior
// in the article yet, but the main point is the test is checking a
// negative condition that is believed to never happen prior to the
// call
public void processOperation_executecheck_checkentity_handlesnegativecase()
{
  var checkEntity = new CheckEntity( ...whatever we use to initialize this...);
  var operation = { category: "execute_check", data: checkEntity};
   Assert.AreEqual( -whatever we expect-, Processor.processOperation(operation));
}
```

_I am using a static method because it makes for shorter examples. 
Given the type of system I am imagining here, I would probably consider 
a static method and how it suggests the presence of a singleton with a 
big nasty chain of dependencies behind it a bad design choice._


Let's consider some of the possibilities.

What is the test checking for?
============================================
Unit tests check almost always perform confirmatory checks against some expected behavior.
We could require several behaviors from the unit under test in this case.

1. Do nothing
2. Do nothing the caller would notice, but emit some event to the logs to track later if it does happen
3. Throw an exception or abort further operation
4. Return an error code
5. Transform the data to mitigate confusion and errors in further processing

For the first two, there is no expected behavior from the unit in response to that
condition. One could write a unit test with the errant data condition as an input, and
then assert that nothing happens than if it were not errant. In the case of writing to logs,
one could assert the entry happened. The thing is, there is no contract that any
consumer of the API is counting on, no behavior they rely on being protected or enforced, so
in that regard the unit test offers nothing for them. A unit test in this case is not
a bad thing, but other than demonstrating coverage, it isn't guarding much.

In the case of the last three, all of those decisions create a behavior that both
the caller and downstream consumers of the code will rely on. In the case of the throw or
the error code, the caller will need to handle both of those, and downstream processes
are relying on never seeing an operation with this errant state. In the case of transformation,
the caller will not need to handle anything, and downstream consumers will need to know to
handle whatever that transformation looks like, but likewise should not ever expect the
errant data state.

Let's look at what the unit test might look like if the code will throw (this varies
depending on the testing framework you use and the language the code is written in):

```
[TestMethod]
[Expect Exception(typeof InvalidOperationException)]
public void processOperation_executecheck_checkentity_handlesnegativecase()
{
  var checkEntity = new CheckEntity( ...whatever we use to initialize this...);
  var operation = { category: "execute_check", data: checkEntity};
  Processor.processOperation(operation);
}
```
The unit test above will help protect the contract, especially for downstream processes.

But it's impossible, so why bother?
============================================
It might seem unnecessary to add unit tests for a state that is impossible
for the system to get into. If the upstream processes of our `processOperation()`
call never produce a combination of `execute_check` and `CheckEntity`, why bother
with the test?

Checking for the supposedly impossible or not feels like one of those cases which separate
testers and developers. The developers say "That cannot happen, why bother?", and then the
tester says "Oh yeah... let me show you..." and goes in search of a way to make it happen. Or
the tester runs the test below the layer where the impossible state is guarded, and then
the developer objects to the entire premise.

Because the state is impossible. Right?

Is it really though?
--------------------------------------------
We might believe something to be impossible, and it really might be, but that is usually
only because of the current state of the system. Systems change. Maybe more importantly,
what we believe about the system and the truth about the system may be very different. There
are some things which make knowing what is impossible or not easier or more difficult.

__Built in language and platform enforcement__

Sometimes the language we use to write the code, or the technology we build it
on, make certain kinds of states or situations impossible. For example, imagine the
two following methods, written in two different languages:

```
// written in C#, with strongly typed inputs
public int countMyFingers(int hands)

// written in JavaScript, with loosely typed inputs
function countMyFingers(hands)
```

In the first version of the method, passing in a non-integer value for `hands` is
an uninteresting and meaningless test because the language and the compiler make
doing so impossible. Likewise checking to be sure that the return value from the `countMyFingers`
is an integer, and even happens at all (outside of a throw) is unnecessary because
the compiler forces it.

Not the case for JavaScript, where both the data types on input and output, as well
as whether or not the method returns a value could vary. In JavaScript, checking these
conditions is an interesting test.

> Use unit tests to protect behavior in situations where the language or platform
do not offer protection.

__Fragile data types__

Datatypes that can build enforcement into them can offer protection and lift a burden
on code that processes those datatypes. Other datatypes have no such capability, pushing
the burden on code that processes them.

In the example I offered, the data type has no opportunity to provide such enforcement,
as the data structure is built dynamically in the code before calling it. The relationship
between `category` and `data` could be anything. It is very similar to arguments and inputs
presented as strings, for example Json or XML, for that matter.

```
  var operation = { category: "execute_check", data: checkEntity};
```

Consider the following alternative:

```
class OperationInstructions
{
  public void OperationInstructions( CategoryEnum category, BaseCheckObject check)
  {
    if(category.Equals(CategoryEnum.ExecuteCheck && check.GetType().Equals(CheckEntity))
    {
      throw InvalidOperationException();
    }
  }
}
```
In this case, the input data cannot be created in the invalid state because
it throws in the constructor. We can move the tests which protect against this condition the
data type and lift the burden on `processOperation()` for having to check at all.

We only have this option when the technology and platform we use permits it. For example,
if implementing a REST API, the inputs are going to be in text format, and if the
language we are using does not allow us to let the platform handle the data binding to
strongly typed objects (as in C# and ASP.NET, for example) then we cannot exploit
such mechanisms so easily.

> When the data type offers no way to self-protect against invalid states, then use
> unit tests agains the code that processes the data to check for handling of invalid states.

and...

> Consider changing the implementation to allow for data types that can either inherently
> offer checking and protection, or that change the problem in such a way that the
> opportunity for invalid data doesn't exist.

__Distant and far away protection__

![diagram of a three tiered system with client, api, and backend where data correctness is checked in the client](/assets/client_api_backend_enforcedatacorrectness.svg)

_The system shown above is a nightmare scenario where we rely on the client to enforce correct data.
Used for illustration only, not at all advised or prescribed._

Where is the guarantee of impossible state happening? Does the immediate caller of
the method provide that guarantee? Is the caller of the method the only one that will
use this method, or is the method available for other parts of the code inside and outside
the component or even system, possibly going to call the method? Does that burden move
likewise to all those callers?

Does the guarantee of impossible state happen further away, in another component or
system? Is this a layered and distributed system? Is this protection happening in
the backend, in the database, in the front end API layer, in web UI, or possibly
even across the cloud and in a client or external system?

Which component is guaranteeing you never have the "impossible" state? The further
away it is, the more possible other components there are, and the less control
you have over that component, the greater risk you have that "impossible" isn't
true.

> When the guard against the errant condition is far away from the code
> under test, and when multiple components may be doing the guarding, add
> unit tests to the code to likewise check the negative data condition.

__Things change, and you don't always understand the system.__
When we say something is impossible, what we really mean is "The system
as I understand it, in the state it is in right now, cannot produce that state."
We can be wrong about the system, even if we built it. We also cannot
know the future and how it may be different than the present, even if we
are the ones that might change the future.

The more complex the behavior of the system, the requirements, the number
of possible states, the more difficult it is for us to understand it. The more
we plan to change something, the more of a future a given thing has and the more
its use is growing, the more likely that some future change will make the
currently impossible suddenly very probable.

> If the system is complex, and if you anticipate a lot of change in the system,
> check handling of errant and negative inputs with a unit test.

The unit may be the only opportunity to test the impossible
--------------------------------------------
Very often code handles enforcement of valid inputs in stacks. A unit
of code deep in the stack may check for maximum and minimum range on values,
or throw if given an invalid combination of inputs. The user interface, for
sake of a better user experience, may check those inputs and inform the user of 
valid ranges, or might somehow disallow creation of those conditions.

![diagram of a system with guards against invalid data in client and in backend, and showing how testing is blocked if only from client](/assets/clientcheckingblocked.svg)

We may still want to check that the unit of code guards against those conditions.
If the layers above the unit provide similar guards, then the only way to test
that behavior will be at the unit test level. You might have separate tests, one at the
unit and one in the UI, but the purpose of the tests are different. The unit test
is to detect if the business logic is allowing invalid states. The UI test is to
detect if the UI logic is protecting the user experience.

This is one of those situations where the developer role, or at least someone
taking on the developer responsibility of checking at the unit level, is the
only role that can do a given test. When enforcement is low level in this way, the
checking should happen that low as well. If it is not checked that low where the
behavior happens, the responsibility to check ripples up the stack where the testing
is far more difficult, slower, and as illustrated above, might be impossible.

> When separate layers guard against invalid or errant conditions, the only
> way to check unit layer guards against invalid conditions is with negative
> tests at the unit level.

Possibly wrapping up now
==============================================
When and how and what we ought to test for is not always an easy question to
answer. It is even more difficult when we consider negative tests against
conditions that we believe the system will never encounter. Whether or not
such conditions are impossible is difficult to know, so you should take
several factors into consideration when deciding how to handle such
conditions and whether to write the unit test.

The factors that motivate considering a unit test are based on the
risk of the "impossible" suddenly becoming possible, and the likelihood
that other code will rely on the behavior of the code in the invalid
data state. Through careful consideration we can build a set of checks
that help us detect regressions before we submit the code, and through
consideration of the testing we might even make design decisions that
make the problem not even a consideration.
