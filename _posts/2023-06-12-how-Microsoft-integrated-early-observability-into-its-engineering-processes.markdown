![Chimpanzee contemplating a problem](/assets/chimpincontemplation.jpeg)

Can we see problems before our users hit them?

One of the ways many modern products assess, and measure success and quality is through observability. Modern applications, whether they are in the cloud, on mobile devices, on the desktop, or deployed internally on a corporate intranet emit data that track product health, and customer experience. One of the growing disciplines is analyzing this data to measure the customer experience, establish product health, spot regressions or bugs that might have escaped into the marketplace.

One of the things we would like to do is predict when we have a problem in the product before it impacts customers. Can we apply the same analysis we use to spot issues in market to see those problems early? Can we further apply this analysis to assess coverage and gaps in our testing and automation efforts?

This is something we did in Microsoft Office by integrating the product signals, product telemetry data, with our engineering systems and pre-release processes. It was an idea I came up with, and an effort I architected and led and will describe here so others may understand what it entailed.

We wanted an early signal.
=================================
Microsoft Office tracks health of product in use across four “Rings” of users: “Dogfood” who are members of the Office team picking new builds daily for regular use, “Microsoft” employees of the company picking up new builds roughly every week, “Insiders” customers of Office who are participating in early release programs, and “Production” who are the hundreds of millions of regular customers of Office, picking up a new release once a month.

Office tracks on a dashboard health and usage metrics for every ring. Among many measures, there are number of sessions, error rates, crash rates, known issues waiting for fixes in the pipeline. They also track if a given build or version has been stopped from release to further rings.

What we desired was another ring, before Dogfood, to represent the testing from our automation systems. We wanted a ring of synthetic users to provide a signal and find issues before users later in the pipeline did.

![We wanted to add a new ring of synthetic users to our existing release rings of users.](/assets/stagingrings.png)

We had to make some changes in our product and process.
==================================
There were many changes we had to make to the automation system, reporting systems, the telemetry processing pipeline, and the Office product itself. These changes came over a series of many months, involved several people across several groups. In some cases, I made the changes myself, but in many of them I had to rely on other people to make changes in their systems. Like everything, it required a lot of soft-skill leadership, negotiation, and collaboration.

Some of what we had to do would have a similar step for any product. Some of it is specific to Office product and engineering system behaviors. Generally, though, it breaks down as four steps:

![The four steps to the change are: Check, Isolate and Identify, Analyze, and Exploit](/assets/fourstepstochange.png)

Check
==================================
Before you go, you should make sure it is safe to do so. Office already had an enormous investment in its telemetry system, and interrupting its operations would have created problems for product teams using it to make decisions and evaluate releases.

Test data volume from the automation systems before letting the data through.
-----------------------------------
There was an early concern that data coming from our automation runs would overrun the systems that collected telemetry from our customers. Microsoft Office has a large automation system, running millions of tests per day, and the telemetry data storage costs were expensive, so the worry was understandable. Because of this, the team who built Office telemetry code and services put features in place to prevent product telemetry upload during product testing. Before overriding these behaviors, we needed to test the amount of data generated during automation runs, which we did by creating a separate local pipeline. We were able to demonstrate that even with our full arsenal of automation suites running, the flow of data from the existing customers of Office was multiple orders of magnitude larger than what our test systems could possibly generate, and our data centers had more than sufficient capacity.

Once we demonstrated the system could handle the data volume, we had to ensure existing reports that measured customer experience could implement a filter to isolate the synthetic automation traffic from the end user traffic, and then we had to remove blocks that had been put in place to prevent the automation signal from uploading.

Isolate and Identify
===================================
Product observability and signal is all about determining what is happening. Isolating and identifying which events are coming from which groups of users under what conditions is a key part of that analysis. The biggest amount of work for us was properly identifying the Automation traffic in a way that was useful.

Create a new ring for the automation data.
------------------------------------
This involved negotiation with the team that owned the code that stamped the ring identifier on every telemetry row. There was already information available to Office that indicated if it was running on a lab machine. The change was to use this information and set the ring to “Automation”.

Build ring impersonation capabilities into product and engineering system.
------------------------------------
This was a change coming not only from my effort, but also from the team that owned product experimentation features. Like most modern applications, Office can turn behaviors in the product on or off based on which ring the current user belongs to, allowing the product team to turn off a feature when inner rings find issues that would block further deployment. For testing purposes, the experimentation team wanted the ability to tell the automation system to pretend that Office was running as a specific ring, and thus execute with the set of feature flags turned off and on as per that ring. The experimentation team wanted to be able to tell from the telemetry which ring Office was running as. At the same time, we did not want that run showing up as a regular user, we wanted to know it was Automation. I drove in a three-group design negotiation with the team that owned the telemetry data, the experimentation, and of course my own interests. Through that discussion we came up with a way of indicating both pieces of information on every row of telemetry data.

