I gave it some time
===========================================
I started a coding project yesterday. I cam back to it this
morning and decided to come up with some test ideas. I captured
my state of mind as I did so. I will describe the project and the
feature I wish to test along with what I thought as I came
up with the tests.

I made a list, but let's talk about the feature so it makes sense.

The feature - a test data generator
=============================================
This is one my pet projects I return to every couple of
years when I want to try doing something new with it. Sometimes
I use it to learn a new language, sometimes a new coding
paradigm. This time it was for materials to write testing
articles.

The feature under test returns random values for test
data described by a schema. The method takes a DataSchema
object, which contains a list of SchemaElement objects.

```
/// <summary>
/// Produces a valid random string where the requirements
/// for the data string are described in the DataSchema object
/// passed to the method.
/// </summary>
/// <param name="schema">Describes the requirements for a valid data object.</param>
/// <returns>A random string that is valid according to the schema.</returns>
/// <exception cref="ArgumentException">In the case where any element in schema is invalid.</exception>
public static string GetRandomExample(DataSchema schema)
```

More specifically, the test ideas focus on the case
where an element in the schema is of type Choice, which
means the data generator is offered a set of possible
values to return.

Data types definitions are offered below.
```
public class DataSchema
{
	public string Name;
	internal IList<SchemaElement> Elements;
	public void AddElement(SchemaElement element)
}

public class SchemaElement
{
	public string Name { get; set; }
	public object Value { get; set; }
	public object MinValue { get; set; }
	public object MaxValue { get; set; }
	public ElementType Type { get; set; }
}

public enum ElementType
{
	StaticValue,
	Choice,
	Optional,
	ElementList,
	RangeNumeric
}
```

The list(s)
=============================================
I have two lists. One I made while coding, and
another I made while thinking of the tests. Let's
compare those lists.

The unit test... short list
---------------------------------------------
I use TDD when I am coding, so I was making a list of
unit tests as part of writing the code. It is short.
-  __GetRandomExample_choicetwovalues__: first check I implemented, creates
a data schema with a choice element that has two static values. The
assertion fails if the return value is anything other than the two
possible static values.
-  __GetRandomExample_choicefourvalues__: second check I implemented, with
four static values in the list of choices. The check fails if each of the four static values
are not returned at least once after repeated calls to the GetRandomExample
method.

That is where I stopped. The code met the requirements as I
imagined them at that point for GetRandomExample processing an 
ElementType.Choice SchemaElement type.

Rise and shine, and make a list
---------------------------------------------
Today, I decided to dedicate some time to just test work. I prefer
to start by coming up with test ideas. I used Notepad to make the
list. Sometimes I like simple text editors, pads of paper or other
tools that are lightweight and allow me to put all my thought into
the list instead of the tool.

> GetRandomExample: Choice type
> - single choice
> - double choice
> - n choices
> - choice of different element types -> each of one
> - choice of recursive item
> - choice of resursion with non-terminating loop
> - choice of invalid item (e.g. ranges with crossed min/max values)
> - choice of non-existent reference
> - is every choice eventually picked -> is random range truly respected
> - how "random" is the distribution?
>
>  - perfectly distributed? <seems wrong>
>  - ordered? <seems wrong>
> - predictable <seems wrong>
> - load -> can we generate over and over without failure

I put about 5-10 minutes into the above. Probably less.
I wanted to see if I would come up with anything beyond the unit
tests I created yesterday, but I also wanted to keep
the time thinking up ideas short. I knew I wanted to
use the exercise for this article, and all I needed
were a few differences to make a comparison.

Let's look more closely at that list
---------------------------------------------
Let's talk about each of these options and what I was thinking
at the time I made the list.

> GetRandomExample, Choice type

_I don't really need to label this list, as it is just for me,
but I chose to as a way of organizing my thoughts._
> - single choice

_The first deviation away from the unit tests, a single item
in a choice. When I wrote the code, my mind was conflating one
and two items as being the same problem, hence just a check for two._
> - double choice
> - n choices

_These two are the same as in the unit test suite. Redundant? Perhaps, but I
try not to remove ideas from the list until after I have
completed it and I am deciding what to do. Especially when I wrote the
code, the inclination to heed 'don't bother..' is powerful. As a practice
I write all ideas that come to mind._
> - choice of different element types -> each of one

