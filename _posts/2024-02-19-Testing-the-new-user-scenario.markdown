I recently tried testing a new user scenario for a feature on TestRigor, 
an AI-driven test case creation product. I came across a feature that
builds test cases based on end user behavior in the product, and I decided
to take that opportunity to record my experience as a new user as
the basis for a testing exploration.

Writing the Test Plan
========================================================

> It is almost always better to have a testing plan before you start.
> It does not need to be fancy. Just enough to help you think.

Even for a small testing activity I still like to create a test
plan. I want to think about my approach, intent, goals, and preparation
before I start. For simplicity, I use the same document to
capture my notes as I test. The planning portion is short and
simple with only enough steps to guide my exploration. I liberally
deviate from the plan as I test.

> Introduction
> =====================================
>TestRigor has a feature to derive test cases from end user behaviors. The page author instruments the page with a script block provided by TestRigor, and then from there Test Rigor will build test cases based on what end users do against the page. This plan describes a test approach to test that feature.
>
>  Methodology
> =====================================
> 1.	Create a new suite in a new folder… suite name that makes sense for this investigation and keeping the results distinct from other suites I have already reported to TestRigor team
> 2.	As per the TestRigor Behavior Driven Test Case Creation page, instrument the page
> 3.	Turn on both “Recording of user behavior enabled” and “Allow recording to capture input values”
> 4.	Visit the page (DataMaker (datamakerjs-f3b6b7d13de0.herokuapp.com)) and perform several navigations through the select boxes – in general,
>   a.	Fresh visit from new tab
>   b.	Refresh the page
>   c.	Click an option on the schema definition box (initial state, using default namespace)
>   d.	Click on an item in the random examples box and copy the value or something – I am not sure what will be recorded
>   e.	Change the namespace
>   f.	Change the shema definition
>   i.	Try to cover some with very random content, like “url”
>   g.	Repeat the above various navigation around the page
> 5.	Go back to TestRigor and click “Trigger new test cases lookup”
> 6.	Inspect the cases created, execute cases created
>   a.	See what was recorded, what not
>   b.	See how robust the scripts are – are they flummoxed by the data-driven and random nature of the page?

Documentation review as a new user
========================================
Not mentioned in the plan, I waited until I started testing before I read the product
documentation. I made finding and reading the documentation part of the test
itself, as I felt that was something a new user would likely do.

> Product documentation is an important part of the user experience, and a valuable
> resource when testing for finding issues. Do not assume how features work or behave,
> and instead look for some kind of guidance or information from the product documentation
> materials.

I put what I found in the documentation survey in my testing report.
The settings page for the Behavior Drive Case Creation feature was simple
to understand, but it was also the sole explanation of the feature. Trying to use
it I had several questions:
- what did all the different settings do?
- what guidance should a user of the feature follow regarding security and privacy, particularly for collecting end user features and proper use of API keys
- where do the recorded sessions go, how do I inspect them?
- how do I troubleshoot failures if I have them

I raised the lack of documentation about the feature as an issue, along with specifics
about settings on the page as separate questions.

Instrumentation of the web application
========================================
TestRigor provides a code excerpt to place in your web page for that
will capture end user activity and upload it to the TestRigor recording
services.

> Especially with code samples insertions and guided changes to product
> code, pay attention to how closely one can follow the examples or steps
> exactly as written with no modifications and succeed. The more you force that
> during your testing the less guess work a customer will have to do
> on their own to make up for gaps between the example and reality.
>
> If modification is expected, make sure the documentation makes that clear.

This portion of the test was simple and easy and mostly ran with no issues.
I did notice that there were 403 errors in the developer console when I tested
my instrumented page locally, but that might be expected behavior. The settings
for enabling instrumentation have you indicate the URL of your application. It is
very likely TestRigor is checking that the address matches, which it would not have
done with me running on `localhost`. I still reported the issue more as a question,
in case the errors were of interest.

> Record and report any behaviors that you are unsure of during your testing. The
> behavior might be expected, but sometimes you really are seeing something the
> product team did not intend.

After local check of the page functionality, I pushed changes to
my website on <a href="https://datamakerjs-f3b6b7d13de0.herokuapp.com/">https://datamakerjs-f3b6b7d13de0.herokuapp.com/</a>.
I then navigated the page several times, manipulating
the controls and elements as per the plan. I did not know the limitations of
what TestRigor would be able to capture, and some of the page elements are
nothing more than content. I changed up the plan a little bit, copying content to
the clipboard (does that trigger anything? I have no idea...), varying the
scope and span of what I was selecting. A short bit of time doing that and I
decided to see what TestRigor would do.

> Sometimes we don't know the actual product expectations or requirements.
> We can  use testing as a way of discovering its behaviors, indicate what we
> observed, and use that conversation to get more clarity on requirements.
>
> Sometimes the other members of the product team learn something new because
> you tried something they weren't thinking of. This frequently leads to more
> requirements.

Case Creation, hitting test blockage, and the ensuing investigation
========================================
In TestRigor, I clicked the "Trigger new test cases lookup" button and got an error,
"No recorded data for application." I did not expect this, but I also did not know
if I should immediately expect recordings to be available. Maybe the service
handles them in batches. Maybe there is some processing time on configuration of a
new application before recording is enabled. Maybe something else.

At this point, the documentation issues reported earlier started to feel more important.

> When you hit an issue that seems to provide little information to understand more,
> you should start investigating with more powerful tools and techniques.

I wondered if the failures I saw local were also happening on the server. I repeated
some of my actions on the page with the developer console open. The console was clean,
no errors. To be sure, I started a Fiddler session and captured the traffic between
my client and the TestRigor server. All the requests were coming back with an
HTTP 200 response, so it appeared the recordings were making it to the server.

> Test blockage is useful information. Product team members need to know that somebody
> investigating and exploring application behavior is unable to explore further.
> As frustrating as it might feel, reporting of any class of information is
> a win for testing.

I decided to close my testing at this point, because all further planned activity
depended on the case creation step. I would indicate the blocking issue in my
report along with excerpts from the fiddler logs and my observations from the
developer console.

Wrapping up a first time user session
========================================
It is very possible I missed a step or piece of important information while trying
to execute on the first time user scenario. Or it is possible that I hit a bug in the
product. Either way, the information is valuable to the product team, because it
is in the best interests of the product to make the first time user experience easy
and smooth.

The important part of testing a first time user experience is to capture that unfamiliar
experience and try to notice everywhere information might be missing or confusing. But
then you have to comlement that with jumping out of new user mode and dig in with
tools to investigate and collect information, for sake of reporting the issue, that
the end user might not do. You have to do this without losing that "new user" perspective.
