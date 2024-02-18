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

How is the application hard to test
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
unable to use what it can observe for analysis or decision making.
