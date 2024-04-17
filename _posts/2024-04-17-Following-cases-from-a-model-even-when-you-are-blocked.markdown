Sometimes we use tools that make test cases for us. Often they help us work out complicated
or tedious or lengthy concepts in a simpler way, and from that helps us build a set
of tests to execute. Sometimes they even help make the test case steps for us.

This forces us into considering our paths carefully. On the one hand, the tools did
help us come up with combinations of ideas, inputs, actions, and sequences that we
might not have thought of before, might not have stumbled on during our exploration.
On the other hand, followin steps in rote fashion can narrow our exploration, or
push us toward repitition that leads to no new discoveries. If we are cavalier about
dismissing the step creation the tool affords us we might skip something important.
If we rigidly follow the steps, we might miss something or waste time with
our myopic focus.

In this article, I describe a testing session with Test Case Studio, a test step
recording tool. It is a handy way to capture test cases live, and to provide an accounting
of what you did during testing. I decided to test the test tool by building a suite
of tests targeting my own sampel web app, and using Test Compass to construct
a suite of test cases built by an all edges traversal of my sample web application.

Test Suite Creation from a Model
==============================================
Many testing tools allow us to create large lists of possible test ideas
through simple description of a test problem, and then give us strategies for reducing
the test cases down to meaningful sets.

Test Compass let's us build state diagrams (although sometimes it feels to
me more like a flow chart of decision models), and then use graph traversal
strategies to derive cases from the different paths through the graph. Below is one
such model for my DataMaker sample web application:

![Model showing navigation through the edit page and back of my sample web application](/assets/testcasestudiorecordings/DataMaker%20Edit_Save%20sequence%20two%20times%20depth.png)

A full traversal of this model generates thousands of test cases, many of which 
likely test the same thing. Test Compass offers multiple strategies for test case creation.

- __All Nodes__: create enough cases to hit every node in the graph
- __All Edges__: cover every edge in the graph, which corresponds to hitting every decision branch
- __All multiple edges__: Cover every combination of edges at least once
- __All paths__: cover every possible sequence through the graph

I describe the model and the different strategies in-depth in <a href="https://www.youtube.com/watch?v=eIGOG7aAqr4">another
video</a> on my <a href="https://www.youtube.com/@SoftwareTesting-bv6di">YouTube channel "Software Testing".</a> With
this specific model, an "All Nodes" traversal produced only 2 test cases, whereas "All Edges" produced 8, and "Multiple Edges" produced
14. I used "All Nodes" in this article.

The Sample Web Page
==============================================
My sample web site, <a href="https://datamakerjs-f3b6b7d13de0.herokuapp.com/">DataMaker</a>, offers a simple UI for editing data schemas and
generating random data derived from those schema. It is backed by a REST API. There are two
pages in the version of the app I used for the test. The first page allows the user to look at
schema and ask for random samples. The second page allows the user to edit schema definitions and
save them to the data store.

Elements in the first page are dynamic, using JavaScript to update selected schema definition views
by calling into the REST API, and then drawing a diagram of the scehma on the web page. The
page is simple, but has enough behavior both static and dynamic to make for an interesting
recording in Test Case Studio.

Testing Methodology
==============================================
The methodology was to repeat the following for each of the test in the suite. The example steps
in the table below are taken from the first test case in the suite, steps varied per case on either
the exact step action or the "YES" and "NO" modifiers.
1.	Start Test Case Studio
2.	Switch to Microsoft Edge
3.	In a new tab, open DataMaker (datamakerjs-f3b6b7d13de0.herokuapp.com)
4.	Execute the steps in the table below (see “Step” and “Description” columns)
5.	Examine recorded steps in Test Case Studio for issues and problems. (save test case studio recorded cases when done)

Step | Description
---|---
1|Change Namespace (YES)
2|Change Schema (NO)
3|Change Number (NO)
4| Edit(YES)
5| Modify(YES)
6|Save(YES)
7|Back(YES)
8|Change Namespace(YES)
9|Change Schema(YES)
10|Change Number(Yes)
11|Edit(NO)

A full list of all the test cases is located in the following spreadsheet: <a href="/assets/DataMaker_Edit_Save_sequence_two_times_depth_Edge_Coverage.xlsx">Data maker test suite</a>.

Testing - what really happened and results
==============================================
I mostly executed the suite rote as described in the methodology section above. There were only 8 cases. Execution took less
than an hour. But I was unable to follow the cases exactly as recorded because a bug in Test Case Studio prevented me from
doing so. I was forced into modifying my steps to try to avoid a crash.

Maybe this is easier if I describe the results:

Test Case Studio crashed coming back from the Edit page if I changed SELECT element selection
----------------------------------------------
The simple sequence would be:
1. do anyting on the main page
2. click the "Edit" button
3. do anything on the Edit page
4. click the "Back" button
5. select a different item from either the "Namespace" or "Schema Definition" dropdowns

This would lock up the browser, and then most of the time would crash the browser session entirely.
Further testing showed that the browser would not (always) crash if I was using ALT+TAB to
check my steps in Excel. I used this mitigation to try to continue coverage, but I found even
that approach unreliable. I also started doing more clicking inside the web page after coming
back from ALT+TAB in Excel, hoping that either the increased interaction or maybe just timing
would stabilize Test Case Studio. I had already captured the repro case on the crash, and wanted
to get past it for the additional coverage. My attempts at mitigation were ineffective and I
decided not to spend any more time on it, so I marked as blocked all test cases where
a "BACK" step was followed by change to either of the SELECT elements.

Test Case Studio uses the content of SELECT element and NUMERIC spinner elements as the name of the element
----------------------------------------------
When Test Case Studio recorded an action on either a SELECT or NUMERIC input control, it
would put the content of the control as the title of the element in the step instead of
something more reliable, such as it's ID. For example, on the numeric control in my web
page, Test Case Studio would display __"Enter '4' into '5'"__ even though the ID of the control
was `#numberofexamples`.


