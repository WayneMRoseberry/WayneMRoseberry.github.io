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
