Sometimes all you need is some time and a list
===========================================
![AI generated photo depiction of a person holding a list of testing ideas](/assets/testingideaslist.jpg)
_The AI generated image above seems to be directed at an audience
who speaks some obscure northern European language. I have no idea
what the words on that test idea list are supposed to be. Although the
last idea, 'Test' is amusingly obvious._

Short version, if you want to make better test lists, increase
the following:
- Time: for dedicated to test ideas, time between creating and the thinking
- Distance: in time and conceptually from the product code
- Questions: ask more, even if you think you know the answers
- Barrier dropping: if you think "don't bother" on a test idea, write it down anyway

The tests we come up with when we are in the middle
of creating something and what we come up with later,
after we have some time to think about it, can vary
quite a lot. Time creates distance, but so does
ignoring inclinations to discount ideas, consideration
of generic ideas you might consider irrelevant, as well
as asking questions that you think you know the answer
to. That distance helps with the testing.

I gave it some time
===========================================
I started a coding project yesterday. I came back to it this
morning and decided to write up some test ideas. I am very
much a believer that a key barrier to getting test ideas
is lack of dedicated time. I find I need to feel as if there is nothing
else I am going to spend my time on than the testing. If I
am doing anything else, if I feel that I need to get
back to some other task, at all, if I am trying to split my
time, my ideas are impeded. 

I dedicated a small bit of time this morning to just coming 
up with a list and nothing more. I captured
my state of mind as I did so. I will describe the project and the
feature I wish to test along with what I thought as I came
up with the tests.

I made a list, but let's talk about the feature so the test list makes sense.

The feature - a test data generator
=============================================
This is one my pet projects I return to every couple of
years when I want to try doing something new with it. Sometimes
I use it to learn a new language, sometimes a new coding
paradigm. This time it was for material to write testing
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
	StaticValue, // the string value of the Value element is returned
	Choice, // the return value is randomly selected from an array of choices in the Value element
	Optional, // randomly return either an evaluation of Value or string.Empty
	ElementList, // the Value element represents a list of SchemaElement objections
	RangeNumeric // randomly return a numeric value between MinValue and MaxValue, inclusive
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
>   - perfectly distributed? (seems should not be)
>   - ordered? (seems should not be)
>   - predictable? (seems should not be)
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
but I chose to as a way of organizing my thoughts. I am also
trying to break my code familiarity with a touch of formality to add
a little bit of psychological distance from
what I know about the code. I don't know if that works..._
> - single choice

_The first deviation away from the unit tests, a single item
in a choice. When I wrote the code, my mind was conflating one
and two items as being the same problem, hence just a check for two.
I came up with this case as a product of years of tester methodology.
Whenever we are talking about quantities of things, there is nothing,
one thing, two things, and then at least more than two. Oh, that reminds me,
when I came up with the list for this article, I forgot to include "empty
choice list." Rather than fix the list, I am going to keep that omission
here in the article as a demonstration on how time and reflection are
sometimes needed to correct our mistaken assumptions._
> - double choice
> - n choices

_These two are the same as in the unit test suite. Redundant? Perhaps, but I
try not to remove ideas from the list until after I have
completed it and I am deciding what to do. Especially when I wrote the
code, the inclination to heed 'don't bother..' is powerful. As a practice
I write all ideas that come to mind._
> - choice of different element types -> each of one

_When I wrote the unit tests, __ElementType.Choice__ was the only other
element type other than __ElementType.StaticValue__. I didn't write a unit
test for this idea because the concept did not exist yet. It is very easy when
crafting unit tests to forget to consider how a new behavior affects
prior behaviors. Intrinsic to this problem is that __SchemaElement__ objects can
contain elements of different type than their own. This suggests that for every new
element type added there might be an affect on generation of other types which
contain that new type in their __Value__ property. This particular test idea is
one that stays open for more possibilities for every new element type added at least._
> - choice of recursive item
> - choice of resursion with non-terminating loop

