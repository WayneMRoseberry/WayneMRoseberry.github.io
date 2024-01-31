In my test data generation tool, there are notions of optional
objects and choice objects, both of which rely on a random decision,
essentially a coin toss, to determine whether or which object
will be used in generation of the final output. For example, a
file name might have an optional extension(e.g. ".txt"), or a url might
have a set of choices for a top level domain (e.g. ".com", ".org", ".gov", ".edo").

There is also such thing as a schema reference object, which
identifies a schema object by name, allowing one to build
data objects by re-using previously described schema. For example,
an email name might consist of `<username>@<domain>`, where
both `username` and `domain` have had their schema described already.

One of the problems to worry about are schema structures that
create infinite loops, schema reference objects that eventually
point back to themselves. Reference loops are supported to
allow things like domains, which build up arbitrarily long
sequences something like this `domainname => <name>[.<domainname>`.
So long as the self-reference is a descendent of an option
or choice of objects there is an escape that can stop the loop.
The problem is when a branch exists from which there is no
escape.

We want to build protection into the app... but which choices introduce flake?
=====================================================
We want the tool to detect and avoid infinite loop conditions.
There are options:

1. Detect infinite loop as the schema is built

   This requires that when one creates a reference node in a schema
   definition they also have access to all the previously defined schema
   objects to allow for inspection of looping conditions at authoring
   time. This creates a tight coupling to whatever component stores
   the schema defitions, as well as a performance cost at schema authoring
   time that grows as schema definitions graphs get larger.
2. Detect infinite loop on call to data generation, but before beginning to generate

   Data generation already requires access to a schema defnition
   store to resolve schema references, so there is no increased integration
   complexity. The problem will come in an up front cost to evaluate every
   branch of the schema prior to generating data rather than only having to look
   at the branches chosen during generation.
5. Detect infinite loop during data generation

   This option is possibly lower performance, as the need to detect infinite
   loop only arises in the case where the algorithm decides to evaluate a
   branch which has a schema reference in it. The problem with this
   solution is it now introduces a flaky behavior where sometimes a call to
   the same getRandomExample method will detect an infinite loop and sometimes it will
   not, even when given the same schema definition.

The flaky data case - an optional reference to an existing infinite loop pair
---------------------------------------------------------
This is a case that would behave in a flaky manner if we chose to detect infinite loops
during data generation instead of ahead of time.

First, let's consider an escapable loop. Let's imagine a case where
a self reference to an existing schema object is a descendant of an optional
schema object. The fact that the path to the looping reference is optional
means there is a chance to escape if during one of the recursions through
that path the option comes up NO.

 ![image of a schema with an optional object whose child is a reference to the same schema definition](/assets/escapableloop.png)

Let's imagine now a different schema relationship where there is a separate
infinite loop defined between two other schema objects, with no return
back to the original schema. The chance to hit the option that allows escape
is no longer available once we have taken this branch.
![image of a schema with an optional reference to an infinite loop in a separate schema pair](/assets/indirectreftoinfiniteloop.png)

_It occured to me after creating the image and writing this passage that
the external loop does not need to be two schema items. A single external
item looping back to itself would suffice._

This design introduces a flaky condition in the product because the identification
of the infinite loop only happens when an inescapable return to a prior schema name
is evaluated, but that evaluation only happens if the random decision to take
the optional path came up YES. Assuming an even coin flip decision on the option, the
schema in our example is going to fail with an infinite loop error 50% of the time.
Imagine, though, that instead of being one choice below the schema root
the reference was four deep. Assuming an even coin toss on every decision, the
probability of hitting the infinite loop is no 0.5 * 0.5 * 0.5 * 0.5, or or 0.06, a
failure rate of 6% without changing the application inputs at all.

![Diagram of a schema with a reference four choices below the schema root before pointing to an external infinite loop](/assets/fourdeepoption.png)

With proper design, this product flake rate could be made 100% deterministic under
test (see <a href="https://waynemroseberry.github.io/2024/01/29/Caught-myself-in-a-fragile-unit-test-bad-habit.html">
prior article</a> where I describe how I achieve inversion of control via dependency
injection of the code that makes decisions). But a dilemma arises because even if the test
can invoke this failure condition on demand, a design decision to defer flake detection
to when the data is being generated forces us to accept that inconsistent behavior (as
per a dice roll) is by design. The end user will sometimes see a failure, and sometimes
not, and so long as we commit to this design, there is nothing which will change that.