Build test specific metadata into the product code.
------------------------------------
To understand product health as well as quality and coverage of our tests, we needed to know more about the test that was running when the product signal was generated. This would allow to understand what the product does per test, as well as separate test signals based on purpose of the test running. If the product was producing more errors, or crashing more often, we would want to easily know which run or script was making that happen. For that I created a specific product event initialized on Office client code boot which was only recorded when the product was running in the lab and which reported test information about the purpose of the run, the configuration the test was running under, and the identity of the specific test suite.

Prepare test machines with telemetry configuration.
------------------------------------
There are several Office settings which affect product telemetry behavior. Most important are the user permission settings, as international regulations require that products provide an opt-in model so only information the user gives permission to collect is sent. There are also per ring rule settings updated on product boot that dictate which telemetry events are collected. We modified our test suites as if the end user had selected the most verbose, most permissive information collection policies, as well as hard-coded the rule settings (by specifying a non-existent URL for the endpoint which provides the updated rules) and placed on the machine rules normally only used during testing.

Something else we added to our test code was an after-test event that called into the product code to flush whatever was in the telemetry cache when the application exited. Normally telemetry events are uploaded every five minutes, with the next Office session uploading whatever events remain in the cache from the last session. There was no “next session” during automated testing, so we had to induce the upload ourselves.

Analyze
====================================
Once the data can be identified and isolated, you need to make meaningful sense of it. Office already had systems and processes for using that data. What we wanted to do was augment it with our new data to apply the same analysis in ways uniquely useful for test workloads.

Add test metadata analysis and pivots into the telemetry reporting systems.
------------------------------------
There is a whole team in Office that owns services which process and analyze the telemetry data once it is uploaded to Microsoft. There is a data warehouse built on a SQL Reporting Server cube, a web front end to reports built from that cube, a set of Azure Data Explorer accessible tables for people to build their own queries against data aggregates and trends, as well as a anomaly detection service from which product teams can define metrics which they want tracked and bugs automatically filed when the AD service determines a new trend is emerging. For the automation and test data to be useful, we needed to add pivots and filters from which to aggregate results in this solution. This allowed us to produce reports of emerging crashes generated from our automation runs, isolate which product functional points were hit during which test runs or show per test run failure rate trend build over build. I negotiated with the owners of this service over which pivots and filters I we needed, and they did the implementation. If anybody has done OLAP cube design in the past, you know that this takes some planning, as data aggregation costs get very high when the pivots have many distinct values. I needed to pick my priorities carefully and make some sacrifices in places where the value of the precise slices on the data would have been too large for the database.

Exploit
====================================
When you have the information and insights, you need to act and make decisions. Information is useful when it drives change.

Reproduce problems haunting product teams.
------------------------------------
One of the ways we were able to help was to isolate errors reported in the product telemetry to specific tests that yielded that same failure. We are generally unable to contact a user and ask them what they were doing on a given day at a given time, and could they possibly do whatever that was again, perhaps multiple times in slightly different ways, to see if we could capture that error again?

With test metadata uploaded in the events along with all the other product events, we were able to isolate exactly which automated scripts, in which runs, had generated the errors. We could get all other information associated with that run, including detailed product logs. We could launch that same set of tests again, this time under product engineer control, so we could observe the product in action. Several times we had product team engineers unable to reproduce something from production users find the answer in runs coming from our automation system.

Improve existing automation.
------------------------------------
We sometimes want to know if a given feature is covered in our automation. Maybe there are five ways to insert an image in a document, or there is a given configuration setting which we want to know if any of our automation covers it. By looking at the product signal database, we can see if a given event is hit at all, which exact tests ever produce that event. If we establish no tests have hit the event, we can add one designed to target it. If we decide we have multiple tests which seem to do the same thing, we may decide to trim the suite.

Target testing based on product change.
------------------------------------
One of the problems that product engineers face is selecting tests to run when making a product change. Missing a test means possibly overlooking a mistake in the change. Selecting too many irrelevant tests means possibly investigating results and failures not related to the change. You want tests which cover the same functionality and code path near the change. A product engineer can query the telemetry database for interesting events, determine which tests emitted them, and run those tests to cover their change.

Testing deep and complex behaviors.
------------------------------------
I describe this more in-depth in my article about signal-based testing, and it is an exciting way to use tools and automation to find bugs that are otherwise very difficult to find. Normal pass/fail checking helps determine if very targeted, very specific behavior you know how to check is broken or not, but such tests do poorly at finding failures in complex behaviors and in ways you did not anticipate. When you have the product observability signal integrated with your automation system, you can begin creating more complex behaviors where failure information can be mined from the product signal. I believe this is a field of test engineering that is going to grow more in the coming years, especially as AI guided engines make building such tools easier.

In Summary
====================================
There is a lot to gain from tying your observability capabilities and telemetry signal to your automation and testing processes. The key technique is a matter of identity and isolation which enables the analysis and action that get the results you want. I put a good deal of the past several years in doing this for Microsoft Office. I’ve seen other teams do something similar. It is a fun and rewarding way to move your team to the next level.