_At this point in the feature development, "recursive item" isn't possible. Every item
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
to be SURE to bother. It is very easy to believe generalized solutions cover
all cases only to discover they do not. Careful observers will realize that
"invalid item" is similar to "element type" in the way it ripples in
combination with generating other elements that may be the
parent of the invalid element._

_Another important point is that "invalid item" is not defined in my list. I am
leaving this point open for more examination. So far there is only one
schema element type that has any validity checks in the code, but as a tester
I probably want to search for more._
> - choice of non-existent reference

_Another idea that came up when considering recursion. At this
point it is only a guess that such a problem even exists._
> - is every choice eventually picked -> is random range truly respected

_This one is a repeat of the method  __GetRandomExample_choicefourvalues__, but
I kept it here to consider other possibilities. It also inspired the following
questions._
> - how "random" is the distribution?
> 
>   - perfectly distributed? (seems should not be)
>   - ordered? (seems should not be)
>   - predictable (seems should not be)

_While we could imagine some kind of unit test to explore the
above questions, the various ways of effectively testing start to
create a problem for unit test constraints. Notions of proper
random distribution are hard to succinctly identify. Is a given
set of values sufficiently random, or is there some pattern that
is too predictable? That is the sort of result that requires time
and tradeoffs and analysis, and unit tests are meant for
definitive PASS or FAIL assessments. This type of problem lends
itself really well to exploratory testing. Wow, exploratory
testing of a product API! If one pays much attention to articles
and posts online about API testing, one would think that exploratory
testing is impossible via automated scripts, and yet here we are, looking
at an API with exactly that kind of problem._
> - load -> can we generate over and over without failure

_I almost didn't write this one down, the 'don't bother' reflex
was so strong. I wrote the code, and do not believe, at this point,
that there is any issue where a load test is going to discover
problems that one at a time targeted testing will not. From a history
of coding and testing viewpoint it is reckless to blindly heed
such a belief without at least examining the problem. Mistaken assumptions
are the heart of all problems, but especially those pertaining to
the product under load._

_I wrote the above paragraph several hours ago, and coming back to it
I realize two errors in my 'don't bother' reasoning. The first
error is that since the feature is all about building a response,
there are going to be lost of string building actions responding
to a request, and depending on the system those kinds of actions
can create a highly fragmented memory space - especially if running
on a server that keeps everything long term in the same process. The
second point is that future versions of this API may work with IO to fetch
and store schema from storage, which would likely be impacted by
load. Not that I started with "wrote the above... several hours ago" and
how that demonstrates the impact of time on test thinking._

What is in a list?
===========================================
A list is a simple, but powerful tool. So simple we sometimes
may not think of it as a tool. In this case, I used the list
to help me think of more ideas.

Different lists for different uses
-------------------------------------------
I already had a list I built as I wrote the code. I used TDD, writing
tests first, one at a time, then writing the code, seeing it fail
and then changing the code until the test passed. I added each test
as I added new behavior to the code, only writing the test I needed to
check that one change. When I was done, I had a list. A list of
testing ideas implemented as scripted checks.

My formal title for the list would be "unit tests for (_feature name_)," or
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
of anticipated changes? Isn't "we find nothing bad doing (_this_)"
just an other requirement? It is, but it may not describe a change.
For example, "can run 100,000 times in same session without failure" does not tell
you what the change to the code might be (properly freeing some resource in an edge case,
caching items for re-use, using a distributed cache for scalable
performance, etc.), but instead describes an activity, a test
with a good probability of surfacing even unanticipated problems. The problems
you find doing that test tend to indicate the specific requirements
you might not have realized existed, and certainly did not know
were not being met.

The implications of the list - lead with the test problem
-------------------------------------------
In another article, I describe how I prefer to deal with
testing logistics and strategy questions by 
<a href="https://waynemroseberry.github.io/2023/11/29/Leading-with-the-test-problem.html">
leading with the test problem</a>. Rather than asking
"how much automation do we need?" or "should developers do the
testing, or should someone else?", I prefer to let the testing
problems we are thinking about naturally guide the answers.

