Sometimes the testing problem is the answer
==================================
![A cartoon of arctic puffins contemplating the problems of sea lions in the water](/assets/puffinsandsealions.jpg)
We make a lot of decisions in any software project, and
sometimes those decisions are guided by principles that
seem to compete with each other. Respond quickly
versus release when ready. Work in small changes versus
work to understand the bigger picture. Break down
silos versus leverage specialized expertise. Own your
own quality versus get an impartial analysis. Build fast
tests versus build tests that find critical problems. Get
feedback as early as possible versus don't exhaust the
users with broken product. Validating product
behavior matches requirements versus seeking problems
that threaten product value.

We can deal with competing guidelines effectively
if we ignore them (temporarily) and focus on the problem.
This is especially true for testing problems, which have
particular characteristics that should help us make decisions
and decide our course of action, even when dealing with
guidelines that compete with each other.

A working example of a test problem
==================================
This one came from my time testing Microsoft SharePoint
Online, part of the initial Office 365 offering. I would state
the test problem as follows:

> _How can we know and discover if there are bugs synchronizing user identity
> data across the various points where that data is cached
> in the system when properties like the User Principal Name
> (UPN) are changed?_

This example statement has the following characteristics that it
shares with all testing problems:
- It is about risk to product value
- It is about knowledge and concerns itself with a possible gap in what we know
- It is implicitly about observation, bugs that we have seen happen and can confirm exist
- It is targeted at a set of behaviors and conditions which we want to understand
- It is asking for or seeking a methodology or means to gain knowledge, it suggests action

The problem statement, in this case, is also guideline neutral. It describes
only something we want to know, the kinds of risks we are worried about, and
seeks a way to learn what we want to know. This will prove useful.

Guiding decisions with the testing problem
==================================
I am putting pragmatic how-to up front in this article, and leaving
theoretical discussion until after. A lot of you have work to do, so
let's get on with it.

Short version:
1. Describe the testing problem
2. Assess the problem
3. Detail as needed
4. Pick your solutions

If we follow that set of steps, we are better positioned to
make decisions that fit our needs, and are less likely to
allow assumed guidelines and ideology cause us to
take a less effective plan of action.

I describe the steps below.

Describe the testing problem
----------------------------------
Use the five attributes indicated above. What risk are you concerned about?
What do we not know yet about that risk? Is this a narrowly targeted risk
about specific behaviors, or larger and more open ended? Do we have a
sense of methodology yet, or do need to invent one?

You want a testing problem with enough meat on it you
understand what you are going to work toward. We will use
this problem statement to guide actions and decisions.

Assess the testing problem
----------------------------------
Is the testing problem well articulated? Do we understand what we need
to learn? Does the testing suggest other deeper problems? Does the
testing problem describe a risk that is important to us, knowlege
we deem valuable? What risk do we face by not working on this testing
problem?

