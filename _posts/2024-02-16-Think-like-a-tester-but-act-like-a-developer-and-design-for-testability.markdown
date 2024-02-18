...rough notes for now...
DataMaker was presenting random examples of output.
The random examples were in a list inside a div.
They had no ids on them.
Self-healing tools like ContextQA were trying to match on a fallback list of element locator types that would ultimately fail.
Likewise, it was emitting a graphic in SVG format showing the schema nodes.
The elements in the graphic had ids that were generic - "node1", "node2" - and likewise, the tool was picking fragile fallback constructs, as well as relationships to objects based on the SVG object hierarchy, which was subject to change.

What I wanted instead was an id property on each item.
Self-healing fallback logic tends to prefer id properties because they more precisely and succinctly locate the object.
Even when things like HTML or SVG hierarchies change, the IDs can be robust.

The question is how to create the ids? What name to give them?
Because their placement inside the HTML is dictated by a graphic layout engine that builds the SVG, and the relationship of objects does not echo the relationship of the data the graphic depicts.

For the two types of objects I chose two approaches:
randome examples: a simple numbered item approach - "Randomexample1", "Randomexample2", etc.
schema graphic: I recreated the schema hierarchy and assigned the DOT node id that value. I likewise created an id for edges that wash "source.destination"
This made the identity of the nodes based on the actual data structure, something important to testing business logic.
It meant that these objects could in any relationship at all as far as the visual layout language is concerned and still retain the MEANING of that object in relation to the schema data.

I will flesh this out later.

Why is the application hard to test?
==========================================
I wrote <a href="https://waynemroseberry.github.io/2024/02/16/Think-like-a-tester-but-act-like-a-developer-and-design-for-testability.html">
an article recently</a> about a testing session I ran with
<a href="https://contextqa.com/">ContextQA</a>. One of the key points
in the article was that the website I used to do the testing was
difficult to automate against. It had testability issues.

In this article I describe a redesign of that application and the impact
that had on the automation.

You have to apply some design thinking to make an application more testable.
===========================================
It is not as if an application is automatically easy to test, and it
is not as if one can change the way the code behaves with no thought
and instantly have something useful for testing. Testability
is similar to other application requirements in that it takes planning
and understanding of the user and the problem they are trying to solve.

Can you control it? Can you see it?
--------------------------------------------
There are two important needs for automation, control and observation. The
script needs to be able to manipulate and control the system under test. It also
needs to see what it is doing, record information about it which can be check,
analyzed, or used as the basis for decisions and actions within the script. We
have problems when the script is unable to control the application, or is
unable to use what it can observe for analysis or decision making. In the
case of UI our problem is usually being unable to accurately find the
objects we want to control and observe because the information available
in the UI is not related to the model of the application the automated
script is working from.

You want to make the infomration in the UI markup match the model of the application state.
--------------------------------------------
This is sometimes difficult because the language used to design the UI is
about layout, visual aesthetics, and channeling behavior between controls
and elements in response to input devices like the mouse or the keyboard. There
is a gap between the data model used to describe the UI and a model which
describes the data and business logic underlying the system coded into
the automated script.

What we want to do is bridge that gap by carrying information about the
model the tester needs (and by extension the automated script) into the
UI markup. There are three main considerations to think about this:
- what is the underlying data or state model?
- what is the purpose of the test?
- what does the UI offer us to present that information?

In the case of my website there were two main problems.
==============================================
Both of the problems on my website where instances of elements
on the page the automated script needed to observe and control, but
which were not easy to locate reliably. They were in data that changed
as the user selected different options in the UI, and where the layout
was imposing logic that was different than the underlying data structures.

Random text output
-----------------------------------------------
The first problem was with the random data examples list
that updates when the user selects which type of data to show from
the schema definition selection list.

![screenshot of DataMaker page showing that the random examples list items were missing id on their span elements](/assets/datamaker_randommissingid.png)

The items are displayed inside a `<div>` tag, each enclosed in `<span>` tags.
They have no id on them. In order to find them, one must either try to
locate them based on their text value which will change every time the
user ask for a new set of random values, or based on a pathing strategy
from the parent `<div>`. This works initially, but if the layout is changed
then the path relationship changes as well. For example, I changed the page
at one point to use a table inside the div with every value inside the second
column of a row. The flat list path strategy constructed by the recorder in
ContextQA could no longer find the element.

Visual schema diagram
-----------------------------------------------
super duper wanna be a trooper

![screenshot of DataMaker page showing that the nodes and edges in the schema diagram are a flat list with generic ids "node#" and "edge#"](/assets/datamaker_schemadiagrammissingid.png)


