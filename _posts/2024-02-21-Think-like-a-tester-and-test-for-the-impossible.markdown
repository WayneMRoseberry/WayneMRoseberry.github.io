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
what we believe about the system and the truth about the system may be very different.

The unit may be the only opportunity to test the impossible
--------------------------------------------




Raw transtript between myself and John
======================================

You sent
If there is a behavior you want to be sure the code does, you should put a unit test to check.
John
John Patterson
That's the thing. We don't particularly care that the code does this. It is kind of a bug.
You sent
How bad is the state in question if the code keeps going?
You sent
Severity is a guide to formality
You sent
If the severity is bad, it is a good idea to formalize the expectation.
You sent
People have been killed by inputs believed to never happen
You sent
Very much depends on what is at stake
Tue 9:24 AM
John
John Patterson
Not too bad. No financial consequences. Just some confused customers if it broke.
Tue 12:18 PM
You sent
I think the important part to put unit tests around are the behaviors we rely on, particularly those that are part of the contract.
You sent
Assume there is an input state that you need the unit to object to were the caller ever to invoke the interface with that state, then put a unit test up to check for it.

This way you know what the code will do if the caller ever did that.
You sent
If you don't rely on the unit noticing it, then maybe you don't create a unit test for it?
John
John Patterson
Now pivoting a little. Would you advocate removing that test if it were already written?
You sent
That is difficult to know.
You sent
What is the code doing in the case of these inputs that never happen?
John
John Patterson
The context this: we are the frontend team for folks managing their money & onboarding for platforms using Adyen.

Our partner team performs KYC (know your customer regulation) checks on documents, legal entities, etc. and records this data for us in one format.

This code reads those KYC results, filters them down to the events we care about showing users, and aggregates some types of events together into one status. 

There are two check types: entity checks and audit checks. A parent KYC record can have one or the other. There's also a brilliantly named category0 enum field which should have the operation performed (add, update, execute_check, etc). It should be impossible to have entity data and category0 of execute_check. Similarly we would never add something with check data.

The structure is something like:
{
  "category0": "execute_check",
  "data": CheckAudit {
    ...
  }
}

IF that happens, there is no other dimension differentiating an entity and a check, so if the version id, category0, and category1 all matched they would be collapsed to one row.

This test asserts that happens.

The consequence is that the KYC timeline shown to the ends users would be confusing. Depending on which "wins" the aggregation later in the code, it would appear like an entity disappeared or a check disappeared. There would be no consequence to the actual underlying KYC operation nor to the onboarding status of the account, but it could be difficult for the customer or their account rep to reason about the state of the account.
John
John Patterson
In this case, none of this code was covered with a test, so I wrote about 80 tests covering all the branches I could find. The original author is taking umbrage with this test, saying we shouldn't test malformed or impossible situations. I disagreed pretty strongly and said I would add a comment explaining why the test should be impossible so no one is led astray. I am sanity checking my intuition here.
You sent
Is the interface json?
You sent
or, rather, the datatype... is it just a json string?
You sent
like through REST or other wire protocol?
You sent
The assertion "impossible situations" is determined somewhat by the technology.
You sent
If this were in Java or C# where the nature of the data structures and language itself can enforce the impossibility, then there may be a valid argument.

But if this is a text based data format where you don't know what the caller is using to construct the package, then "impossible" is wishful thinking.
John
John Patterson
I'm actually not sure in this case.
John
John Patterson
I agreed with his larger point that we should probably throw on this case or something like that, but I refused to change the code behavior in the same PR.
You sent
"probably throw on this case"

That depends on your system.

It sounds like the current behavior is to do something with the data in this case that prevents some further later harm... and if that is the case, then it seems to me you should put in a unit test that ensures that transformation happens and does what you want, because if it really is happening, downstream code is at some point going to rely on it... past behavior tends to do that.

If on the other hand you decide to throw in that case, then I would write a unit test that looks for the exception. Because now you are saying that in this case further processing should stop - and again, downstream code is going to rely on never getting something from further up the pipeline in this state. So you want to know the throw happens.
You sent
I have built a career on seeing "impossible" happen.
You sent
The decision fork you describe, both of them seem to change the state of something with regard to workflow processing. That seems something to write unit checks for. You have created a contract.
You sent
If, on the other hand, the response was to record an event to the logs that something seemed wrong but otherwise left it untouched, then the relative in-action as far as the rest of the system is concerned might warrant skipping the unit tests.

Depends on how much you want to see that log entry.
You sent
Impossible == "will not ever happen in the current state of the system, as I believe it behaves, right now"

That can be wrong in two ways. Your belief about current state of the system, and future changes to the system.
You sent
Likewise for severity - what might be minor confusion right now may be more important with later changes that take action based on data state. Maybe some new workflow initiates that have significance, and being in the bad data state might do damage.
