I had a tool with performance bugs, so I decided to write a performance testing specification for it.
=====================================================
![Cartoonish picture of a snail sitting on a performance testing specification, even if the title is spelled badly. There is hand holding a stopwatch, and another hand holding a pen over the paper.](/assets/slowtoolperformancetestspec.jpg)
_The prompt to Bing Image Creator is the same as the heading for this section of text. The other image were too conceptual, and I thought the snail was cute and funny._

I often write these articles based on whatever development or testing
activity I did this week. I often get weird code ideas in my head, especially
Monte Carlo simulations that demonstrate some point I want to make in some
discussion or debate somewhere. I had one of those this morning, and it was really
going nowhere at all, so the possibility of using it for one of these articles
seemed toast. But then the tool had a weird performance bug. And that reminded
me of one of the ways I think about performance problems.

I like looking for the variables that affect performance. Part of that is about
creating a workload, so I figured I would talk about that using my pointless
tool as an example.

Workloads are the heart of performance testing
=====================================================
Multiple kinds of performance test require definition of a workload in order
to execute the test. The different performance tests where this might apply
would be any of the following (did I miss some?):
- __Stress__: Workload is applied to the product/system up to, and past the point where one or more resources are maximized and a failure point is reached.
- __Volume Testing__: Workload is applied to the product/system at below maximum level and sustained for an extended period of time.
- __Capacity Testing__: Workload is applied to the system until some measurement threshold starts to deterioriate, at which point the maximum capacity of the system is discovered.
- __Scalability Testing__: Resources are added to the system while workload is increased to see how they affect system capacity.
- __Load Performance__: Workload is applied to the system and performance/reliability measurements are taken. This approach is in contrast to performance measurements taken in isolation as single operations on an unloaded system.

The workload usually consists of the following components, each of which are adjusted
or fixed to compare affect on the system.
- Operations: Actions taken against the system, in some mixed distribution of operations at some rate over time, and with different permutations that affect operation behavior
- Data: Entities that the product/system can process or store that vary in type, size, and content

In addition, the system or product may vary in terms of any of the following:
- Resources: Hardware, dependent systems, environment settings
- Product Configuration: Settings within the product itself that affect its behavior

We also identify things we will monitor or measure. There may be some operation where we measure response time, throughput rate, size. There may be some data signal which we watch for signs of failure, there may be resources we watch for capacity, usage rate, or indications of failure.

Frequently for performance testing we work from a hypotheses. We will indicate which
variables we believe will have a certain affect on the system. Those variables usually
come from the workload, although they may also any of the product or environment related
configurations.

Example Product: A build simulator
===================================================
I wasted most of my morning today writing a Monte Carlo style
tool to simulate the way batch size affects the probability of
bug accumulation in a product build. I was checking what kinds of
results my simulator gave me, and I found performance degraded
quickly as simulations got larger and larger.

I didn't, and probably still don't, intend on doing any performance tests
or fixes against this tool, but it is small and simple enough to
provide an example of analyzing a workload, even for something
as humble as a single user desktop tool.

Normally we run performance tests against larger and more
complex applications. Small examples make it easy to
introduce ourselves to an idea. The same principles scale up
as a product gets larger, the performance problems more
difficult and complex. In this example, we are looking at a single operation
as the tool only offers one. There is no data set other than the
parameters that affect operation behavior. The tool doesn't run
over long periods of time or service multiple users. Still, despite
those limitations, the form of the analysis is the same, just simpler.

Build Simulator: Behavioral Summary
----------------------------------------------------
> _I always want test specifications to be entirely
 about the test analysis and only the test analysis. Any
 word or sentence that doesn't say something about why, how
 or what of testing I ask for removal._
>
> _Ordinarily I tell a tester to remove information in a test
 specification that are word for word repeats of material already
 in design docs, product specs, user documentation, or source code.
 I tell them to point in a link to the document instead. I include it
 here because that documentation doesn't exist, and the article
 wouldn't make much sense without it here to reference. I may
 elaborate more on this point in a later article, with examples of how to differentiate
 test documentation from product designs and specs._

