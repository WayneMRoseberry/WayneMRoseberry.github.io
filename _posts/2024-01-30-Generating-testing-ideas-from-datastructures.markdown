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
called `SchemaDef` points to a graph of different kinds of `SchemaObject'
types, several of which (`ChoiceSchemaObject, OptionalSchemaObject, and SequenceSchemaObject`) 
might also point to other `SchemaObject` types. There
is specific type, `ReferenceSchemaObject` that can name another
`SchemaDef` object, with self-referential name permitted. This creates the possibility
of recursion and looping. I created a diagram of just these kinds of
objects:

![a diagram of the recursive schema object relationships](/assets/schemaobjects_recursive_DOT.png)
