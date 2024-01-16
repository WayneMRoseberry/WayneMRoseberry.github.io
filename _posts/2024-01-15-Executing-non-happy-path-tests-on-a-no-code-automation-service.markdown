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
