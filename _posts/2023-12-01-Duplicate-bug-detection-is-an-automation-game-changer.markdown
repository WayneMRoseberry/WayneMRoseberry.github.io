Microsoft solved this problem in Office over a decade ago, and so can you
=================================
Office has duplicate failure detection in their automation
runs solved. Not 100% - the problem is a fuzzy one - but
solved to a degree that made working with the automation
suite unlike anything I have seen anybody else talk
about anywhere. When Office solved that problem, everything
changed.

I am going to talk about how, and describe some ways other
people can, with some work, do the same thing.

Duplicate bugs impede going big with automation
=================================
Imagine the following situation. It may seem like a fantasy, but I worked
for over a decade in most of this state, and within the last two three
this state fully realized:
- automation results are sent immediately and automatically to the proper failure owner
- anybody can easily run the automation suite at any time as many times as they want
- the system automatically knows if a given failure has been seen before, from which tests, under what conditions, and which build
- the system has an automatic ability to inform the engineer if their code change is high probability the reason the failure happened
- the system has a running profile of the failure rate of every distinct failure in the system
- suites are run many times a day against the main build, against individual and team branches, for many purposes and many reasons
- the above is executed across the full range of tests from unit, component, integrated system and end to end tests that span clients and services

When I first joined the Office team, they were able to do
handle none of the above at scale. Some of the features to allow
some of it were in place, but a single question impeded the
ability to go big.

_Is this a new failure?_
----------------------------

Not knowing if a failure was new kept test automation
hobbled. Every run had to be vetted and checked and cleared
by somebody, and that somebody was always a tester, before
anybody was allowed to file a bug or ask a developer to
investigate the failure. We were unable to really use
the automation suite to solve big problems, like doing a
quick check of impact after a set of changes, or looking
at failures that followed an update of the OS or compiler, because
every time we did that the failure investigation had to
pass through a small number of people that decided whether
or not the bugs merited investigation.

At this core of this investigation is establishing if a
failure is new or not.

A new failure strongly suggests "_Whatever you changed just now caused this failure._"
A new failure that does not repeat on re-execution suggests (flake) "_This failure has been here for a while, probably before whatever you just changed._"

We want developer time spent either changing product, or fixing
a bug. We want the right developer to fix the right bug, one
in their scope of control, one tied to something they
understand well. We want developers to spend the least
amount of time possible trying to determine if a bug
is a duplicate. We want the failure introduced by someone
else in an earlier change to go to that person instead
of a developer who happened to be standing there when the bomb
went off.

The cost of curating suite run results meant we ran the automation
infrequently. For example, if the team was taking in a new
build of Windows, notification of the event would have to
announce at a test manager meeting of some 40 different people
to let them know an automation suite run was coming, and to
get ready to have someone look at the results. Absorbing a change
of that caliber was a big expensive and infrequent process
with a large lead time, involving a lot of people.

Contrast with the current state where updates from the OS team
come almost ad hoc. A build is rolled, the suite launches, results
are processed, new failures are sent to the team that owns the
piece of code under test. It can all happen without any upfront
notice, you just deal with the issues as they come in.

No failure curator.

And it is only possible because the system will, automatically,
answer the question, "_Is this failure new?_"

Detecting a new failure
=================================
The heart of the first effective version of the solution relies
on this patent: https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/8782609.

Prior to implementing the technology above, the team
used a pattern matching solution. Engineers, almost always
testers, would see a failure and if they believed or knew it
happened more than once they would create a string to
match it (it used an SQL LIKE syntax). For example, one
pattern might be "%Exception%Graphics.Registration.ModuleLoad.initGrpFZZM%"
(_the pattern is fiction I made up right now. The namespace path
in the example, while invented by me, is not far off from the sorts
of odd naming you wind up with a team using Hungarian Notation for
variable and method names_). This hand curated failure
pattern method improved the system ability to
indicate if a failure had been seen before, but individuals
could neither keep up with the rate of failures, and
some individuals would lazily create patterns like "%crash%" that
bucketed all crashes, no matter where from, to the same category.

One of the SDETs in the Office Engineering team (his name is on the patent
above as an inventor. He is a good friend.) decided we the system to
identify duplicate failures without requiring a human to do so. They
preserved the manual tagging mechanism as a default to catch issues
people know about, and the for fallback the system would decide on its
own if the failure had been seen before.

Without going into details, the failure message, call stack and a couple
of other important pieces of information were compared against a database
of previous failures, an edit distance calculated, and the top
result within a certain threshold distance was declared a matching failure.

Since implementation, the task has been about tuning the failure
bucketing system. Sometimes it under buckets, declaring something
new that is not. Sometimes it over buckets, declaring a duplicate
when the failure is new. It is always a range of accuracy that
the team was always fixing in some way or a nother.

