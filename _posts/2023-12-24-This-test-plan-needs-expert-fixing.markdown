If you find this test plan acceptable, you need expert help.
===========================================================
![An image of a test plan commonly posted as an example on LinkedIn - a link to pdf will follow later](/assets/dreadfultestplan.jpg)

In this article, I am going to behave as if I were in a leadership position
in a company where somebody handed me the test plan displayed
at the top of this article. I am going to review the document
as if the company I work for was delivering the product
referred to in this plan.

Throughout this review, I inject quoted, italicized statements that start with the phrase "_Review Principle:_"
which identify how I was thinking about that section of the plan as I
read it.

A version of the same <a href="/assets/dreadfultestplan.pdf">test plan in  PDF format
can be found in this link</a>.

Test Plan Review, step by step
============================================================
__Nit: Title block should have the name of the feature.__

This is not a big deal, but when someone is reading lots of material,
it helps when the title of the document says what it is about.

>_Review Principle: Treat the test plan like a map for those who need to learn more._

__Features to be Tested__, and __Features not to be Tested__
-------------------------------------------------------------
I am very worried about this short list of features.
- __Why not create multiple accounts__? Isn't that what sign-up is all about? Doesn't
signing up new accounts drive the primary business use case? Isn't signing in and
out as different users what distinguishes things like access to data, information storage,
privilege levels? Creating multiple accounts strikes me as a primary use case
for sign-up, as is the case for sign-in of multiple accounts.
- __Why not edit account information__? This at least deserves explantion,
because sign-up is on part of the account information path, it is usually the way
the first, default information gets to the account. Account information is also
where some systems store sign-in information, such as user name, and usually password.
There are very likely important sign-in and sign-up user flows around the edit account experience that
should be checked.
- __Why not other feature integration__? Account sign-up and sign-in sometimes
need to happen in sync with other features. Some features are about on-boarding a new user
via invites, item assignment, external feature offerings, links to resources
inside service, and they almost always need to either make an account or at least
sign-in to their account on the way through. Attaching authentication is sometimes
on a service by service/page by page basis, which suggests at least a quick check
against the different features/pages/forms in the system.
- __User roles and privilege__? While the sign-up/sign-in features are about
authentication, not authorization, they are part of the workflow where authorization
is initially assigned both on account creation, and often as part of mechanism how
roles are carried with the account (depends on implementation, but lots of web-apps
carry claims inside the user authentication token to save on backend hits later). Investigate
integration with this feature.
- __What of account backend system__? Sign-up and sign-in are intricately related
whatever account service is in place. There is no mention of that on this plan, and
sometimes those systems require more than casual usage to check what you need to know
to test.

>_Review Principle: Assume the list is incomplete and look for evidence that demonstrates it._
>
>_Review Principle: Use combinations of features and concepts as a heuristic for missing material._
>
>_Review Principle: Attempt to negate the "not covered" block with relevant risks._

Test Levels and Test Types
-----------------------------------------------------------------
__Test Levels__

The test levels only mention System and Acceptance. What of integration, 
component, and unit testing? Is that involved in the strategy at all,
or is it out of scope for some reason? If covered elsewhere, by someone
else, indicate as much. System testing is heavyweight and expensive, so
it deserves how risk has been analyzed relative to other testing that might
happen at lower levels of abstraction so the system testing strategy
can focus on the kinds of whole-system problems it is best for.

>_Review Principle: Assume the list is incomplete and look for evidence that demonstrates it._

Acceptance testing needs some more details. Who defines the acceptance tests, the
people doing the tests, the product owner, the stakeholders, the end users,
someone else? Who is executing the acceptance tests and on what cadence/time
in the schedule?

_>Review Principle: Look for statements that suggest something must happen but do
not offer details. Present examples of details you want to see and why._
_>Review Principle: Look for requirements that might require spending money, allocating
time and resources, scheduling time, or other logistics concerns, and clarify the
need for more information._


__Test Types__

I raised issues above about integration testing missing.

Security and sign-in/sign-up are important. The sign-in/sign-up features are the primary
way of security the system. You want to test for ways that a user can bypass
sign-in, identities and priveleges faked, mocked, confused, or ignored.

User flows and sequences are important. Is that a subset of the functionality
coverage? I am not sure what is intended.

No mention of browser, or user environment compatibility testing. Why?
Are you relying on end users to cover the rest, or do you believe
we can cover everything in one test configuration?

