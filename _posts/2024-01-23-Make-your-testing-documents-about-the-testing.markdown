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

Rewriting the spec
=========================================================
Sometimes a tester will repeat content that is already described in
product documentation, designs, specifications or source code.
Small bits of such content are useful when the re-write
is about testing implications, but when no such testing
information is included it is a waste of tester time and
energy. It also gives the impression to teammates that testing
is a vacuous nothing, that there is nothing to it other
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
> PersonalizedSettings property bag in their account on the service.

Keeping the boilerplate
=========================================================

Making the apology for categories of quality
=========================================================