```
BuildSimulator:
 Simulates a series of builds, each take a new set of changes on top of a prior
 build, and returning the total number of uncaught breaks and regressions in the
 product, assuming a probability model of product behavior changes conflicting with
 each other.

Output: (non-exhaustive)
# builds, #total submissions, #total changes, break rate, total breaks, total regressions

Inputs:
 builds: number of builds to simulate, each with a prior build and a current build
 batchSize: number of change submissions per build - analogous to multiple developers submitting code to the same build
 factBatchSize: number of "facts", being behavior, state or attribute changes to the product per change submission
 breakProbability: probability that a given fact combination will manifest in a break
 breakMissDetectionProbability: probability that the given break will be missed (and not fixed as part of build)
 regressionProbability: probability that a given fact will cause a regression with a fact from a prior build
 regressionMissDetectionProbability: probability that the given regression will be missed (and not fixed as part of build)
```

Workload Analysis
--------------------------------------------

__Builds__:

Number of builds will vary to measure if increasing number of builds
while all other variables remaining static violates a linear increase
in runtime. Number of builds will generally remain static when testing
other variables.

__Batch Size & Fact Batch Size__:

Batch Size and Fact Batch Size are believed to independently increase
run time non-linearly, possibly exponentially, although the possibility
of a mathematical means of avoiding that has not been investigated. Ideally
runtime would increase linearly as each is changed (can we dream of an order 0
mathematical shortcut?). These two variables will be tested (static|variable),
(variable|static), (variable|variable) - where the latter is expected to increase
runtime exponentially.

__All other variables__:

All other variables are expected to have no effect on runtime length.
There will be two tests per each, increasing their values by some factor
in order to check the 0 effect hypothesis.

> _There is some uncertainty in the above material. We can test
> even when we do not know 100% what to expect. Sometimes the team is
> waiting for test results to answer such open questions._
>
> _It is also worth pointing out the above beliefs and assumptions may
> be incorrect. This is why we review specification material with
> our team._

Build Simulator Performance Test Hypotheses
================================================
In the case of each of the hypotheses, one or more variables are changing
while all others remain static. The measured variable is Run Time, which
is compared against a target, which will be measured prior to the
runs and used as a baseline. From there, each success target measurement is
compared to the prior row and adjusted as shown in the tables. The
baseline measurement is expected to be less than 100 milliseconds - more
than that will be considered a bug. Actual run time growth should be within
a 5% error margin of target run time.

__Build Increase Hypothesis__
> _Run time will grow linearly with the number of builds so long as all other variables remain static.

| Builds | Target Run Time | Actual Run Time| Batch Size | Fact Batch Size | Break Prob. | Break Detect Miss Prob. | Regress Prob. | Regress Detect Miss Prob. |
|---|---|---|---|---|---|---|---|---|
| 10 | measured  | measured | 1 | 1 | .5 | .5 | .5 | .5 |
| 20 | 2x prior  | -        | 1 | 1 | .5 | .5 | .5 | .5 |
| 40 | 2x prior  | -        | 1 | 1 | .5 | .5 | .5 | .5 |
| 80 | 2x prior  | -        | 1 | 1 | .5 | .5 | .5 | .5 |
| 160 | 2x prior | -        | 1 | 1 | .5 | .5 | .5 | .5 |
| 320 | 2x prior | -        | 1 | 1 | .5 | .5 | .5 | .5 |

__Batch Size Increase Hypothesis__
> _Run time will grow linearly with batch size so long as all other variables remain static.

| Builds | Target Run Time | Actual Run Time| Batch Size | Fact Batch Size | Break Prob. | Break Detect Miss Prob. | Regress Prob. | Regress Detect Miss Prob. |
|---|---|---|---|---|---|---|---|---|
| 10 | measured  | measured | 1  | 1 | .5 | .5 | .5 | .5 |
| 10 | 2x prior  | -        | 2  | 1 | .5 | .5 | .5 | .5 |
| 10 | 2x prior  | -        | 4  | 1 | .5 | .5 | .5 | .5 |
| 10 | 2x prior  | -        | 8  | 1 | .5 | .5 | .5 | .5 |
| 10 | 2x prior  | -        | 16 | 1 | .5 | .5 | .5 | .5 |
| 10 | 2x prior  | -        | 32 | 1 | .5 | .5 | .5 | .5 |