_When I wrote the unit tests, ElementType.Choice was the only other
element type other than ElementType.StaticValue. I didn't write a unit
test for this idea because the concept did not exist yet._
> - choice of recursive item
> - choice of resursion with non-terminating loop

_At this point in the code, "recursive item" isn't possible. Every item
in the schema is described in place. A new feature has to exist to
introduce this concept. Non-terminating loop came as an extension of
thinking about recursion. As a test problem, this is something I
will hold onto until the code is implemented. Both of these may convert
to a set of requirements ("support named data schema as schema element",
"support recursive named data schema", "detect and throw error for
non-terminating loops"). If they do, that inspires a new set of
testing ideas as the product behavior gets richer._
> - choice of invalid item (e.g. ranges with crossed min/max values)

_This is an idea I got from a unit test I implemented yesterday. One of
the element types has validity checks implemented for it. Realizing that
I decided to add a test where the choice is one of the invalid items. I
had a little bit of a 'don't bother' nag in my head because I know how
the code checks for invalid values and I believe the behavior is
already covered. When I think 'don't bother', I use that as a reason
to be SURE to bother. It is very easy to believed generalized solutions cover
all cases only to discover they do not._

_Another important point is that "invalid item" is not defined in my list. I am
leaving this point open for more examination. So far there is only one
schema element type that has any validity checks in the code, but as a tester
I probably want to search for more._
> - choice of non-existent reference

_Another idea that came up when considering recursion. At this
point it is only a guess that such a problem even exists._
> - is every choice eventually picked -> is random range truly respected

_This one is a repeat of the method  __GetRandomExample_choicefourvalues__, but
I kept it here to consider other possibilities. It also inspired the follow
questions._
> - how "random" is the distribution?
> 
>   - perfectly distributed? <seems wrong>
>   - ordered? <seems wrong>
>   - predictable <seems wrong>

_While we could imagine some kind of unit test to explore the
above questions, the various ways of effectively start to
create a problem for unit test constraints. Notions of proper
random distribution are hard to succinctly identify. Is a given
set of values sufficiently random, or is there some pattern that
is too predictable? That is the sort of result that requires time
and tradeoffs and analysis, and unit tests are meant for
definitive PASS or FAIL assessments._
> - load -> can we generate over and over without failure

_I almost didn't write this one down, the 'don't bother' reflex
was so strong. I wrote the code, and do not believe, at this point,
that there is any issue where a load test is going to discovery
problems that one at a time targeted testing will not. From a history
of coding and testing viewpoint it is reckless to blindly heed
such a belief without at least examining the problem. Mistaken assumptions
are the heart of all problems, but especially those pertaining to
the product under load._

What is in a list?
===========================================
A list is a simple, but powerful tool. So simple we sometimes
may not think of it as a tool. In this case, I used the list
to help me think of more ideas.

I already had a list I built as I wrote the code. I used TDD, writing
tests first, one at a time, then writing the code, seeing it fail
and then changing the code until the test passed. I added each test
as I added new behavior to the code, only writing the test I needed to
check that one change. When I was done, I had a list. A list of
testing ideas implemented as scripted checks.

My formal title for the list would be "unit tests for <feature name>," or
maybe informally "checking that I made the changes I wanted to make." That
is a useful list, but this morning I wanted a different type of list.
I want one I would call "Did I make a mistake?"

Are the two lists different? Yes. The first list assumes a set of
known changes anticipated ahead of time, and you are describing a
set of checks which will fail if you did not implement the change
in the way you expected. The second list assumes there may be
problems in the set of changes, and you are coming up with a 
list of ways to find those problems.

Why isn't that list of ways to find problems the same as a list
of anticipated changes? Isn't "we find nothing bad doing <this>"
just an other requirement? It is, but it may not describe a change.
For example, "can run 100,000 times in same session without failure" does not tell
you what the change to the code might be (properly freeing some resource in an edge case,
caching items for re-use, using a distributed cache for scalable
performance, etc.), but instead describes an activity, a test
with a good probability of surfacing even unanticipated problems.
