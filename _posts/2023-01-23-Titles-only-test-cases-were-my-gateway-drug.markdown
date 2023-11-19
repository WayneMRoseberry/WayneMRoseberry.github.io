Titles Only Test Cases as a Way of Breaking a Habit
==========================================================
![A row boooks on a shelf, titles facing out](/assets/booktitles.jpeg)

If someone were to ask me today for advice on a test case management strategy, 
the first question I would probably ask them is “do you really need one?”
I believe a separate article is necessary to explain that question, maybe a small book. 
In this article I describe an in-between step in my trip from dedicated formal test case enthusiast to whatever it is I am today.

A story of a young tester questioning a love for test cases
===========================================================
I was interested in formalized test approaches early on in my test career. 
I wrote out steps and expected results. I shopped around for test case 
management systems to store cases and, more importantly, their results. 
I eventually reached a point where I was driving the test case management
strategy for large groups of testers. I spent a great amount of time in my career 
thinking about how to use test cases effectively for various purposes.

As I was getting deeper and deeper into using test cases in certain ways, 
I was also stepping further and further from the formality. There was a project
when I was leading a group of testers, and we were preparing for a coming test pass. 
This was in the waterfall schedule days where one could wait weeks, even months, 
before the development team produced a build functional enough to start the app. 
A typical test pass would take about two weeks, development would fix the bugs,
and then we would have another two weeks, more fixing, and then usually a third 
pass before things looked pretty good. It sounds painful, but it was pre-Agile days. 
The test case creation was taking about a week, maybe two on average, so the
expense of all that editing in the case management DB, which was a very tedious tool, was bothering me.

My team was writing their test cases, we were reviewing them. I remember one 
less experienced tester, less comfortable with technology, and I was reviewing 
their cases, which were written out in formal 1-2-3 stepwise format for every test.

They were testing some client UI integration with the Windows system menu 
behaviors: minimize, maximize, restore, close – each in combination with some prior 
state in the application functional behaviors. For each of these, the tester had written 
a case for the different ways to get to the system controls – the icons in the upper 
right, the system menu on the left. Likewise, a case for different ways to activate the
menu, mouse, keyboard, etc. Every single one of had the same sequence of steps 
except the one variation that distinguished icons versus menu versus keyboard versus mouse.
I was bothered. I did not believe these cases likely to show distinct failures, 
also hated the cost writing repeatedly the steps for all those cases. I couldn’t take it; the dam had burst.

I told my team to stop writing out the stepwise test cases. I told them to switch to “titles only” test cases.

Yes, a Titles-Only test case
===========================================================
A titles-only test is exactly what it sounds like. Inside the case management database, 
the test case is described and distinguished by its title. There are no steps inside the 
body or description of the case. For proponents of formal test cases, where every step and 
every detail are spelled out one at a time inside the body of the case, with ids to trace back to 
requirements, version control to trace to results, explicit expectations to account for exactly 
what failed and exactly what passed, the phrase “titles-only test cases” is a horror show.

Specifically, I told my testers to do the following:
* Use your test specification (or plan, or design document – I don’t care what you call it… it is a document) to describe the steps for the test case – share steps between cases
* Make it obvious in the title which shared steps you are referring to and which permutation it is
* Put a link to the test documentation inside the test case description

At this point I still had not given up on the idea of test cases having formal steps, 
I just told them to stop repeating steps in the cases. The idea of formality was preserved, 
but at a lower cost to create and maintain.

What I gave up was a goal I learned years before regarding test cases, “Write them so 
that anybody unfamiliar with the product could follow the procedure and do the test 
properly.” My new direction was “Assume the person executing these steps knows the product
well and is deeply familiar with the test plan.”

That last little compromise was a wedge, and probably the most important part of my 
change in views. It was about treating people like thinking, intelligent, creative 
individuals instead of non-thinking, non-skilled automatons.

The impact on cost of suite creation and maintenance was realized immediately. 
We no longer had the 5-10 days of data entry. We could go from thinking about cases to 
something in the database quickly, especially with tools people crafted to do the task 
for us easily from spreadsheets or other easy to use formats.

Moving along
===========================================================
Over the years after my titles-only shift, I adopted and advised several other practices:
* Drop the steps

