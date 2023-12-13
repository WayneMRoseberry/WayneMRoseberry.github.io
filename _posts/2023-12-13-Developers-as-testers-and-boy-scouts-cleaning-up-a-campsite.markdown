Testing your code and cleaning the campsite, a comparison learned from leading Boy Scouts
=============================================
![An image of people cleaning a campsite](/assets/cleaningthecampsite.jpg)

"What do we know?" versus "what did we miss?"
=============================================

I was watching "Continuous Delivery," a YouTube
channel I like by Dave Farley. The video today
was <a href="https://www.youtube.com/watch?v=QFCHSEHgqFE">Don't
do E2E testing</a>, and I realized there is an important
difference between how a developer thinks about
testing versus how a tester thinks about testing,
and that reminded me of Boy Scouts cleaning up
a camp site.

There is more than one way to do it.

What do we know?
=============================================
When you occupy a campsite for several days,
eating, sleeping, playing around, the site
gets cluttered quickly. Scouts leave their
gear, clothing, food, wrappers and other stuff lying
around.

This creates a problem. Rain gets clothing wet.
Gear gets broken. Food and wrappers attract
animals and vermin which break into other
gear, make a mess, leave droppings on
other gear, sleeping bags, etc.

![An AI generated photo of boy scouts cleaning dishes](/assets/scoutscleaningastheygo.jpg)

The approach to this problem: clean as you
go, or soon after (which usually means an
adult or more senior scout notices the mess).
This is "What do we know?" because we are
cleaning based on our activity. If we
just ate, we clean up around the eating area.
If we just cooked, we clean up the cooking area
and put away gear. If we took off socks or layers
of clothing, we put them away inside the tent
where they are not exposed.

This type of cleanup is based on a knowledge
that we were just doing something in a specific
spot and a fast, easy tidying up around that area
will do the trick.

What did we miss?
=============================================
At the end of the camping trip, after we pack
up all our gear, we clean the site. The approach
we take is very different than the as-we-go
cleanup. That approach is meant to get problems
we anticipate or know about in spots where we know
we were doing something. The final cleanup is
meant to catch something left behind, some mess
or piece of litter or trash, or piece of equipment
tha we might have missed.

The approach is intended to maximize observation
and coverage. It relies on a bit of redundancy. It
is slower than the as-we-go cleanup.
![a line drawing depiction of scouts in a line cleaning up a campsite](/assets/cleanwhatwemissed.jpg)

We line the scouts up about three feet apart in a
line on one side of the camp site, and we walk to
the other side. Every scout is to pick up any
non-nature object they find. Wrappers, food, gear,
nails - anything that a human introduced to the
site that is not part of the site itself. We never
come out empty handed from this sweep. Often we
find something that had been left by previous campers.

Why don't we always just do a line-sweep... or vice versa?
=============================================
Each form of cleanup works well based on the problem
it is trying to solve.

If we employed the "what we know" style cleanup at the end,
the scouts would put all their attention on things like
the table, the campfire, or anywhere else they remember doing
something and they would ignore the rest of the site. The
whole point is to cover something we forgot, so a "What do I
know?" approach fails.

If we employed a "what did we miss" style cleanup as we go
along, first we use way too many scouts to address the cleaning,
but also the volume of things out and around the campsite
would have to be triaged by the line and the whole process
would get bogged down. The "what did we miss" style cleanup, at
least for camping, works best when the site is already
primarily clean and you need to do a final pass. Its formal,
redundant, large sweep approach works best for finding things
you forgot about.

If you have ever gone camping, you can probably tell when
the prior campers did not do a campsite sweep. There will
be bits of paper and plastic, little remnants of containers
and bottle tops or whatever else littered between the
leaves and twigs, around the table legs. It makes the camp
site unpleasant, and an attractant for pests.
![AI generated photo depicting a campsite with litter left behind and mice](/assets/campsitelittermice.jpg)

How does this relate to testing software?
=============================================
One way to categorize testing methods is to separate them
from testing for problems we anticipate versus testing for
problems we might have missed.

Testing for problems we anticipate are usually optimized for precise
answers regarding questions we know ahead of time around changes we
understand and with expected outcomes we know already. Usually the
more precise, the more accurate, the faster, the better for this
kind of test. We want these tests to be so efficient and easy to understand
that we can use them as the developer writes the code. This is like
the "clean as we go" approach we teach Boy Scouts to use on a camp out. If you
know you were doing something, clean up the area you were doing it
as you do it or just after you finish. Keep it fast, simple and efficient.

Dave Farley's "Don't do E2E testing" video I referenced from above is one
of the best "here is how to clean as you go" videos I have seen. By all
means, follow that part of the advice.

Testing for problems we might have missed approaches are optimized by
trying to increase the probability something bad will happen and that
you will observe it. This usually means testing across larger surface
areas, increasing complexity or load, widening the view of problem potential
to detecting anything, even at the cost of false positives. The goal is
to spot as many problems as possible, and you pay for it with some
degree of inefficiency. This is like the slow, redundant, sweep of the
campsite at the end. 

There is a point in the "Dont' do E2E testing" video at 9:14 where
Farley says something really important. He says:
> _Simply by adding systems A and C into the mix here, we've increased
> the number of places where stuff can go wrong._

Farley is mostly referring to ways the test fixture and test methods
can lose control of the system under test, something which the
"clean as we go" approach addresses by targeted focus. But this same
attribute is to our __ _advantage_ __ if we are trying to find problems we
missed. If we are looking for bugs, we want to increase the number
of things that can go wrong. We want to find something our targeted, "clean
as we go" approach might not have found.

Dismissming E2E testing as a "Don't do" is like skipping the final
sweep of the camp site. Every mistake you made you didn't know to check
for is going to stay there, on the site, for the next camper. That camper
happens to be a customer. Let's hope what you left isn't a rusty
nail or something...
