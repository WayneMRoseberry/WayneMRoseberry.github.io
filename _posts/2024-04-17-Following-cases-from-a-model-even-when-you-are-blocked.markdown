Sometimes You Have to Change the Plan
=====================================

![A stick figure cartoon of a someone contemplating a collapsed rope bridge thinking about changing their plan](/assets/perhapschangetheplan.png)

Sometimes we use tools that make test cases for us. Often they help us work out complicated
or tedious or lengthy concepts in a simpler way, and from that help us build a set
of tests to execute. Sometimes they even help make the test case steps for us.

This forces us into considering our paths carefully. On the one hand, the tools did
help us come up with combinations of ideas, inputs, actions, and sequences that we
might not have thought of before, might not have stumbled on during our exploration.
On the other hand, following steps in rote fashion can narrow our exploration, or
push us toward repitition that leads to no new discoveries. If we are cavalier about
dismissing the step creation the tool affords us we might skip something important.
If we rigidly follow the steps, we might miss something or waste time with
our myopic focus.

> Even if you are using a formal, scripted test case step method, you should be prepared
> to tactically and strategically make adjustments to your testing and steps. It is common
> that testing may be impeded, blocked, expose new ideas, or demand you look
> beyond the simple rote requirements to notice and discover more.

In this article, I describe a testing session with Test Case Studio, a test step
recording tool. It is a handy way to capture test cases live, and to provide an accounting
of what you did during testing. I decided to test the test tool by building a suite
of tests targeting my own sample web app, and using <a href="https://www.compass-testservices.com/">Test Compass</a> to construct
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
The methodology was to repeat the following for each of the test cases in the suite. The example steps
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

> Failures can sometimes be bad enough that further testing is blocked. It is worth looking
> for a workaround, but at some point it may not be worth the effort, or the nature of the
> workaround alters the test state too much. It is a careful judgement call whether to keep
> trying, or to report the issue and move on.

Used the content of SELECT element and NUMERIC spinner elements as the name of the element
----------------------------------------------
When Test Case Studio recorded an action on either a SELECT or NUMERIC input control, it
would put the content of the control as the title of the element in the step instead of
something more reliable, such as it's ID. For example, on the numeric control in my web
page, Test Case Studio would display __"Enter '4' into '5'"__ even though the ID of the control
was `#numberofexamples`.

An important part about this particular issue is that this behavior is possibly
intentional. Whether or not it is a bug is a matter of opinion on my part. I find it confusing.
If I were following steps blindly, I would have had this behavior handed to me "Make sure the
steps says...<thing>" and I probably wouldn't record this bug at all. I am intentionally being
non-rigid about expectations because I want to force myself out of test case rote
execution myopia. I want things to look weird to me even if they might have been intentional.

> Give yourself lots of room for noticing something that does not look correct. If you have
> documented expectations, use them, but intentionally force yourself to look at
> other behavior and ask yourself questions such as "How would the affect the user?" Some
> written requirements feel very different when you see them in action.

"Back" button was not consistently recorded
----------------------------------------------
In all of the instances recordedin the session for this article, Test Case Studio would not
record clicking the "BACK" button. For example, in this sequences from the Methodology example
above, step 7 has the user click the "BACK" button:
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

The recorded steps by Test Case Studio omitted that step, which should have come immediately after "Click on 'Save'" below:
```
Open website
Click on "schemaexamplesbaseobjecttypesnetworktypes"
select "baseobjecttypes" from "schemaexamplesbaseobjecttypesnetworktypes"
Click on "htmlElement"
Click on "htmlElement"
Click on "Edit"
Click on "schemadefjson"
Click on "schemadefjson"
Enter "{ "SchemaName": "alphalowwer"
Click on "Save"
Click on "htmlElement"
Click on "schemaexamplesbaseobjecttypesnetworktypes"
```

I don't know if the "BACK" button is supposed to be recorded or not, but from
the perspective of being able to describe what I did, the steps recorded above are nonsense. The last
"Click on 'schemaexamplesbaseobjecttypesnetworktypes'" is not possible if the
user did not click on "BACK" just after "SAVE". I am working on an implicit expectation, that
being that whatever Test Case Studio records could be followed by someone later and
get the same result. Perhaps this could be expressed as a formal expectation, but
I am also treating this as more of an exploratory expression on the rote execution.