Assume that the person testing the application understand the system, test plan and generally knows how to test. Assume they can infer the steps. Focus the test documentation on describing the problem you are concerned with. For example, if you are concerned that a given set of operations will be impacted by encrypted network channels, indicate which operations those are, which encryption protocols you intend to cover, and if necessary, describe any specifics needed to analyze what is happening. Tailor the needs to the testing – if you can get by writing less and still remember what to do, do it. Only include steps if it truly aids in what you are going to do – some tests are very sophisticated in their design, and the act of writing them down helps with the thinking, but this is the less common case. Most of the time the tester will know what they intend to do.

* Group the cases
![Grouping the cases, depicted as adorable beagle puppies.](/assets/adorablebeaglepuppies.jpeg)

This is a tradeoff that can take creation and maintenance costs down easily by an order 
of magnitude. I saw entry costs shift from about a day to just a few minutes taking this 
strategy in combination with data entry tools. The difference is having something 
like “Test AddServer function with URI==http://127.0.0.1:83” versus “Test AddServer 
with different URI addressing permutations.” The guidance I gave testers was to use their 
own judgment on whether the grouping was too course. For example, just “Test AddServer” is 
confusing – test what about AddServer? A set such as “Test AddServer with different 
security protocols” or “Test AddServer against different user roles” gets the reader
closer to understanding what the test implies.

The trade on this is that one must read further for the details. Another trade is 
that you give up the high granularity one case at a time failure traceability. 
A tester or their manager needs to decide how they fell about that. In my 
experience the team at large used the bug database almost exclusively as their
measure of progress, so I wasn’t giving up anything that caused any problems.

* Don’t do wall-to-wall formal test case execution, don’t rely on the formal description to find the bugs

This is advice I had always used, but was giving it more often the further along I got. I didn’t want testers to try to up front describe every single test they would run, every single expectation they would validate. I would tell testers to, at most, use 50% of their testing time described by formal up front cases, but the rest for testing things they did not anticipate up front.
![A photograph of a horse drawn wagon, full of horse manure, traversing a grassy field.](/assets/manurecart.jpeg)

My experience with formal cases was that even though they were effective at finding bugs (I 
have more success with them than some opponents of the practice want to allow), it was 
atypical that the exact steps and exact expected results were what yielded the failure. 
It was often something I noticed along the way that seemed wrong, or maybe preparing 
the system to get ready for the initialization steps something failed. Or there was 
a failure, but not “the thing didn’t do the thing I asked.” I once described formal test 
cases as a manure cart driving up and down narrowly defined rows in a field, and that I 
was counting on some of the manure accidentally landing where I didn’t plan to give me better 
coverage of the field. All the formal cases did was provide a framework of ideas that assured 
I was out in my field, spreading the manure.

Cheat when running the cases
===========================================================

I found that almost every tester around me that was an avid user of test cases was 
never really testing from the cases. I never really did either. Rather than go 
one case at a time and follow steps, I did something more like this:
1. Start of day: take a quick look at the cases I haven’t run yet, decide if any of them represent whatever is top of mind for me
2. Testing: Pick some topic that seems important right now and explore it – that may or may not intersect with the test cases
3. End of Day: Look again at the list of test cases, and if I recognize that they match what I did that day, mark them PASS if I found no bugs running them, FAIL if I did
4. Later that Week: Look at the cases I haven’t run yet… do I still need these? If not, out of the suite you go

Every tester I knew either didn’t use test cases at all (frustrating for me at
a time because I was the key driver of the adoption of the tools we were using) or were 
really doing exploratory testing all day and accounting for case alignment after the fact.

By this point I was no longer prescribing formal test cases.

I was prescribing To-Do lists with lots of metadata and version-controlled results.

But maybe more important than that, and perhaps not emphasized strongly enough in this article,
was that the key to this transition was to trust the intelligence and creativity of the testers. 
A “Titles-only” test case assumes a human is smart enough to fill in the gaps, and once you 
have accepted that you are on the road to accepting a good deal more.

More About Titles Only Test Cases
===================
When I wrote this book years ago, titles only test cases were heavy on my mind. I wrote
the tips and tricks and tecniques in "Writing Test Plans Made Easy" to support a lighter
weight way of talking and thinking about testing and supporting test activity.

<a href="https://www.amazon.com/Writing-Test-Plans-Made-Easy/dp/1478333693">![Writing Test Plans Made Easy](/assets/writingtestplanscover.jpg)</a> 