__Fact Batch Size Increase Hypothesis__
> _Run time will grow linearly with fact batch size so long as all other variables remain static.

| Builds | Target Run Time | Actual Run Time| Batch Size | Fact Batch Size | Break Prob. | Break Detect Miss Prob. | Regress Prob. | Regress Detect Miss Prob. |
|---|---|---|---|---|---|---|---|---|
| 10 | measured  | measured | 1 | 1  | .5 | .5 | .5 | .5 |
| 10 | 2x prior  | -        | 1 | 2  | .5 | .5 | .5 | .5 |
| 10 | 2x prior  | -        | 1 | 4  | .5 | .5 | .5 | .5 |
| 10 | 2x prior  | -        | 1 | 8  | .5 | .5 | .5 | .5 |
| 10 | 2x prior  | -        | 1 | 16 | .5 | .5 | .5 | .5 |
| 10 | 2x prior  | -        | 1 | 32 | .5 | .5 | .5 | .5 |


__Fact Batch Size and Batch Size Increase Hypothesis__
> _Run time will grow exponentially with fact batch size and batch size so long as all other variables remain static.

| Builds | Target Run Time | Actual Run Time| Batch Size | Fact Batch Size | Break Prob. | Break Detect Miss Prob. | Regress Prob. | Regress Detect Miss Prob. |
|---|---|---|---|---|---|---|---|---|
| 10 | measured     | measured | 1  | 1  | .5 | .5 | .5 | .5 |
| 10 | 4x prior     | -        | 2  | 2  | .5 | .5 | .5 | .5 |
| 10 | 16x prior    | -        | 4  | 4  | .5 | .5 | .5 | .5 |
| 10 | 64x prior    | -        | 8  | 8  | .5 | .5 | .5 | .5 |
| 10 | 256x prior   | -        | 16 | 16 | .5 | .5 | .5 | .5 |
| 10 | 1024x prior  | -        | 32 | 32 | .5 | .5 | .5 | .5 |

... and, that's enough hypotheses for now. I think you get the point. The important
point is that we have a methdology figured out, and other people
can review it. Maybe we are planning some performance tests that
are not as important as other problems. Maybe there is a riskier
performance question not covered in our plan. This is how we
talk about priorities and business risk.

It's not only about defining the variables, but I like this part of it.
========================================================
There is a lot more to the craft of performance testing, especially
in the tools, impact of the environment, behavior of different technologies
(boy oh boy, HTML page load ordering, that is a fun one), and
very specific performance testing methodologies. It is so specialized
that some people have made their entire career out of performance
engineering and testing.

If you enjoy that kind of thing, there is a lot of fun to be had there.

But all that aside, my favorite part of performance testing is what
I described here. Taking the problem apart conceptually by selecting
the variables that I believe impact performance, and coming up with
a way to explore what happens as those variables change. The workload
is a highly re-usable part of the performance analysis. You will likely
find that once you have defined different flavors of workload for your
product or system that you go back to it to solve a lot of problems.

Learning more
=========================================================
The tables I used for laying out anticipated results that check
performance hypotheses follow a technique I describe in my book
<a href="https://www.amazon.com/Writing-Test-Plans-Made-Easy/dp/1478333693">"_Writing Test Plans Made Easy_", available on Amazon</a>.

<a href="https://www.amazon.com/Writing-Test-Plans-Made-Easy/dp/1478333693"><img src="/assets/writingtestplanscover.jpg" width="400" /></a>

I cover several other ways of taking apart testing problems
and representing them in compact, easy to understand form. The
book is short, inexpensive, and an easy read. I wrote it especially
to target testers that might be comfortable writing big bulky documents,
or for anybody look for faster, easier ways to describe and think about
testing.
