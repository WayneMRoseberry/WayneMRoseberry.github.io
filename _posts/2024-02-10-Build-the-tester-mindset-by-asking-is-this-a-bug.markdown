
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

The expectations are met in three of the cases, (networktypes, url), (basojecttypes,url),
and (baseobjecttypes, 8charalphanumeric), which result in pass, fail, pass, as we
would expect. The case (networktypes, 8charalphanumeric) which should fail
when DoesQA tries to select "8charalphanumeric" from the select element
`schemalist` returns a pass instead. When we examine the final screenshot of the
run, we see that the value "querystring" has been selected in the `itemlist`
element instead of "8charalphanumeric." This is likewise refected in the
text span below it in the leftmost table column, where the json of the schema
definition of "querystring" is displayed and not "8charalphanumeric."

Why does the combination (networktypes, 8charalphanumeric) present a false
negative, a passing result, while its invalid option counterpart, (basojecttypes,url),
does not? The reason is because when "baseobjecttypes" is the default
selected namespace in the `namespaceslist` select element. Because of this,
the value "8charalphanumieric" is present by default in the `schemalist` selection element. From
the start of the script, the value "8charalphanumeric" is available to select, while the value
"url" is not.

What is happening?
====================================================
As a tester, particularly one not on the project and with no access to the source
code, I can only guess. The only evidence I have is from symptoms, things I can
observe about the application behavior when the test method is executed. The evidence
we have:

1. DoesQA is told to select a value and it selects something else, but does not report that to us
2. The value it is told to select had to be available prior to WHEN it makes the selection in order to get the false negative outcome

This suggests a possible race condition where DoesQA attempts to select a value from
`schemalist` before the element `schemalist` is populated based on the `onChange()` event
of the `namespace()` element. But it also suggests a possible stale state behavior in
DoesQA where it is not updating its view/understanding of the page state before taking 
action. There are other possibilities.

When testing we want to describe the symptoms and necessary conditions to get to the
symptoms as completely as possible. Diagnosing exact behavior, tracing to root cause
is a different activity. It is not a bad activity to do at test time, but one does need to have the code available
to them to do it.
