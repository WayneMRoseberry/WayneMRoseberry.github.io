Testing like a new user
========================================================
![cartoon of a stick figure contemplating instructions from a build it yourself furniture box](/assets/newuser.png)

Sometimes it is useful to test something as if we are a new user,
trying to deal with only what we can gather from the documentation
in front of us.

I recently tried testing a new user scenario for a feature on <a href="https://testrigor.com/">TestRigor</a>, 
an AI-driven test case creation product. I came across a feature that
builds test cases based on end user behavior in the product, and I decided
to take that opportunity to record my experience as a new user as
the basis for a testing exploration. A testing report of the
session <a href="/assets/TestRigor%20Behavior%20Driven%20Test%20Case%20Creation%20First%20Usage%20Test%20Report.pdf">is published here</a>.
You can also watch a video of the testing session <a href="https://www.youtube.com/watch?v=Z29VmC7FiFc">here on my YouTube channel</a>.

A note about TestRigor
=======================================================
I am not an employee of TestRigor. I was not paid by them, or asked by them to
do any of this testing. I performed the testing on my own, wrote a report, and sent it
to Artem Golubev. He contacted me and offered to speak with me. He was cooperative and helpful.
He has read this article, particularly since the modifications I made to the updates
you will find below.

Article Update
=======================================================
I waited a while before sharing this article, because I wanted to share it with Artem Golubev,
founder of Test Rigor. Especially because the testing was blocked before I could
complete my intended plan, I wanted clarification if I was perhaps doing something wrong.

What I learned from talking to Artem was that behavior based case creation
relies on getting a large number of user sessions, and based on that looks for
patterns of similarity in those sessions to build cases from. In my testing, I had
only done 3 sessions, not enough for Test Rigor to build cases from.

Artem also shared with me that behavior driven testing was an early feature
and some of their new features around natrual language driven automated case
creation were finding more interest with customers, so they had invested more
there. Customers do use the behavior driven features, but as you will see below
it does require instrumentation of the application, which is heavier commitment
from a customer and sometimes the people doing testing are not in a position
to drive that change.

> When we test and hit problems, especially when those problems seem to
> block testing, a conversation is vital. Even if we are members of the team,
> small details can confuse us and keep us from understanding.
>
> The conversation too will highlight other problems in context of the
> new understanding.

I kept the rest of the article as an account of my activity, because I
believe the account of investigation techniques are worth sharing.

That does not mean the testing was in waste. In particulare there are a few points
to come of this:

The documentation said nothing about a minimum sample requirement
--------------------------------------------------------
As you will see below, I found no documentation about the feature at
all. I found mentions of it in some of the marketing material, but
otherwise nothing. The feature is easily findable on the navigation menu
when viewing cases, so it was easy to stumble into. The settings page
is easy to follow, the instrumentation was easy to implement. I may have
had improper understanding of the application, but the educational material
(i.e. the text on the settings page) led me to that misunderstanding. I conveyed
that to Artem.

There is no status indicator on how much behavior has been collected
---------------------------------------------------------
Maybe I missed something, but I saw no indication in the settings page, or
elsewhere in the product that could show me how much behavior data had been
collected. Given the behavior driven case creation feature relies on a certain
volume of sessions, and needs to see a certain quantity of patterns emerge from
the data, it seems some kind of indication that something had been received, and
how much would be important. What if the customer had done the instrumentation
wrong? What if the settings were wrong? What if the configuration of the website
was impeding the collection. As observed below, the collections requests were being
blocked when I ran from localhost, which makes sense - but other settings like VPNs,
network configurations, firewalls and such could cause problems. The feature should
have some indication of volume/status so the user can anticipate whether collection
is going well, and to have some prediction of when they could use the feature.

There were other issues I mention below. The two I mention above came
to mind after learning more about the feature behaviors.


__Everything that follows from this point forward was written before
I had my conversation with Artem. I was still operating under a misunderstanding
of how the feature worked, but my intent in this article was to capture
the new user experience, and confusion is part of that experience. From a
testing perspective, I felt it worth preserving this moment for others
interested in testing to reflect on.__

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
>
> Consider the navigation and discovery experience part of your testing activity. How
> easy is it to find. Try to browse and navigate, avoid searching for key words and
> feature names unless you have been exposed to them somehow, as the user is not
> going to know them unless they too were exposed.

