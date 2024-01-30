Get into the testing mindset by diagramming your data
=========================================================
![Cartoon of a stick figure character looking at a chalkboard with a data diagram on it](/assets/drawyourdata.png)

_I drew a chalkboard instead of a whiteboard in this cartoon because
it feels more lyrical and poetic to me._

Sometimes we can get a better notion of testing ideas by forcing
ourselves to look at the problem through something other than the
code.

I often think of data in terms of shapes. Especially for structures
like trees and graphs. Most graph based data structures have
infinite possible combinations, which we immediately want to reduce
to risky, meaningful, and interesting equivalence classes. The trick
is finding the true boundaries of equivalence. That is where the shapes come in.

At this point, I start drawing. This article is a diary of me doing that this morning.

Let's think about testing this tool I am writing
=====================================================
I am in the middle of writing a test specification for a tool I
am working on. Yesterday I was adding basic functionality and
the tests I created were unit tests, all very confirmatory,
all simple checks.

I wanted to step away from the coding and start considering the problem
from a little bit of distance. Rather than guide the next check
based on my whitebox view of the code, I wanted to consider
the method under test based on its inputs.

I have been working on this document for about two hours, trying
to organize my thinking. It is very rough, doesn't even have test
ideas or test cases described yet. It has some pictures, and some analysis
of data for the inputs. That part is still incomplete, but I thought
it would be interesting to catch the planning process in its rough shape.

Diagramming the problem with pictures
================================================
I created a diagram of the method under test `DataMaker.getRandomExample()`
using the DOT language. The image is a bit dense, but the nice thing
about DOT images is the diagrams are all text based, which makes it easier
to change them and focus on smaller sections. This image serves no other
purpose than to help me think.

![diagram of the system under test](/assets/getRandomExample_DOT.png)

Creating the image took a while as I changed the layout and how to represent
each item in the drawing. How would I group them? How would I keep the image
easy to read? This might seem like "non-work," but it is part of the creation
process. First is that a diagram that is easy to understand helps one think
of ways to use the information the diagram depicts. But maybe even more important,
all that re-structuring of the layout gets me more familiar with the data and
the relationships, and helps stimulate ideas.

Drilling in on the data structure inputs problem with more pictures
=================================================
One of the key aspects of this system is that data input, an object
called `SchemaDef` points to a graph of different kinds of `SchemaObject`
types, several of which (`ChoiceSchemaObject, OptionalSchemaObject, and SequenceSchemaObject`) 
might also point to other `SchemaObject` types. There
is a specific type, `ReferenceSchemaObject` that can name another
`SchemaDef` object, with self-referential name permitted. This creates the possibility
of recursion and looping. I created a diagram of just these kinds of
objects:

![a diagram of the recursive schema object relationships](/assets/schemaobjects_recursive_DOT.png)

Thinking more about these recursive, referential and looping relationships
================================================
At this point, I have started thinking about the types of data structures I
want to try testing. What I am trying to do at this point is understand which
types of objects have different effects on the overall schema definition.

I describe what I believe to be the condition that creates a looping relationship,
as well as what causes, or avoids, an infinite loop. This will guide my test
data designs later.
> __Looping shapes__
>ReferenceSchemaObject my name a SchemaDef that eventually refers back to itself. This creates a loop. The loop must have a terminating alternative branch to avoid infinite looping. The object types that offer >alternate paths are ChoiceSchemaObject and OptionalSchemaObject. 


I also describe different data relationship concepts. With graph and tree
based structures, I like to imagine different kinds of shapes in the data, sort
of like molecular structures that form the basis for all other shapes. That is
what I am doing here. I am imagining that there are at least two classes of structures,
those that terminate, and those that do not. I am also imagining different depth
relationships for referential structures. I have begun writing those out below.
> __Terminating levels__
> 
>(one level relationships) = [Static|Choice|Sequence|Optional|RangeAlpha|RangeNumeric] -> string value
> 
>(two level relationships) = [Choice|Sequence|Optional|Reference] -> (one level relationships)
>
> (three level relationships) = (two level relationships) -> (one level relationships)
>
>__Terminating objects__: StaticSchemaObject, RangeAlphaSchemaObject, RangeNumericSchemaObject, string value
>__Non-terminating objects__: SequenceSchemaObject, OptionalSchemaObject, ChoiceSchemaObject, ReferenceSchemaObject

Notice that there are few complete sentences in any of the material above. The writing style
is optimized for fast thinking. I am relying a lot on the ideas being apparent without
supporting verbage.

Notice the implied test case expansion in the passage above. For example, "_(one level relationships)_" suggests
at least six different test inputs. This set of 6 inputs could be utilized in any of a number of different test
cases, suggest a 6x multiplier for whatever that is. As we go to the next, "_(two level relationships)_", notice
how it borrows "_(one level relationships)_" in combination with 4 types of schema objects, for a total of 24 derived
test inputs. And "_(three level relationships)_" repeats the same trick for 4x24 = 96 test inputs, making a total of 
96 + 24 + 4 test inputs from this "__Terminating Levels__" analysis.

The analysis will continue on in this fashion. Some of the ideas I intend to
analyze are the different options data shapes for array values, different kinds of branching
structures that create variations of terminating and non-terminating graphs (infinite looping)
graphs are interesting because I want the code to detect it and throw an error. Also interesting
are the recursive structures that can terminate, because it is interesting to see if
the algorithm will either flag them as infinite loops and throw, OR find itself needlessly stuck
from a graph that does have an exit.

I think I need to get back to what I was doing
==============================================
As you can see, everything here is in progress, but not at all finished. Think of this
like watching one of those home makeover shows to the halfway point. I hope it is
useful to see this kind of work mid-way through the thought process. When we only see
what things look like when complete we don't always understand how it got there. As you
can see here, there is a lot of rough material, but that works out just fine.

If you are curious, the code is available on my github repository
====================================================
The code for this project is here: <a href="https://github.com/WayneMRoseberry/datatools">https://github.com/WayneMRoseberry/datatools</a>.
I wrote it once already using ASP.NET in C#. I decided to try a version in Node.js
because it seemed simpler, smaller, and I wanted to learn how Node.js. That is located
in the <a href="https://github.com/WayneMRoseberry/datatools/tree/main/datatools.datamakerjs">https://github.com/WayneMRoseberry/datatools/tree/main/datatools.datamakerjs</a>
sub directory.
