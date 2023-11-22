If you are the customer you aren't really testing
=================================================
A lot of testers like to say they represent the customer, or
that they are the voice of the customer, or that they are
acting like the customer. Their intention is good,
and paying attention to problems the user will face
that the product team might miss is good. But no
matter how hard we try, we cannot be someone else
until we are entirely within their lives and context.

And then we aren't testers anymore.

I am going to explain with a story about integration
testing.

Integration testing Microsoft Office
=================================================
Sometime in the early 2000s, when I was a test lead in
the SharePoint team, I heard buzz of a concern over
integration testing. Several members of the test leadership
in Office had met and worried about how to cover integration
points between the different Office applications. Integration
has always been a major selling point for Microsoft Office,
being the key differentiator back in the early 1990s when the
core products Word, Excel and PowerPoint were bundled
together in the same box. While each was trying to make a
play as best of breed in their own market, the priority
was a "best together" emphasis that became more and more
key to the product strategy and architecture moving forward.

Integration is a difficult testing problem even on the simplest
applications. For a suite of complex applications that span
platforms, client, server, and services which integrate on everything
from file formats, clipboards, communications protocols, embedded
objects, shared components, various backend and client side
APIs, common look and feel, and hundreds of other touch points, integration
is a testing nightmare.

Or dream job. Depends on how much you enjoy looking for problems. But
from either perspective, the key issues are the high degree of
complexity and large domain space.

The combinations are mind-boggling
------------------------------------------------
Apparently, the top concern at this meeting (I wasn't there, so
I have to go by accounts given to me by others) was the combination and
permutation space. Everyone was imagining every feature, data point, and
shared behavior between every app with every other app in the suite,
and then adding +1, +2, +3, etc. depth on that in their heads
and realized how large the problem grew. The reports I got was
the mood in the room was befuddlement and exasperation. How to cover such
a problem?

How to cover such a problem, indeed!
------------------------------------------------
I have this thing where I hear someone state a seemingly impossible
undertaking, and I have to figure out to actually do it. I look for
shortcuts, less ambitious solutions that get at the heart of the problem,
but try to avoid boiling the ocean. And for this problem in particular,
I realized we already had a solution. I knew we already had a solution,
because our customers were doing it all the time. Customers use
the Office suite in an integrated fashion without being paralyzed by
all the possible combinations and permutations. Customers just have a problem
to solve, and they set about solving it.

We already had a word for this. Scenario testing.
