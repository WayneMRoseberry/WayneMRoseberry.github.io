UNDER CONTRUCTION...

I am commiting this early like a crazed maniac every few minutes
because github lost an hour of work on me earlier today. So if you see this
article, yeah, it is unfinished....


Multiple kinds of performance test require definition of a workload in order
to execute the test. The different performance tests where this might apply
would be any of the following (did I miss some?):
- __Stress__: Workload is applied to the product/system up to, and past the point where one or more resources are maximized and a failure point is reahed.
- __Volume Testing__: Workload is applied to the product/system at a below maximum level and sustained for an extended period of time.
- __Capacity Testing__: Worklaod is applied to the system until some measurement threshold starts to deterioriate, at which point the maximum capacity of the system is discovered.
- __Scalability Testing__: Resources are added to the system while workload is increased to see how adding that workload affects system capacity.
- __Load Performance__: Workload is applied to the system and performance/reliability measurements are taken, contrasted against performance measurements taken in isolation as single operations on an unloaded system.

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
bug accumulation in the build. I was checking what kinds of
results my simulator gave me, and I found performance degraded
quickly as simulations got larger and larger.

I didn't, and probably still don't, intend on doing any performance tests
or fixes against this tool, but it is small and simple enough to
provide an example of analyzing a workload, even for something
as humble as a single user desktop tool.

Build Simulator: Behavioral Summary
----------------------------------------------------
```
BuildSimulator:

Inputs:
 builds: number of builds to simulate, each with a prior build and a current build
 batchSize: number of change submissions per build - analogous to multiple developers submitting code to the same build
 factBatchSize: number of "facts", being behavior, state or attribute changes to the product per change submission
 breakProbability: probability that a given fact combination will manifest in a break
 breakMissDetectionProbability: probability that the given break will be missed (and not fixed as part of build)
 regressionProbability: probability that a given fact will cause a regression with a fact from a prior build
 regressionMissDetectionProbability: probability that the given regression will be missed (and not fixed as part of build)
```

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
