My definition of flaky results is as follows:
__Flake__ _when you get different results from executing a test multiple times without changing the test conditions_.

That sounds simple, but there is a lot of complexity wrapped up in that statement.

Without Changing the Test Conditions
====================================
Is it possible to actually have different results from the same test conditions?
Casual experience might suggest yes, but when we consider the way we understand
the universe (at the macro level, this is not quantum physics), then under
the same conditions we must get the same outcomes every time.

It is not that the conditions are unchanged. What happened is something changed
which we do not understand or control.

Conditions versus Test Condtions
====================================
Let's define two terms:

__Conditions__ _aspects of a system state that have an impact on the outcome
of changes to the system._

__Test Conditions__ _aspects of a system state that a test procedure is able
to either measure or control._

The difference in these two terms describes a boundary around any system. We
commonly call that boundary a Black Box.

An example
====================================
A website has a scheduled timer job that executes every X minutes
to look for data to purge from the system. The site exposes a REST
API with a set of CRUD operations for objects that site handles.

A set of automated checks use the REST API to check product functionality.
The automated checks do not have access to the server, its settings,
or its diagnostic output. All the checks are able to do is execute
against the REST API.

The state of the timer job, idle, executing, and down what action
is unavailable to the automatced checks. This set of states are conditions
of the system under test, but not test conditions.

The inputs to the REST API, and the website response to those inputs
are under control of the automated checks. These are test conditions.

Everything behind the REST API is a black box.

Time(r) for a flaky example
------------------------------------
Let's imagine there exists a race condition bug
where adding a certain type of object via the REST API
will get purged if the timer job and the addition of the
object line up at precisely the correct time. Maybe some
piece of data on the object keeps it from being purged, and
maybe the initialization of that data is happening after
the timer job checks.

If we run the check which adds this object, sometimes
it is going to write the object during a window where
the object is purged by the timer job. If the procedure
then checks if the object is in the store, it will see
it is missing, and reports a failed expectation.

If we run the check again, chances are it will not
happen.

A __condition__ within the product changed, but it was
not a __test condition__ because the test is not able to
either observe nor control that condition. This manifests
as a flaky result.

The inherent flake of this system test
------------------------------------
The flaky problem we hit arises as a side effect of the
black box nature of our system state. So long as the
conditions which affect the system behavior are not
included in the test conditions there will be potential
for flake. We don't plan the flake, it arises when the system
is affected by conditions we do not anticipate.

The flake can be frustrating. But we also need to learn
how to make it happen. The system is a black box to
everyone, not just tests, which means that the same
conditions the tests cannot control will almost always
be the same for customers and end users. Usually more
restricting. Whether or not a certain bug always occurs
when a certain condition is true does not make that
bug feel consistent and reliable to the customer when
they are unable to control or observe the condition. For all
practical purposes, from the sake of experience with
the black box, the bug is flake.

From testing perspective, we need to fronts of
attack on this problem. We need to gain control
of more test conditions on the one hand to get
precise target of bugs we expect to find, and we
need to continue to surrender control to discover
the bugs we did not expect.

Gaining Control
====================================
We gain control by making conditions that affect the system
either controllable or observable by the test. We make 
test conditions of them.

Sometimes the conditions are already available and the test
framework hasn't taken advantage. In those cases it is a matter
of rolling up our sleeves and getting to work.

Sometimes we lose control because of poor test code design.
I am not going to go into a list here, because so many people
have done a splendid job of covering this territory.

But the most powerful, the most effective way to convert
conditions to test conditions is to change the product code.
The product must be designed to offer control. The product
must be built to be testable.

Control Example
------------------------------------
I am going to shift all the way to the unit test level
because it really does start there.

Consider the following code:

```
public void PurgeItems(ItemSet items)
{
  foreach(Item i in items)
  {
    if(i.CreateDate < DateTime.Now().AddDays(PURGE_OFFSET))
    {
      if(!Purge(i))
      {
          throw new ItemNotPurgedException(i);
      }
    }
  }
}
```

The conditions that affect the input oject, items,
and the current system date, as well as a set of
hidden conditions behind whatever happens in 
Purge(). The test conditions are
just the contents of the obect items. Any test
method is unable to control exactly what time
PurgeItems believes it is comparing the i.CreateDate
against. It is also unable to check PurgeItems'
behavior when Purge() returns something other than
true. It is also unable to check PurgeItems' behavior
were Purge() to likewise throw.

For example, what if PurgeItems() is supposed to
purge only down to hour level granularity? How can the
test code setup that condition? What if there are special
cases of failure in Purge() that PurgeItems ought to
handle, maybe skip and move to the next? No test method
could check for that.

Consider this change in design:
```
public void PurgeItems(ItemSet items, IPolicyCheck policy, IItemManager itemManager)
{
  foreach(Item i in items)
  {
    if(policy.GetCurrentTime().AddDays(PURGE_OFFSET))
    {
      if(!itemManager.Purge(i))
      {
          throw new ItemNotPurgedException(i);
      }
    }
  }
}
```

The above implementation turns the current time, and the
behavior of Purge() into test conditions. The test
code can implement mocks of IPolicyCheck and IItemManager
and implement whatever behavior they want to cover for PurgeItems.

Careful observation should reveal I didn't fix any of the bugs
mentioned above. I left it that way to demonstrate that
there is still a point to doing the test. Were someone to
write a unit test that checks for sub-hour precision, or
cause Purge to throw various exceptions the bugs above would
be exposed.

Unit Level Design Changes Accrue and Ripple Up
==============================================
I started the discussion of gaining control via design change
from unit tests because it is the most effecive, most
powerful way to remove flake from a system. With some exceptions
(IO being the main one), if one cannot remove flaky behavior
from a unit test then the code under test is designed
improperly.

But beyond enabling tests, and what most proponents
of unit testing will say is that they force a design
that is more stable, more extensible, more modular and
easier to change later. We create loosely coupled
objects that are tied together by interfaces and contracts
rather than being tightly coupled dependencies.

From a testing perspective, this means we are able to
replace, wrap, or extend the system for sake of testing.

In the PurgeItems example above, IItemManager is used
to replace the call to Purge() for sake of running a check
against PurgeItems. If we keep this pattern going of providing
interfaces between components, we can replace, wrap, or extend
with larger and larger systems.

We could, for example, replace an entire cloud based storage
system with something running on a local file store, in order
to facilitate longer running tests that do not need to
connect to the cloud, introducing delays, noise, live accounts,
and higher costs. We will not be testing a real system, but
if the components are designed in a way that is loosely
coupled we can exercise a lot of functionality under far
more control.

And control is how we reduce, and if we can eliminate, flake
during testing. Control also opens up coverage.

Think of test conditions as data domains, or state domains.
In our PurgeIems above, the "DateTime.Now()..." expression
represents an entire testing domain around time as it relates
to processing data purges. Without that condition under test
control, without it being a test condition, the problem is
impractical to test. Once under test control the number of potential
test cases expands enormously. Coverage grows.