> Implicit expectations, or expectations that cut across all usage of a feature can
> be a powerful tool in your set of oracles and heuristics. Some of these are very general, and
> you have to use your imagination to realize how they apply in that specific situation.

The names of the select items kept growing
----------------------------------------------
It took me a while to notice this, because I didn't find the resize column control in Test Case Studio
until later. The names of the select items kept getting longer as my testing went longer. In the first test case,
the name that Test Case Studio ascribed to the schema definition control was short:
`Click on "schemaexamplesbaseobjecttypesnetworktypes"`
By the second test case, it had grown longer
`Click on "defaultschemadefaultchoiceschemaexamplereferenceschemaexamplesequenceschemaexamplerangealphaschemaex"`

I did not explore this behavior in-depth. I don't know if it keeps appending the name it generated onto the last name
it has for the control, or what. I am also not sure if stopping/starting Test Case Studio affects this behavior. I
suspect it might have to do with me running a case for a while, saving the session, telling test case studio to
delete the steps and then I would start over with another case.

This one is odd, because it is very possible that the test cases are affecting each other. Would this happen were
the exact same test cases run on entirely different machines, perhaps automated, or each case given to
a different tester? It is quite possible that none of these cases on their own would find this bug.

> Tests can sometimes affect each other, and sometimes that is a useful side effect for noticing problems
> you would not notice where the tests cleanly isolated. Believe it or not, there is sometimes
> a benefit to a sloppy workbench.

Ambiguous name "Click on 'Edit'" assigned to click on multiple items
----------------------------------------------
This came from me trying to stabilize the browser crashes. I would ALT+TAB to Excel, ALT+TAB  back
to DataMaker, and then I would click somewhere on the page. I didn't really know if that would change
anything with regard to the crash, but I wanted to get through the case somehow.

The point is, much like a physics experiment, I was altering the test conditions. Test Case Studio was
recording those extra clicks, as it should have, and that led me to discover a bug I was not at
all looking for and which the tests were not at all meant to expose.

The tests were built from a model that was only concerned with the interactive controls on the page.
The white space and surrounding HTML doesn't do anything within the app under test, so I did not
mention them in the model.

At one point, I clicked inside large whitespace in the table cell surrounding the "Edit" button.
Test Case Studio recorded that as `Click on "Edit"`, the same way it described clicking on the edit
button itself. The XPath and CSS selectors were different (that is what drew my attention), but
still, the name of that step was strange. It didn't seem a good choice for clicking in the space
surrounding that button. This is a matter of opinion - I am not sure what Test Case Studio ought to
call the step, but I don't like the ambiguity on the step name.

> Do not be afraid to deviate from your steps. There is proably more value in trying something
> different than there is in strict adherence to the steps. You tend to see something nobody
> would have noticed when you change the steps up a bit.

Sometimes the number control value change was capture as a double click
----------------------------------------------
I chose to elaborate on the steps as described and change up the ways I was changing the
numeric control. All the steps from the model say is `Change Number(YES)`, without indicating
what to change the number to or whether to use the spinner or type in a number. I decided to
do a bit of both.

I found that if I used the spinner to do more than a +1/-1 that sometimes Test Case Studio
would record `Double-click on "5"` instead of `Enter "5" into "5"`. For a while I thought this
was happening when I would cross over the original value while incrementing or decrementing
the control value, but I also saw it for setting other numbers. I tried to keep my click speeds
slow to make sure I was not doing actual double-clicks, but Test Case Studio still captured
it as a double click.

> Testing steps are sometimes ambiguous. You could formally lock them down in the step
> instructions, although it is sometimes more useful and more efficient to execute the
> steps with variation. Use the ambiguity to your testing advantage. Doing exactly the same
> thing every time you test is sometimes a symptom of growing numb and blinding yourself
> to what is happening.

Wrapping up this story
=============================================
I did not plan for this article to be about adapting rote test case execution
and changing the steps as you go, and now that I think about it I realize
I am doing the same thing here. Especially in testing, where some of the most important
insights we can offer the team are about something we nearly missed, being able to
adapt and change in response to conditions and our instincts is often the crucial
factor that makes the difference between spotting a really bad problem everyone
else missed, or letting something dreadful cruise by unnoticed.