![a screenshot of the TestRigor behavior driven case creation settings page](/assets/testrigor_behaviorsettings.png)

I put what I found in the documentation survey in my testing report.
The settings page for the Behavior Drive Case Creation feature was simple
to understand, but it was also the sole explanation of the feature I was able to find.
Trying to use it I had several questions:
- what did all the different settings do?
- what guidance should a user of the feature follow regarding security and privacy, particularly for collecting end user features and proper use of API keys
- where do the recorded sessions go, how do I inspect them?
- how do I troubleshoot failures if I have them

I raised the lack of documentation about the feature as an issue, along with specifics
about settings on the page as separate questions.

Instrumentation of the web application
========================================
TestRigor provides a code excerpt to place in your web page that
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

![A screenshot of the TestRigor behavior driven case creation settings page displaying the No recorded data for application error](/assets/testrigor_norecordeddata.png)

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

```
54	200	HTTPS	datamakerjs-f3b6b7d13de0.herokuapp.com	/api/schemadef/dot?namespace=schemaexamples&schemaname=referenceschemaexample	302		text/plain	msedge:20404			
55	200	HTTPS	recorder.testrigor.com	/v1/events/gs8ztvqy1Bo5xP-gFQB9	0	no-cache, no-store, max-age=0, must-revalidate; Expires: 0		msedge:20404			
56	200	HTTP	Tunnel to	recorder.testrigor.com:443	0			msedge:20404			
57	200	HTTPS	recorder.testrigor.com	/v1/events/gs8ztvqy1Bo5xP-gFQB9	0	no-cache, no-store, max-age=0, must-revalidate; Expires: 0		msedge:20404			
58	200	HTTPS	recorder.testrigor.com	/v1/events/gs8ztvqy1Bo5xP-gFQB9	0	no-cache, no-store, max-age=0, must-revalidate; Expires: 0		msedge:20404			
59	200	HTTP	Tunnel to	www.linkedin.com:443	1,362			msedge:20404			
60	200	HTTPS	recorder.testrigor.com	/v1/events/gs8ztvqy1Bo5xP-gFQB9	0	no-cache, no-store, max-age=0, must-revalidate; Expires: 0		msedge:20404
```

Wrapping up a first time user session
========================================
It is very possible I missed a step or piece of important information while trying
to execute on the first time user scenario. Or it is possible that I hit a bug in the
product. Either way, the information is valuable to the product team, because it
is in the best interests of the product to make the first time user experience easy
and smooth.

The important part of testing a first time user experience is to capture that unfamiliar
experience and try to notice everywhere information might be missing or confusing. But
then you have to complement that with jumping out of new user mode and dig in with
tools to investigate and collect information, for sake of reporting the issue, that
the end user might not do. You have to do this without losing that "new user" perspective.

Final Notes...
========================================
I am adding this closing after having had the conversation with Artem, because
I also want to include the post conversation perspective.

Some states of mind are difficult to capture. One that is especially hard is that
moment where you are unfamiliar with a product and trying to learn how to use it. In my
case, the confusion was clarified by a conversation with the founder. We don't all get to
talk to the founder of the company that made the tools we were using, but perhaps
we have a co-worker, or members of the community of users to help us out, or maybe
we talk to the developer and learn the behavior.

Once we have that conversation, the "new user" perspective is gone. We can try to simulate
it later (e.g. blindly following instructions), but the tendency to fill in gaps is so strong
we easily overlook things. When you find your self in that state, recognize it for the
unique and rare thing that it is and exploit the state of mind.

> Some states of mind and perspectives are brief and short-lived, but may be important.
> Recognize them and record what you observe, experience, believed, and how you got
> there. Report it.

It is tempting to categorize the new user behaviors - the confusion, doing the wrong thing - as
not important, but that is not our job when we are testing. Our job is to capture that
kind of experience and describe it as accurately as we can. I was led to my mistake, my
confusion, by a product that offered no information about how to use it correctly. Part
of the value that moment of confusion brings is telling someone how it came about so that
it can be addressed.