Sometime prior to 2017, the Microsoft Research team implemented a
new failure matching solution based on Machine Learning and clustering.
The patent for that solution is located here: https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/9710371.
Microsoft Office switched to the new solution in the years between
2017 and 2019.

Things Office does now based on failure bucketing
====================================
The way Office uses automation now would have been impossible without
automatic duplicate failure detection.

- Ten million tests per day (and climbing): This is an under count, because most of
unit test methods roll up dozens or hundreds in a single assembly, and the system
counts the assembly as one test. End to end tests, of which there are a lot
are properly counted one to one.
- Test reliability runs: This is something I put in place. When you run end
to end tests you get a lot of intermittent results, many of which
come from intermittent failure conditions in the application. A lot of these
failures occur less than 1 time per 1000 iterations of the test. The reliability
runs give us a historical profile of when a failure occured that is closer to
the real introduction point, a measurement of the consistency rate for
all the tests in the critical suites, and a chance for the MACHINES to
be the first witness to an intermittent failure instead of the DEVELOPERS.
Our "developer saw the flake first" flipped from always the developers
to almost always the machines as soon as we started reliability runs.
- Lots of runs by lots of people doing lots of testing: Prior to failure
bucketing, the only people launching automated tests were either
the build team for build evaluation, or testers. After failure bucket,
test launches scaled out across the dozens of product teams, smaller
feature teams, and thousands of individual developers. The duplicate
detection capabilities helped developers focus on the bugs they created
instead of something that was already there when they made the change.
- Automatic "this might not be you" detection: Same engineer that 
invented the original failure bucketing system also invented a statistical
modelling solution that used past history of the suite results, behavior
of existing and known failures, to offer a developer a probability
bet over whether or not a given failure newly seen in their run most likely
came from their change, or might have just been waiting in the code to
get caught.
- Automatic bug investigation tools: One of our engineers with a passion
for testing and automation spent a lot of times trying to find ways to
make intermittent, hard to repro failures easier to repro. He was able
to do this work because the duplicate detection system gave every failure
an identity. He could search the automation system for instances of that
failure and begin an investigation. He found information he could use
from other tests on box, configuration of the run, language used, and
a variety of other variables to change rate of failure on repeated execution.
He got good enough at it he wrote a bot to do it for him. The bot was
formalized, so now when a flakey failure shows up on the system, a
bot starts crawling for data that indicate the failure may happen more
often in some states than others, and if the bot can identify that
case will re-activate the failure (engineers like to close intermittent
failures they cannot reproduce) and assign back to the team with the
additional information.

Prior to this set of capabilities, engineer satisfaction with automation
results was below 50%. After the changes, satisfaction was in the upper
80%. Dissatisfaction with the automation system was the top feedback
from engineers for the longest time. Exploiting the potential of
duplicate detection was key to turning that around.

More about how we use duplicate detection in Office
-----------------------------------
I have a presentation from about flaky test automation delivered
at the Pacific Northwest Software Quality Conference all of which
derives from the ability to automatically detect duplicate
failures. You can <a href="https://waynemroseberry.github.io/about">find the link here</a?.

How do I do that?
====================================
It is possible some tool vendors do a good job of this. If so, I have
not heard many people talk about taking advantage of it the way we
did in Office.

I have some example code in a GitHub repository. I call it DupIQ, short
for Duplicate Intelligence Quotient. The code is publicly shared. It is
implemented on ASP.NET as a REST API. It requires some elbow grease
if someone wants to try it out, use it for themselves. It comes as is.
I wrote it over the last summer and have not had a chance to put it
into operation. This is not a turnkey solution, but the bones of a functional
system are there.

The DupIQ Duplicte Detection Design
-----------------------------------
DupIQ utilizes a popular design used by search engines, text matching,
and anything else where the inputs are similar to each other in fuzzy ways.
It is very different from the design of the failure bucketing
system used by Office I describe above.

It does the following (in summary):
1. A failure report message is input as a string
2. A set of word embeddings are built for the message (pluggable, the current system uses Word2Vec for simplicity)
3. A vector database (Pinecone in current version) is searched for the top 1 entries closest to the embeddings (using cosine similarity)
4. If the report and the top result are less than a specified threshold distant from each other (configurable), the report is declared a duplicate of the top result. The report is stored in a database with the ID of the prior result associated with it
5. If the top result is more than a specified threshold distant from the report, the report is considered a new failure, its embeddings are stored in the vector database, and the issue message (as well as date and time, and other information) are stored once for the instance of the report, and one for a record of the failure in an SQL database.

Every failure reported to the system is given an ID this way that can be used to see how often the same
failure has been reported and when.

Beyond Duplicate Detection
-----------------------------------
Duplicate detection is just about establishing if a failure is
new or not. An existing automation system would need to
integrate it. Script driven automation runs in Jenkins or
various CI/CD pipelines are probably in the best position
to easily make a call out to a REST API before further
processing of faiure results.