The more uncertain we are about decisions we might make, the
more important the assessment. For example, we may regularly apply a guideline
that all product testing must happen quickly via the release
pipeline. If a given testing problem is deemed extremely high
risk to the user or business (e.g. _"How can we discover vulnerabilities to
security attack and exploit in our customer person data 
retrieval and storage pipeline?"_) we may decide our rapid
testing guideline is too confining to apply rigidly to that testing
problem.

Detail the testing problem
----------------------------------
This almost doesn't merit mention as it should be obvious.
Assessment and solutions and decision making get into a chicken
and egg loop rapidly with details on a problem. We may
not know how to assess without more facts. We may not
know where to start on a solution without understanding the
problem better. Understanding that problem better or collecting
more facts likewise need a solution, and may need an assessment
before investing further.

This is a call it as you see it step. Let the situation guide you.
Rule of thumb is if you feel anxious about not understanding
the problem well enough, you are probably correct.

Pick your solution
----------------------------------
Easier said than done, but there is no magic method which
will guarantee whatever solution you choose works well. Some ideas to
consider:
- consider options and alternatives
- consider the solution first, evaluate relative to guidelines later
- a testing solution means observation of the product in action
- assess carefully how much you want/need to know, and whether the solution ought to provide that
- remember that observation only works for something that exists already
- the risk from knowledge gap is weighed relative to importance of a guideline
- be ready to give way on either the solution or a guideline
- every guideline you drop or ease up on suggests a complementary guideline coming into play

The key part of picking the solution is that everything
is problem centric before applying guidelines. We consider
possible solutions independent of guidelines, with the problem
already assessed at an earlier stage.

It is at this point where the guidelines affect the decision. Maybe
a given solution is inappropriate for some reason. Maybe a risk
behind a problem is so critical we have to think of another
way of working than we normally do. Maybe when we consider a given
guideline against a proposed solution it helps us imagine another
way of solving the same problem that fits the guideline better.

Testing Problem Characteristics
==================================
Let's look at these shared testing problem characteristics more closely.
Taken in their entirety, they differentiate from problem statements regarding
product design, implementation, knowing if a product is right for the market
or customer needs. Different kinds of problem statements intersect on
attributes, but not 100%.

Risk to product value
----------------------------------
Risk to product value is broad, and an open-ended, infinite possibility
concern that sits opposite the fixed set of product behaviors we intend
to bring to the users. Are there ways the product might fail, confuse the
user, damage data or property, create risk to user interests, increase
costs to the business, diminish the reputation or value of the business
brand? In practice, testers adopt the guiding principle that there are
attributes and behaviors in the product which threaten value, and
that what we want to do is discover them. Testing problems focus on,
frequently start with, an underlying assumption of risk.

Risk to product value flips two other problem statements categories "Does the product meet requirements?"
and "Is this the right product?" around. The inverse of both questions become
a kind of threat to product value. We ask "In what ways does the product fail to meet requirements?" and
"What might make this the wrong product for the market?" instead. Inversion
of those two problem statements makes both of them a small subset
of the much larger categories of things which might threaten product value.
Confirmatory tests which check if a given condition is true (e.g. "After changing
the file type on the image, is the saved file structure and extension
consistent with the selected format?") are really intended to notify us when
the condition is false - when risk to product value has been identified.

Knowledge and the gap in knowledge
----------------------------------
We have a testing problem because there is something we do not
know. If we know something the testing problem is erased. Knowledge
and risk are intertwined. Not knowing is a form of risk. We address
risk partly by learning something. Knowing clarifies the specifics
about risk.

Knowledge and belief are complementary, but different problems. Product
requirements and designs are about belief. We believe that a given set of requirements,
if satisfied, will produce value for the users or business. We believe
that the design we have created, and the implementation of that design
are going to satisfy the product requirements. We do not intrinsically
know either of those things one way or the other. The thing is that
designing and building product cannot be frozen from lack of knowledge.
There is a chicken and egg relationship between believing what you are
making will solve a problem and knowing that it really does. The designers
and developers and other creators have to move forward on belief. That
faith may be informed by other things they know, but remains only
faith so long as we are still making the product.

Testing is not complete until we know something. We know that a given
threat to value exists. We know that five hundred repetitions of the
test procedure yielded an aborting memory leak 2% of the time. We know
that the web page renders differently on Firefox than on Chrome.

Testing infinitely contemplates what we do not know yet. We do not
know yet at workload throughput level will operations begin to fail in
some manner. We do not know yet whether some of the product features
are unavailable to accessibility tools. We do not know what percentage
of users successfully follow the guided instructions in the help wizard
for the new formatting features. We don't know what risks worry us
about the newly acquired project code because we haven't done a survey of it.

Implicitly about observation
----------------------------------
All testing is an act of observation. It is an empirical act. If there was no observation
then a test did not happen. Observation via testing complements our
other belief based activities of design and enumerating requirements by delivering
facts, truth, and knowledge.

Observation affects the problem in a large way. Something must exist
or happen to be observed. Our method must be capable of 
detecting a given thing for us to observe it. Observations are samples
from moments in time that may or may not repeat in the same way. Different things
have different probabilities of being observed, so a great
deal of observation (testing) is about manipulating test conditions
to put observational probabilities in our favor. Observation
requires an expense of time and effort, both of which themselves
are a cost and a risk in their own way. Observation always comes at
some degree of precision, accuracy, and understanding. Observation
unavoidably intersects with perception and our own need for
sense making. When we test, we are adopting methods and practices and
techniques to manage these affects of observation in our favor.

Targeted to a set of behaviors and conditions
----------------------------------
Testing is unavoidably about choices. Any moment of observation offers
an infinity of pieces of information to collect, questions to consider,
something to learn. When we record an observation, manipulate some test
condition, we are making choices.

We like to make some of those choices up front. We may not know enough
to do that, so maybe the test problem is "What are the key risks and concerns
we can draft for this product and how can we best collect what we need to
know to describe them?" Our test problem might be very broad, "How can we
discover if the user stories conflict with each other?", or it might be very
specific "Does the personalized content cache have problems purging and repopulating
entries when under high volume workload of a large population of users with
lots of customizations and frequent updates to the customized data stream?" (another
real testing problem I had to deal with when I was on the Content Management
team in SharePoint).

The targeting helps us on the technical side designing the testing approach,
pre-requisites, executing the cases. Relevant to this discussion, it helps
us understand the assessment. Some behaviors may considered a lower priority
or low risk problem, some might worry the product team a great deal. Some targeting
also aids in the "choose another solution" cycle that occurs often enough
when some high priority guideline conflicts with a suggested solution.

Asking for a solution, demands action
----------------------------------
The crux of our whole endeavor is to act on decisions. A problem does
not start with a solution (_"We are writing an automated test suite that runs
in the cloud on virtual machines that emulate gaming devices. What problem
do we need to solve?"_ as backward as that sounds, it is oddly familiar to
how people discuss testing). We understand the problem first and
craft the solution to address it.

Guidelines are similar to solutions. They shape our decisions, but are
not first principles. Our first principles are the problems we are
trying to solve, and the guidelines help us set constraints on our solution.
They help us make decisions. They introduce concerns we must weigh against
problem priorities and solutions we describe.

There is also a matter of sufficiency. The solution is only satisfactory
if it actually addresses the problem. For example, imagine the following
test problem and proposed solution:
> _How can we discover information disclosure and alteration exploit vulnerabilities in the user credit card storage and retrieval
> sub-system?_
>
> __Solution__: Implement a test suite to automate each one of the user stories
> to validate the product meets requirements.

The example is vacuously and obviously bad, ignoring all the ways that a system
which hits every single one of the user story points might have mistakes
in implementation that leave wind open holes in data security. It is exaggerated
for sake of illustration, although sometimes I feel what people offer and
propose as test solutions in general are not that far off. Usually what they
lack is articulation of any test problem at all, leaving us empty
and malnourished from lack of proper solutions.

Let's grind some axes on ideology
==================================
If you read this far, you probably concluded that while I wrote this
article as a high level how to, it seems there is an argument happening
as well. You would be right.

There exist a lot of arguments, some heated, about different practices
for testing and development. Most of these practices are applications of
guidelines grounded in ideologies. I see ideologies as organized collections
of heuristics, philosophical toolsets which we may pick up and utilize
when needed, and then put away when a better tool suits our needs.

Some of us treat ideologies as singular fixed statements about truth and
good and bad that are always right and never to be violated. I reject that
approach to ideologies, but I leave my argument for a different venue. What
I will say is I believe when we put the ideology first and foremost we
sometimes wind up making bad decisions, even when the ideology is otherwise pretty sound.

_Some ideologies seem to have no redeeming value at all. I don't want my
tool metaphor to confuse anybody I consider ideologies on equal footing
when it comes to effectiveness and moral ground._

I am not going to enumerate any competing ideologies here any more
than examples of guidelines I stated already. What I really wanted to
do with this article is describe an approach to solving problems and
making decisions that puts ideologies in a more proper place. Use them as tools
which helps us make decisions and not overpowering rules that define
the parameters and constraints for all behavior. I believe one way
of doing better at that is to put the problem first, assess the problem,
consider solutions, and then apply our guidelines.
