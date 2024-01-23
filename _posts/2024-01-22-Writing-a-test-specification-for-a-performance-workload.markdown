Multiple kinds of performance test require definition of a workload in order
to execute the test. The different performance tests where this might apply
would be any of the following:
- __Stress__: Workload is applied to the product/system up to, and past the point where one or more resources are maximized and a failure point is reahed.
- __Volume Testing__: Workload is applied to the product/system at a below maximum level and sustained for an extended period of time.
- __Capacity Testing__: Worklaod is applied to the system until some measurement threshold starts to deterioriate, at which point the maximum capacity of the system is discovered.
- __Scalability Testing__: Resources are added to the system while workload is increased to see how adding that workload affects system capacity.
- __Load Performance__: Workload is applied to the system and performance/reliability measurements are taken, contrasted against performance measurements taken in isolation as single operations on an unloaded system.



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
