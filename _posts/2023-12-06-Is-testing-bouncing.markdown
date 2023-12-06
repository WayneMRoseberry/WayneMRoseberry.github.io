---
Title: Is testing bouncing?
---

Get rid of testers, then start hiring testers
=============================================
![A ping pong ball bouncing off a wall toward the viewer with the words "no testers" printed on it](/assets/notestersbounce.jpg)

I have been interviewing for work since March 2023.
I was surprised to see evidence of a
testing bounce, a return to open testing jobs from
workplaces that had previously decided they didn't
need testers. It is not a loud, large signal, but it
has been there.

I am going to share some stories from these
interviews, some common characteristics
I saw between these stories, and then some
thoughts I have about what this might mean.

Example interviews I had with employers this year
=============================================
These are all real interviews I had with employers this
year. I have obscured details about company names
and product specifics. Three of these are companies
with worldwide, everyday brand recognition. Three of
them did not have an open head approved but were exploring
the idea because of problems that came up. Four of the
five companies do not have testers.

- I interviewed with a director of engineering at a well known
company (almost everybody uses their product at
least once in their lives) who was looking to fill two
positions. This company had gone all in on removing
their QA teams and pushing all testing to their
developers several years prior. One open position
 was a director of engineeering reporting
to her that was to drive improved testing capability services,
tooling and support because their development teams were
having trouble covering critical tests on their own without
better capabilities. The other open position was for an engineering manager
that was to run a team of SDETs whose charter was to
run through enterprise customer platform extensibility and
custom application scenarios, because they had been having trouble
with those end to end scenarios breaking after new product
drops.
- A VP of data quality at a data analysis services company
whose customers use that data for their own research as
part of regulatory approval for bringing product to market. The
company was built on a "No QA teams needed, developers do all
their own testing" paradigm, and any overt indication of
hiring QA positions was met with sharp disapproval from the
development side of the house. This VP was concerned with
recent instances of data integrity problems coming out their
pipeline. For their customers, data integrity problems result
in legal trouble, the kind where they are brought forward to
testify before congress with stiff penalties. The VP was hoping
to open a head to serve covertly as a test engineering leader that wasn't
called QA, but whose primary goal was to build the strategy
to cover the gaps in testing for data integrity to prevent these kinds
of problems.
- An engineering manager for a games product who related to
me a conversation where his manager had said "Remember when we
got rid of all the testers several years ago? I think that was
a mistake," and has since been building a team of engineers whose
primary goal is to build and enable testing capabilities that
cut across the scenarios where the individual development teams
miss problems. One of the examples he described was a fun
skunkworks mechanism that measures online streamed gameplay latencies
end to end, catching bugs in performance that at every single
individual end point was measured as within tolerances but
when covered end to end was clearly causing customer problems.
- A director of engineering at a large, centuries old
non-software company who is looking to modernize their software
integrated aspects of product  (burned onto silicon right now)
and shift to modern software modes where product can be
updated, improved and fixed without having to update chips. The
nature of the product is such that software failures could
range from trivial risk to physically harmful or fatal. The
teams are working on old-style, slow, hardware based schedules
with long pole tasks for everything, including testing, and
they hope to bring a faster, more effective, more efficient
testing process to the team. The goal here is to get
ahead of the game, get the testing covered well without a
hasty shift to 100% fast feedback biased iterations.
- An engineer manager at a company who produces monitoring
software for power systems. Their business has no QA teams. They
are not exactly CI/CD with fast, trunk based Agile engineering, but
their market pressures are such that this doesn't matter to them, and they
run pretty close to that state anyway. They had not had any huge problems
come up, but the manager was noticing inefficiences in the way some
kinds of testing and quality assessment was happening, and felt a
need for somebody to drive testing practices that spanned across
systems or filled gaps the individual development teams were
not hitting. The position needed VP and CEO approval, which he
was unable to get after our conversation.

The common themes
=========================================
I saw the following common ideas across most of these accounts.
- Previous decision to remove testing teams
- Generally satisfied with their developers owning testing
- Problems affecting the business or customers that came from gaps in the testing
- A capability, tooling, and support driven model, based on QA/testing expertise, to close the gaps
- Less interest in direct introduction of dedicated testing engineers, except in special scenarios

For three of the five companies there were significant enough instances of
problems getting past the development teams that motivated a
change to the existing development owned testing activities. The
perception of pain came from real events, the perception of
gap was ambiguous, as if someone was acknowledging there
was something not there, but they didn't know what it was. The sense
I got in the conversation was that they believed the problems were ones that
either spanned lines of ownership/scope, or were larger, bigger, more
challenging than the developers were able to handle given current tools
and practices.

None of the companies wanted to abandon a dev-centric ownership of testing.
Few of them wanted to augment with testing by dedicated staff, but
all of them definitely wanted to bring in people with prior testing
knowledge and skills to help support and build the development
team capabilties to do better at their own testing.

What does this all mean? Is testing by testers coming back?
=========================================
Way to put up a strawman, Wayne!

There is still a large market for softare testers, mostly satisfied
by the majority of software development shops that have not adopted
continuous integration and continuous and delivery and have not
yet adopted the "no testers neeed" viewpoint. It is the front
part of that wave, which does have a large number of players, where
the "no testers" thing is playing out.

The tester bounce takes a while to happen because there are large
benefits to the type of development practices that have to happen
to enalbe delivery of product fast, often, and with no QA team. Most of those improvements
come not from better testing, but from better coding. MUCH better coding.
This article was almost a diatribe 5x as long as it is now where I describe
the affect from that change in coding, but I opted to cut 
the diatribe. Suffice it to say that when
a team starts that shift, the code stabilizes fast, the business ability
to target the product the customer needs improves substantially, and it is
difficult to see past all of that improvement to recognize other problems
that may be accumulating.

I was seeing some of these sorts of accumulations at Microsoft. Problems
would get all the way to market. Expensive problems that could not
be repaired with a simple roll back and "I am sorry." Most of them
got through because there was not the type of scrutiny we used to employ
when there were people whose specialty was looking for product risk. I believe the
same thing is starting to catch the attention of companies who are
ahead of Microsoft when it comes to adopting fast, agile iterations. I believe
these problems are going to motivate a second look at the "we don't need
no testers" model.

I am not convinced we will see a return to the generalized tester
assigned on a per feature basis. I believe the "no testers
needed" shops will bounce back to a different model. I am certain the "testing
expert building team capabilities" model will grow its presence, given teams are already
opening this job title up. I believe there will be some test and sdet crews
opening up, although I believe their charter will be pointed in specific,
chosen directions instead of a general coverage of features one by one. I find it difficult
to predict what future is coming.

Bouncing to the end...
==============================
Bottom line, I am seeing a bounce, and it seems to be happening for
a reason. For all the positives of the current faster iteration
practices, the zealous adoption of "fast first" dogma is leaving gaps. 
Problems are leaking through, creating wet spots on a floor that is weakening.
As some businesses are noticing, it is only a matter of time before something
falls through that floor.
