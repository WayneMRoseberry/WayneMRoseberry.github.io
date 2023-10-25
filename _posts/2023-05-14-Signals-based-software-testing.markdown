![In honor of smart monkey testing, an image of a monkey on a radio tower.](/assets/monkeyonradiotower.png)

For the last several years, I have been working on a test approach that I call "signals based testing."
 
_I define it as when we exploit engineering and operations data emitted by the product in order to find bugs by applying some workload against the product._

Exploit engineering and operations data emitted by the product...
==========================================
For sake of this style of testing, a signal is data emitted by the product that is not intended for consumption or use by its primary users, but instead for the operations and/or product team. This differentiates it from normal product output, such as user data files, UI displayed on a screen, sound emitted, or even behavior of machinery and electronic devices.

For most modern software, the product generates data that it sends back to the product team. The data is usually a mixture of diagnostic information and customer usage patterns. The purpose of the data is to allow the product team to improve the product in various ways by identifying new feature opportunities, observing problems, and spotting emerging issues in production.
 
This is the primary signal referred to in signal-based testing. We use this signal to do product testing. By product testing we mean learning about product behavior in a way that assists in making decisions, most often regarding risks to the business, product, or customer.

...while applying some kind of workload to the product.
==========================================
This part differentiates the act of signals-based testing from testing in production or mining the customer signal. While these other practices intersect heavily with signals-based testing, in order to do signals-based testing one needs to be intentionally and decisively making the product do something rather than passively consuming the in-market product behaviors. We test to learn something in ways we do not learn in the wild for various reasons; because we can do it earlier, under our control, at will, using diagnostic and inspection modes not afforded in real-world, and often far more efficiently.
 
Workload is a very broad term in this sense. Loosely it means "using the product", or "making the product do something." The workload might be engineers interacting with the product directly, or it might mean automatons driving the product, creating a synthetic workload. The point of the workload is to get the product to do something which might yield useful information from the signal.

Doesn't our other automation do that?
==========================================
Maybe it does. But probably not.

Most of the time, when someone refers to automation used in testing, they are referring to the checks used during build evaluation, commits of code to repository, as part of a CI/CD pipeline. If that is the case, then really, no, the other automation approach is not a form of signals-based testing.

Even if there is a signal emitted from those runs (may or may not be - these kinds of things tend to be configurable), chances are the automation does not look at the signal at all. Further, these sorts of checks are intended to emit a binary result. The check fails, or the check does not fail (it "passes", in common parlance, something I consider a misnomer, but I am not going to hash over that here).

For many reasons, signal-based testing is ill-suited for these kinds of checks. Finding problems in signals is more exploratory, more analytical, and a binary decision is almost impossible to make given the time constraints. Signals may be part of a much larger cloud-based solution, with latency times in minutes, maybe hours (I have seen some take more than a day). The demands on automated checks typically drive to the shortest execution time possible - some teams expecting no less than a few minutes (although I believe that is aspirational - I know of several who talk of times in hours). Independent of time, computing a result on a wait time in hours for data to come back is going to yield a lot of inconsistent results for no other reason than timeout waiting for results.

What kind of testing beast is this that does not PASS or FAIL?
==========================================
Signals based testing tends to be highly analytical, highly contextual. It tends toward analysis of data in aggregates, in trends. It relies heavily on oracles that signal "this might be bad" rather than "this is what is expected."

"This is bad" not "this is good"
------------------------------------------
Signals based testing happens amidst complex usage. Customers use the product in rich, complex, and unpredictable ways. There is no state-based oracle that can arbitrarily follow a customer path through a product and say in an automatically checkable way "the state after this last action ought to be <this>."
 
Such state-based oracles are what our usual checks do. They can do this because they limit themselves to a finite set of scenarios, and execute very simple procedures where the expected state is easily constrained, easily checked.

All that goes out the window with signals-based testing.

Instead, we flip it around. Instead of looking for what we expect, we look for things we will recognize that bother us. We look for known patterns of something going wrong. This ranges from being very easy to being very difficult.

What sorts of things look bad?
------------------------------------------
There are some easy oracles indicating failure. Product crashes, in-product asserts and errors, performance markers, all of these and others are commonly used to indicate failure for a customer. They are just as meaningful if hit by a synthetic workload. The first venture into signals-based testing is easier to start if one picks these kinds of events in the signal. You can usually collect all the data you normally would for a user and report the bug the same way.

Sometimes error signals are less certain. Sometimes there is inconsistency around what an error means. Does it mean a very bad thing happened causing a problem for the customer? Does it mean something wrong happened, but the product applied mitigation and the customer is okay, but you track it anyway to understand it better? Does it mean the developer has something they do not fully understand, and put it in there more as an information event, and maybe it is very bad but maybe it is completely harmless? Are the errors tracked using consistent, easy to identify markers, or have different developers adopted different format and schema for indicating an error? This kind of confusion is not uncommon if the error signaling practices were adopted before the team formalized any kind of procedure around processing those errors. You will find that whoever is trying to make sense of the customer signal is wrestling with the same issues that come up when analyzing the same events during synthetic workload.

One approach that works pretty well is the application of AI and ML on analysis of the customer signal. A very commonly used one is anomaly detection, which tracks historical trends in a historical pattern and indicates when the data is trending to something contrary to that pattern. Almost any event or collection of events can have anomaly detection methods applied to it, so long as there is a way of establishing a meaningful baseline. There are some challenges to this with synthetic workloads that have to with credibility of the patterns in the workload which we will talk about later.

