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

The "There is nothing new under the sun" scenario
=================================================
The concept of scenario testing was fresh in my mind because user stories
and personas and scenarios had made the rounds of design at Microsoft several
years prior. I think it was Julie Larson Green that made the big push,
and this notion that we should design our products around a set of problems
customers wanted to solve, and little stories about how they solved it, were
all over the design documents and specifications. Our testing approach was
similarly affected.

It seemed obvious and natural to me that one answer (there never a singular "The Answer"
to any problem of this scope) to the integration testing problem was to apply user scenario
testing. Rather than worry about the massive world of all possible combinations,
use hand picked user scenarios to focus attention and time. Sure, we would could
not cover everything, and we would leave a lot uncovered, but we would also
very likely cover a lot.

I made my pitch, and I was given a small virtual team of engineers.

Scenario testing and product integration
------------------------------------------------
We wanted the testing to lean heavily on integration. We also wanted to and accelerate discovery rate, so we set some parameters:
1. Pick a persona (e.g. secretary, data analyst, etc.) who has some problem to solve (e.g. manage a list of clients, check expenses for the team, etc.) which is not solved by the product out of the box
2. Choose a solution that requires combining at least two, preferrably three of the applications together to solve it
3. When you come to some point that has a problem, report that problem as the bug - if you are blocked, you are blocked, so move on to a new scenario

The customer really needs to solve this problem, but you don't
------------------------------------------------
The beginning of our effort was quite productive as we did some initial product integration
exploration to demonstrate proof of concept, and to convince testing leadership
the effort was worthwhile. We found bugs very quickly before we began the scenario testing.

When the scenario testing actually started, I found a problem come up I did not expect.

The team of testers I was working with had a difficult time getting their head out of the problem space of
the customer. Two of them were working together on a data tracking and reporting problem for an imagined
secretary who was starting with data in Excel that was going to eventually be pulled into Word
for reporting. While working up alternative means of getting the data back and forth smoothly, they
found that the initial means they planned was not going to work. It was blocked by
something that somehow lost data or didn't allow everything to come over easily and conveniently.

They described this problem to me, and said "So we decided that the best way to get the data over
was rather than use the product features directly, we would build a separate app and use the
product APIs to..."

I stopped them at this point. They had blown right past the whole point of the exercise. They had
deemed the customer blocked without going into full on developer mode. That was the bug. They should
have stopped, described the block, and moved on to something else. Instead, they were spending more time
imagining how to work around the block than they were looking for more problems.

They were thinking like a customer, and that was a problem. They needed enough customer thinking to
imagine the problem, to think of how a customer might try to solve it, to recognize when the bug
came up, but that is where it ends. Because their real job is to describe risk to the product. The
customer doesn't care or want to be involved in telling the product team about bugs. The customer
wants to get their problem solved, will go hunting for ways to solve it, and when they do
will probably never tell the product team about the gyrations they had to go through to
get around it.

Continuining on like the customer would is very alluring. It feeds our problem solving
mentality. It is creative. But in this case, it was the opposite of what we should have been
doing.

The rest of the story
=================================================
The integration effort did catch attention of test leadership, but so did the rest
of the ship schedule. Eventually each of the testers loaned to me were taken back
to focus on other problems within their team.

The scenario testing focus caught the attention and the imagination of the rest of
the Office team. From the point of my integration testing project and forward, different
Office product teams used scenario testing - user problem based modes of focusing
test attention - as a key coverage and discussion point across the team. Even though
my project shut down over competing initiatives, the approach we used was popular.

More testing ideas for you and your team
=================================================
I cover other testing ideas in my book (linked below) where I focus on how to quickly put
together a test plan that help you think of product test coverage, share and critique
it with others.

<a href="https://www.amazon.com/Writing-Test-Plans-Made-Easy/dp/1478333693">![Writing Test Plans Made Easy](/assets/writingtestplanscover.jpg)</a> 
