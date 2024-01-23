I prefer testing documentation be about testing and only about testing.
The purposes of testing documentation are as follows:
- Help us think about testing we might do
- Support critique of the testing approach
- Reference for test execution, preparation, and reporting
- Communicate and guide testing logistics

There are some testing documentation anti-patterns that I
always ask testers to remove or change in their plans. I ask
them to replace the content with something else that fits
one of the purposes above.

> __Stories from the front lines__
>
> One time, a developer told me "_I always thought the
> purpose of writing the test plan was to check if the tester
> understood how the product works._"
>
> I hid my feelings, but I was angry. To me that
> was an insulting and patronizing view of the tester's
> capacity to act as a skilled member of the team. Nobody
> else is subjected to special homework and quizzes to determine
> if they know what the product is about. But it also
> demonstrated a gross misunderstanding on the part of the
> developer of what testing is about, what a test analysis
> should describe. Topping that off, it suggests that maybe
> none of the testers the developer had worked with ever
> offered anything of substance to talk about.
>
> So many problems to fix.

I am not a tester but I do a lot of my own testing, should I keep reading?
=========================================================
I think maybe yes. Chances are whatever documentation you write is
brief and short. You might share your ideas with others, or the
burden of review might fall entirely on you, although I would advise
sharing anyway to get the benefit of collaboration and feedback.

Either way, the techniques I talk about here are about replacing bulk
with lower volume, value-dense information. When the content is about
the testing and only about the testing you save time writing and
reviewing it. It serves its function more properly.

I also believe "do this instead..." material helps us understand
better what might work for us. Keep on reading, and imagine how whatever
material you prepare for yourself might be affected by the techniques
below.

Rewriting the spec
=========================================================
Sometimes a tester will repeat content that is already described in
product documentation, designs, specifications or source code.
Small bits of such content are useful when the re-write
is about testing implications, but when no such testing
information is included it is a waste of tester time and
energy. It also gives the impression to teammates that testing
is a vacuous act, that there is nothing to it other
than what everybody else already created.


Here are some techniques to make the content testing
relevant

__Try to leave it out entirely.__

Maybe the content offers nothing at all. Just cut it and
see if your specification is any worse without it.

__Use an "(EXCERPT) _(and therefore)_..." pattern.__

Include a description of product behaviors, but after each
detail of meaningful cluster of details, append "_and therefore..."
and say something about the testing. For example:

> The user personalization cache checks for personalization refresh
> based on a timer schedule, _and therefore_ testing will focus on
> actions that fetch from the cache along with actions that affect
> personalizations around triggering of that time job.

__Replace product behavior descriptions with "_What happens when...?_"__

A lot of testing activity is about explorations that seek answers to
questions, about things that happen under different conditions. Imagine
the product specification said something like this:
> User preferences are captured in the app across all platforms and
> persisted both in cookies (or local settings files) and in the user's
> `PersonalizedSettings` property bag in their account on the service.

In the testing specification, you might write in response to that

> __PersonalizedSettings__
>- _What happens when the user personalized settings are different on different devices?_
>- _What happens when the user clears their browser cache?_
>- _What happens when the user makes personalized settings while disconnected?_
>- _What happens when the same personalized setting is across device and platform types, are there any which do not work going from one platform to the next?_

__Write concerns and risk statements__

Testing activity is largely about exploring risk inherent to
the product, project, dependencies, or any other factor
which might threaten product value. In the test documentation, we
can start the discussion of a testing approach with a statement of
concern that the testing approach is meant to explore.

Imagine the following from the product specifcation:
> __Generative AI Integration with Diagnostic Report Data__
> 
>  The report calculation feature will present meaningul and easy to
> understand content by taking the raw output of diagnostic
> measurements, combine the data with prompts fed to generative AI
> and presenting the output to the user in PDF format.

The testing document may respond with the following concerns as areas for
investigation:

> __Report Generative AI Integration Concerns and Risks__
> 
> This document covers strategies for addressing the following risks and concerns
> - LLM response peformance, will the round trip and processing time be adequate
> - LLM model versioning and response drift, will our prompting strategy produce consistent responses across model changes
> - LLM hallucination and unintentional escapes, in particular when processing unstructed diagnostic data

Written in this fashion, the statement functions as a segue for more
specific testing details.

Keeping the boilerplate
=========================================================

Making the apology for categories of quality
=========================================================

