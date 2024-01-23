
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
