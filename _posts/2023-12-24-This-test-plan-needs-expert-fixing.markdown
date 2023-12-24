If you find this test plan acceptable, you need expert help,
===========================================================
![An image of a test plan commonly posted as an example on LinkedIn - a link to pdf will follow later](/assets/dreadfultestplan.jpg)

- a thorough review and critique of that test plan template for sign-in/sign-up that
apears on LinkedIn all the time

In this article, I am going to behave as if I were in a leadership position
in a company where somebody handed me the test plan displayed
at the top of this article. I am going to review the document
as if the company I work for was delivering the product
referred to in this plan.

An version of the same <a href="/assets/dreadfultestplan.pdf">test plan in  PDF format
can be found in this link</a>.

Test Plan Review, step by step
============================================================
__Nit: Title block should have the name of the feature.__

This is not a big deal, but when someone is reading lots of material,
it helps when the title of the document says what it is about.

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

Acceptance testing needs some more details. Who defines the acceptance tests, the
people doing the tests, the product owner, the stakeholders, the end users,
someone else? Who is executing the acceptance tests and on what cadence/time
in the schedule?

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

Regression testing: Given priority of scenario, It makes sense that a feature like sign-up/sign-in should
have some kind of regression testing associated with it. What I am not sure
of is what this label implies. Does the team need to make some special investment?
Does it suggest something that his to go on the schedule? How large is the regression
risk with this feature, and what does that suggest for ongoing team costs/investments?

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

Test Deliverables
-----------------------------------------------------
Provide "how to find" information for each of these. For example, "Test Cases", provide
an address to the repository where the cases for this feature are stored so that some
can find them and review them. Likewise for the other deliverables.

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