Is there any concern over international coverage? Is the interface localized?
Do we have a market across different countries/regions where language
and other settings change?

>_Review Principle: Assume the list is incomplete and look for evidence that demonstrates it._
>
>_Review Principle: Use combinations of features and concepts as a heuristic for missing material._

Usability testing seems strange to me for this plan. My knowledge of usability
testing is a scientific measurement of the ability of an end user to complete
a task with the producct. Do you mean something else? Perhaps following use
cases, user stories, and user scenarios to see if you find bugs while
following them? Whatever the case, if you truly meant Usability Testing in
the formal definition of the word, this plan does not support it, as that
requires development of a study protocol, sometimes lab resources, an invite
of test subjects to observe or at least some means of observing them live. I
would expect some ask of resources and some mention of work to support that
were it the case.

>_Review Principle: Look for something that seems a strange fit and ask for clarification._
>
>_Review Principle: Sometimes material seems or doesn't make sense because of terminology
>that has not been clarified. Ask for clarification, perhaps offering your understanding of
>the term._

Regression testing: Given priority of scenario, It makes sense that a feature like sign-up/sign-in should
have some kind of regression testing associated with it. What I am not sure
of is what this label implies. Does the team need to make some special investment?
Does it suggest something that his to go on the schedule? How large is the regression
risk with this feature, and what does that suggest for ongoing team costs/investments?

_>Review Principle: Look for statements that suggest something must happen but do
not offer details. Present examples of details you want to see and why._
_>Review Principle: Look for requirements that might require spending money, allocating
time and resources, scheduling time, or other logistics concerns, and clarify the
need for more information._

Exit Criteria and Suspension Criteria
-----------------------------------------------------
The conditions "no functional bugs" and "remaining bugs have low severity" are inconsistent
with each other.

What does "Suspension Criteria" mean? Stop ship? Stop acceptance of builds into test?
Stop testing? If the latter, then "Critical bugs are open, and they are blocking testing"
seems important to clarify. For testing, the statement is a tautology and not worth mention.
For build acceptance and stop ship, it seems redundant with what someone would put
on an exit criteria. Likewise "All remaining test cases are blocked by an open bug."

This whole criteria section feels either self-contradictory in some parts and
redundant with common sense in other parts. It is difficult to believe the criteria
as stated is what the team will actually do. Something more specific to this feature, and
more realistic to the decision the team is committed to make is what needs to be here. Either that,
or cut the section.

>_Review Principle: Look for statements that seem always true in all cases and do not indicate anything different about this plan. Advise removal._
>
>_Review Principle: Look for ommissions of information that is specific to this plan, particularly in boilerplate sections that tend toward meaningless jargon and boilerplate._

Test Deliverables
-----------------------------------------------------
Provide "how to find" information for each of these. For example, "Test Cases", provide
an address to the repository where the cases for this feature are stored so that some
can find them and review them. Likewise for the other deliverables.

>_Review Principle: Treat the test plan like a map for those who need to learn more._

Test Environment
-----------------------------------------------------
If the feedback from above regarding other testing levels and testing types
are addressed, then there ought to be some discussion here about how
test environment implications from that analysis play out.

Sometimes this is about spending money or building something, or answering
"since we want tests on (_this environment_), but don't have that here, our
plan to cover that is...". Sometimes preparing for that coverage requires some
lead time to line up resources, schedules, etc. Sometimes we say we know we cannot cover something,
and intend to take a risk - this is one of those places where we highlight that.

>_Review Principle: Assume the list is incomplete and look for evidence that demonstrates it._
>
>_Review Principles: Look for something that suggests purchasing, scheduling, acquisition of resources and equipment. Ask for clarification._

Estimation, Staffing and Training
------------------------------------------------------
The numbers in these sections seem inconsistent to me. There is nothing
I believe in this section, given what was written above.

There are 16 hours of TestZephyr training on a schedule that
allocates 8 hours of testing on the feature. TestZephyr is a tool
that ought to apply across many of our projects, why are we putting
that schedule estimate here? If this is the one time we use it,
16 hours for the training given the amount of testing on the schedule
seems ridiculous.

There are 2 testers assigned to interactive testing, and 3 SDETs assigned to the automation.
Five testers are assigned to 8 hours of testing. That makes no sense at all,
and it leads me to believe the estimate is very wrong.

This section needs to be re-written after prior feedback
is addressed and a more complete understanding of intended activity
is described in the document. The estimates in this section need to
be self-consistent.