Sometimes signals are far more sophisticated than just being an error. A given event may be normal and happen all the time, but the proportion of that event relative to another one might become a problem. For example, an application might have a "establish new document workspace" event that happens the first time a user opens their workspace, and that event is perfectly normally occurring at a certain rate. That same event might mean something is very wrong if it occurs every time every user opens their workspace. The threshold for "bad" is harder to define because the situations that trigger it are tied in with user behavior as much as product behavior.

Whatever the kind of signal, you are looking for markers of "bad."

What about that workload?
=========================================
So far, this article has been hand-waving the term "workload" as if we can just produce such a thing from thin air. Like most testing, it requires more work than that. Further, no matter what the workload, there are some considerations.

How will you generate the workload?
-----------------------------------------
I have seen engineering teams use a "gather the people" approach. They get a bunch of people together and have them use the product for a given period of time, trying things in either an open-ended, or very coordinated way. After the fact, they analyze the product signal to see if anything interesting comes of it. My observation and the ad-hoc accounts I have received about this indicate some initial success that diminishes rapidly. The problem is really about volume and scale. A group of humans can only move so fast, and signal based testing gets more out of volume because most of the analysis is about trends and aggregates. Further, the coordinated tests are often about getting multiple apps into race conditions against each other on some shared server or messaging resource, and that is so difficult one gets far better luck with a stochastic approach, which gets back to the speed and scale problem.

This rapidly gets us into automated tools. Automated tools work very well for signal-based testing. One of the most common forms of a signal-based testing is stress and load testing (see, nothing new under the sun...), which gets all its power from the ability of automation to apply heavy, rapid, sustained load for long periods of time. We can also use automated tools that scale outward, adding more and more agents working against a system. With an increase in volume comes an increase in probabilities. If a given set of race condition are exercised thousands, millions of times, then we increase the rate we are going to hit the various combinations of those races than if we had exercised them only once or twice.

Another powerful advantage of automation is complexity. By straying away from the controlled "step 1, step 2, step 3, ... " design pattern and instead creating automation that mixes operations in a more random, unanticipated way in longer sequences we can get the application into states which may have been difficult to reach by either prior automation or humans testing interactively. Humans do have a knack for getting product into states an automaton won't, mostly because we fail to think of telling the automaton to do so, but it takes very little work to randomize an automaton that gets ahead of the human in that regard mostly because of time and scale. The person might have eventually got there... but not as quickly as a hundred machines pumping away at it at 100 UI operations per minute.

A challenge of this is crafting the script. Like all automation, it means work. It is in some ways easier than regular checks, and in some ways much harder. Easier, because you don't need to write the check, you don't need to worry about keeping precise control of the application state - just keep going. Harder because "just keep going" is easier said than done. Also harder because there are some things you don't want to accidentally do (e.g. randomly clicking the UI of an email client... we need some walls around that scenario).

An emerging market of automated tools are applying AI to generate workloads that aren't specifically programmed or selected by developers of automated scripts. This class of tools inspect an applications interface (document DOM, accessibility APIs), and build their own model for combining actions to traverse different application states, and then replay those actions later. Such self-learning automatons can be a very useful tool for driving synthetical load to use with signal-based testing.

But is that workload any good?
------------------------------------------
There are some major challenges with the workload we use in this kind of testing:

* if it is very directed, then we are losing on volume - it if is very random, then we don't know if the behaviors are important.
* the more random, the more the possibility that the workload is going to do something we do not want it to do.
* the more complex the automation behavior, the more difficult it is to diagnose - but at the same time, the more good bugs you will find.
* the combinatoric space gets very large, very fast, and probability starts to fight against you.

We need some way of managing these challenges to the quality/usefulness/acceptability of the workload.

If we take a more directed approach to the workload, we need to accept that we are limited to what we can think of, what we can afford to write. This is a common concession, but it is a trade.

If we take a more randomized approach, we are going to have to invest far more into tools and skills to analyze the data, analyze output of the automation system, to understand what the tools were doing. Expect to put a substantial amount of effort into logging analysis, visualization tools, and anything that helps an engineer understand and prioritize.

Be prepared to put some kind of guardrails around the system. For example, you may need testing with real accounts on a given service, but you might need to disable email access to prevent it from going on a spam rampage. You will probably need something to suppress usage of certain features to keep tools from randomly navigating into features that cause problems.

For the combinatoric space, the solution right now is a matter of priorities and choices. If you are using a directed approach, the priorities are baked in - you made the choice when you decided what to automate. If you are using a more randomized, stochastic approach, the problem is far more difficult. The answer is going to vary based on the nature of your tool, but the general answer is you need some way of expressing to the automation a direction, a focus, what to do among all the various possibilities. This is difficult, because eventually focus is going to find the same problems over and over (a variant of the pesticide principle), while casting the net wider makes it difficult to reach the coverage you might be looking for.

Not in conclusion, really...
=======================================
Signals based testing has been evolving experience for me for the last eight years or so. It has taken a while building up the capability to even collect the signal during our automation runs (a story for another time). It has taken even longer building tools and methods to generate the workload and adapt the regular customer signal for engineering usage. I share my ideas right now to start a conversation, see what others are doing with it. I believe that especially as software evolves, and development models evolve, we will see new skills and practices around observability and customer analysis that will drive change in the signals-based testing methods I describe here.

_Thank you to James Bach, who helped me with some of the wording on this piece. In particular, getting the subtle distinctions expressed in the definition of signals-based testing to keep it distinct from other kinds of testing._