From even this short, 10 minute list, I see a variety
of things to think about regarding method and strategy:
- some of these easily describe requirements and would be easy unit tests
- some of these suggest at least a few hours of uninterrupted exploration,
testing, and analysis of the results
- some of these (load tests) suggest investment in framework, data set
creation, and specialized testing scripts or tools

Not all of that fits nicely in a "test it as you write it" or in a
"run it during the CI/CD suite" strategy. Some of it means time
separate from writing code, taking more time than might
fit in a fast "check everything in by end of day" cycle. Some
of it is better to wait until more features are completed so
their interactions can be tested together.

That is okay. Our job is to figure out how to do a job
well, not how to fit into dogmatic notions of how to
divide up work.

A list from someone else
===========================================
I presented the same problem to another tester. I wanted to see
if I got ideas I had not thought of on my own and if that
would lead to more ways to think of a list of test ideas.

I asked them to limit themselves to ten minutes producing test ideas.

Here is what they produced.

This one is from Nicholas.
--------------------------------------------
Before Nicholas started coming up with test ideas, he had some reactions
to the problem itself. He captured those thoughts, and I include
them here because it helps us see his state of mind before coming
up with test ideas.

>So it looks like the logic here is really defined in the comments on the ElementTypes...
>and GetRandomExample is what implements that logic.
>
>I'm curious why min and max values aren't defined as int or float or
>similar since the only ElementType that references them treats them as numeric boundaries
>
>And in designing these checks massively depends on what
>"value" can represent, since it's defined as "object" it could literally be anything
>
>And whatever implementation is used to satisfy "Choice"
>will depend on the different objects that the value can be
>
>well nevermind you did say Array...
>
>So that limits it somewhat
>
>OK I think I'm ready I'll start the timer

I am glad he captured this, because his questions and thoughts are
interesting to observe. When I made my list, I didn't have many
questions to ask myself because I started assuming I knew everything
(although I found a few where I was not sure what I wanted).

Questions tend to be the starting point of creative possibilities. Answers
sometimes open more possibilities, and they sometimes remove them.
If we start thinking of our tests with answers assumed, sometimes we
miss something interesting to test. Above, Nicholas intuits the
possible issues which might arise with Value, MinValue and MaxValue being
of type "Object", especially in combination with settings like "NumericRange".

The list then follows

> 1. Array lengths - shortest, very long, empty
> 2. Other properties in schema being short/long/empty/null should not affect it (or should?)
> 3. Security requirements (not a security expert) - does it protect against injection
> 4. Checking the implementation of "random" by running it repeatedly and making sure it returns different results
> 5. Checking that the randomness will not consistently skip certain locations within the array
> 6. Checking different formats of elements in the array, they could be strings, arrays, lists, ints, custom objects
> 7. How do you convert from the objects in the array to string format - tests around those
> 8. The arrays are in a list - use different list lengths, 0, 1, many
> 9. Arrays of different object types within the same list
> 10. How does it combine the end value when there are multiple elements in the list
> 11. Is the randomness consistent across multiple elements in the list

There is overlap with my list, but also some new ideas:
- Nicholas hits some cases on array values and lengths I did not cover.
- He raises a question about security which, right now is irrelevant, but if future
implementations ever use a database (I have a feature in mind which requires storage) it becomes relevant. I didn't
even consider security in my tests, maybe from not putting time into it, or maybe
because I was subconsciously filtering it out knowing the current implementation is stateless.
- He covers different data types (formats) on the Value attributes, recognizing possibility of problems representing them as string. This is an interesting point, because without intentionally writing tests to find this, I did have bugs in my code related to the way the ToString() function behaves when a data type does not have an explicit behavior for ToString().

Time, Distance, Questions, Drop the Barriers
====================================================
My observation is that as I went from writing the code myself,
to thinking about the tests a day later, to asking someone else
to think of tests, the number of new test ideas grew.

In sync with that progression, the variables of Time, Distance,
Question asking, and Dropping Barriers (to ideas) increased as
well. I feel this is the takeaway from the exercise. If you want
to increase test ideas, work on increasing these variables.