>_Review Principles: Look for calculations and quantities, especially on estimates of any sort, and check if they are consistent with the rest of the plan._
>
>_Review Principles: Check if quantities and estimates seem reasonable and justified to business need._

Assumptions
------------------------------------------------------
The statement about assuming the developers will deliver the code related to the
features is not useful. That assumption is a given in every development
project, and there are no distinguishing decisions or changes in plan
we would make that we need to describe here. I advise removing it.

"License for TestZephyr" seems odd to make as an assumption. Why
don't we have the license? Why are we stating we use
TestZephyr if the license is possibly in question? The reason
for TestZephyr is seeming very suspicious now. No reason has been
put forward why we need it, what it gives us. It seems a strange
ask for a feature specific test plan.

Neither of these assumptions seem worth mentioning. If there are
no feature specific assumptions, no plan specific assumptions, then
leave this section out.

>_Review Principles: Look for statements regarding needs in time, money, effort that are not justified in the plan._

Risks
------------------------------------------------------
Slipped feature delivery, vacations, bugs affecting time
frame are generic risks that are not interesting to mention
in a specific feature plan. The "Risk Mitigations" in this
section are likewise not useful in consideration of this plan.

"QA Environment Is Down" seems more interesting, and seems to deserve more
than saying "Risk Transfer." Does the team have a contingency plan
for QA environment down? Is there an alternate mode to test, and what
risk comes with shifting to that mode (slower? cannot hit certain
coverage? more expensive?)? It is really interesting to say something like
"_If the QA environment is down, we do have local installs as fallback, it just
means that testing XYZ has to wait until the environment is up again,_" or even
to say "_If the QA environment is down, we have no fallback because <reason>._"

Sometimes a feature really has no specific risks of its own, but often
there is something that really is distinct about that feature. Specific dependencies,
decisions that are in flux, tiers of priority which the teams use as
ways of managing risk - and sometimes testing is the mechanism used to
answer those big risk questions and drive large fork in the road decisions.

>_Review Principle: Look for vacuous statements that seem to add no value to the plan._

Test References
------------------------------------------------------
Like the test deliverables, include instructions to find the specific
references like directories, repository locations, document library addresses,
query filters. The test plan is a map for people to understand the testing.

>_Review Principle: Treat the test plan like a map for those who need to learn more._

Thinking about the review principles
======================================================
I repeat the review principles mentioned above here in a single list where it is easier to
look at them all at once. This is not an exhaustive list. It describes instead
what I was thinking of when I created the per item feedback on the test plan.

- Treat the test plan like a map for those who need to learn more.
- Assume the list is incomplete and look for evidence that demonstrates it.
- Use combinations of features and concepts as a heuristic for spotting missing material.
- Attempt to negate any "not going to do it" material with relevant risks.
- Assume the list is incomplete and look for evidence that demonstrates it.
- Look for statements that suggest something must happen but do not offer details. Present examples of details you want to see and why.
- Look for requirements that might require spending money, allocating time and resources, scheduling time, or other logistics concerns, and clarify the need for more information.
- Look for statements that seem always true in all cases and do not indicate anything different about this plan. Advise removal.
- Look for ommissions of information that is specific to this plan, particularly in boilerplate sections that tend toward meaningless jargon and boilerplate.
- Look for calculations and quantities, especially on estimates of any sort, and check if they are consistent with the rest of the plan.
- Check if quantities and estimates seem reasonable and justified to business need.
- Look for statements regarding needs in time, money, effort that are not justified in the plan.
- Look for vacuous statements that seem to add no value to the plan.

I made a joke post the other day that the most difficult part of reviewing
test plans and specifications is keeping from falling asleep. That joke
is serious, because I find many people are lulled into a stupor when
reading test documentation. The test plan template I used in this article
is one constantly portrayed as a helpful guide to test planning along with
advertisement for various testing tools and training services. That it
passess off as an example of neat and tidy plan I think demonstrates how
effective it is at shutting down critical thinking.

I hope that a list of review principles can help shake someone out of that
hypnotic sleep session to actually question what the test plan really communicates.

I also hope that somebody reading this article, seeing this list, might appreciate
how much critique and review needs to go into someone's test plan and strategy. There
is a lot of surface level testing fluff in public discourse, and evidence people
have no idea what there is to know. This article is for a simple (one page, for crying out
loud!) test plan for a deceptively simple feature, and I believe after having gone
through this exercise the feature deserves far more than the throw away piece of work
the plan indicates.
