
The page under test has two select elements and two spans of text.
The first select element has a list of schema namespaces. The
second select element has a list of schema definitions which
change whenever the selected namespace in the first select element
is changed. The two spans of text below are populated with data
whenever a schema definition from the second select element is selected.
The name of the selected schema definition should appear as part of
the text in the first text span.

The flow in Does QA does the following:
1. Opens the page https://datamakerjs-f3b6b7d13de0.herokuapp.com/
2. Sets a variable called __selected_namespace__ to a string value
3. Sets a variable called __selected_schemadef__ to a string value
4. Selects __selected_namespace__ from the `namespacelist` select element
5. Selects __selected_schemadef__ from the `schemalist` select element

Steps 2 and 3 in the flow branch based on different values, allowing an
easy mixing and matching of values for the script. The workflow as implemented
has four combinations:

```
// baseobjecttypes is the default selected namespace in the UI
selected_namespace = networktypes, selected_schemadef = url
selected_namespace = networktypes, selected_schemadef = 8charalphanumeric // this is an invalid combination
selected_namespace = baseobjecttypes, selected_schemadef = url // this is an invalid combination
selected_namespace = baseobjecttypes, selected_schemadef = 8charalphanumeric
```

With these combinations there are two valid settings for the select lists,
and two invalid. The expectation is that the branches/permutations of the flow
would fail on step 5 when attempting to select the __selected_schemadef__ value
from the `schemalist` select element.

