I recently wrote an article about creating a 
<a href="https://waynemroseberry.github.io/assets/DoesQA_selectorcomparison_elementaction_testspec.pdf">testing specification</a?
for covering some not-happy path cases for <a href="https://doesqa.qa">DoesQA</a>, a no-code
test automation service.

Today I executed the testing described in that specification. I sent
a report of my findings to Sam Smith, a founder at DoesQA. In this article, I describe that testing.

The test <a href="https://waynemroseberry.github.io/assets/DoesQA%20Actions%20and%20Selector%20Types%20Testing%20Report.pdf">report is located here</a>.
The raw testing <a href="https://waynemroseberry.github.io/assets/DoesQA%20selector%20testing%20notes%20january%2015%202024.pdf">notes are located here</a>.

My approach
====================================
I treated this as an interactive, end-to-end testing session. My charter
was to cover different kinds of HTML element selection types (IDs, non-ID CSS selectors, XPath, Full XPath, inner text)
against different types of actions in DoesQA (Touch, Set, Select, Check)
against a few different types of HTML elements (SELECT, INPUT, SPAN, repeating, ambiguous, inside TABLES, etc.).

I was simulating a user creating simple control check workflows and
then running them. I wanted to see any problems that came up while using
the different types of selectors, because some of them had been described
previously as non-happy path. I expected to hit some bugs looking in
that direction.

I also wanted to leave room for exploration, so I went for a loosely
structured approach. On the one hand I had a combination of variables
I wanted to cover, but on the other I hand I wanted to catch and
investigate other problems that came up as I went.

A few comments on things I like about DoesQA
===================================
- The designer looks nice, and its graphical tools are easy to use
- The reporting page is presents results nicely with errors inline with results
- Live status on results are nice to see, with the steps and stages represented as a horizontal line
- Per steps screen captures and video are useful
- The designer allows branching of workflows, which run as parallel jobs, and allows for fast creation of testing permutations
- The designer allows defining a collection of actions as a group that may be re-used like canned procedures

Testing Coverage and Method
===================================
I covered approximately 13 combinations, give or take. They all are mostly represented in
a single workflow, enabled by braches in the flow for each combination covered. You can see
that in the image below.

<img src="/assets/doesqa_combinations.png" width="800px"/>

I spent 2.5 hours testing, 2.5 hours preparing the written report.

Summary of Issues
===================================
I reported 14 different issues during testing. The issues are detailed in the test report document. A summarized list of them
follows:
- __Data Loss On Looping Workflow__: If a workflow has a looping sequence, the designer goes into a "Saving..." state it never comes out of, and any subsequent edits to the workflow will be lost, with no warning or prompt, when the user switches away from the screen.
- __Lost Nodes During Edit__: I had some nodes in the designer disappear while editing, but I never got a repro. I suspect it might have been the same problem as the looping workflow described above.
- __Various Node Editing UI Clumsiness__: There are some difficulties around control occlusion, connection editing, connection selection, node selection that are not extreme, but didn't feel smooth either.
- __Confusing errors and incompatibilities between actions and HTML element types__: When the user specifies an action, or a selection type, that does not match the element they are trying to use, or the element state, the resulting error conditions are at least confusing and hard for the user to understand.
- __Various UI issues and problems__: Tab order oddness, ability to give things the same name without UI ability to distinguish, judgement call on the randomly chosen name scheme for runs, and other small problems.

In addition to the issue above, I offered a few feature suggestions. My favorite involves
giving more power to the branching features to allow more re-use of actions across different elements.
