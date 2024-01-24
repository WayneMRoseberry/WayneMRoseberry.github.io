Sometimes products transform complex data. The challenge
of testing complex data is in the difficulty of anticipating
what a correct transformation looks like. We can determine
what we believe it would be on a case by case basis, which works
well when we have a identified some specifically interesting
conditions to check, but it does not work well when we are
looking at a very large set of possible transformations and
we use something like data generation tools to create the inputs
for us. We need faster, generalized testing approaches that can
help find problems over large sets of inputs without having
to anticipate the exact correct state for all of them.

One technique that works well is round-tripping.

Round Tripping Data Transformations
===========================================
Round tripping works in the case where the product transforms
inputs in both directions, when the transformation can be
reversed for at least some set of the input conditions. This
is a common functionality, where most conversion libraries
have something like "AToB" paired with "BToA".

The technique assumes a high probability that if one
correctly makes either conversion that a round trip comparison
will yield equivalent values. In other words, `X == BToA(AToB(X))`.

The technique is not perfect.

This approach only works if the conversion is reversible.
If there are multiple possible values for A that map to a single
value for B, then the round trip is likely to come back to only
a single value of A. This breaks cases where explicit comparison
demands an exact equivalence, such as with string inputs.

It also does not detect incorrect between state
conversions that are lost when converting back. For example, if when
executing `Y = AToB(X)` some extra value was added to Y that was
lost on `Z = BToA(Y)` and thus a check of `X == Z` would return
true, even though there was a bug reflected in Y.

Those, and other, limitations aside, what it does do is
offer an easy way of checking the transformation of a lot
of highly complex data without having to build complex checking
mechanisms. It is a simple heuristic (a term I picked
up from a keynote speech by Harry Robinson at PNSQC one year).

Product Example: Cucumber string conversions
============================================
Imagine the following conversion methods for
converting back and forth between strings in the
Cucumber langage and an object called BDDRuleSet.

Let's pretend these methods are used in a BDD
tool that accepts user inputs as Cucumber, does
something with them, and displays sets of BDD
rules to the user in Cucumber format.
```
// given a string in Cucumber 'GIVEN... WHEN...' format, return a BDDRuleSet object
public BDDRuleSet ConvertCucumberStringToBDDRuleSet(string cucumberString);

// given a BDDRuleSet object, return a Cucumber format string
public string ConvertBDDRuleSetToCucumberString(BDDRuleSet bDDRuleSet);

// returns true if ruleSet1 and ruleSet2 have the same value
public bool BDDTools.RuleSetsEquivalent(BDDRuleSet ruleSet1, BDDRuleSet ruleSet2);
```

Checking the conversion with round-trips
------------------------------------------
We want to test the above across a large set of
possible Cucumber and BDDRuleSet inputs. We write a test method that
looks like this:

```
public void CheckCucumberRoundTrip()
{
  // testDocLibrary initialized on test class startup
  var testDocs = testDocLibrary.FetchTestFiles(CUCUMBERRULESETFILES);
  Assert.AreNotEqual(0, testDocs.Count, $"Fail if we have no test inputs. Filter:{CUCUMBERRULESETFILES}";
  foreach(var testDoc in testDocs)
  {
    var testDocCucumber = testDoc.ReadAllText();
    BDDRuleSet ruleSet = BDDTools.ConvertCucumberStringToBDDRuleSet(testDocCucumber);
    Log.Information($"In between BDDRuleSet: {ruleSet.ToString()}");
    string roundTripCucumber = BDDTools.ConvertBDDRuleSetToCucumberString(ruleSet);
    Assert.AreEqual(testDocCucumber, roundTripCucumber, $"fail if testDoc '{testDoc.Filename}' does not round trip to same input.");
  }
}

public void CheckBDDRuleSetRoundTrip()
{
  // testDocLibrary initialized on test class startup
  var ruleSets = ruleSetRepository.FetchRuleSets(BDDRULESETSAMPLES);
  Assert.AreNotEqual(0, ruleSets.Count, $"Fail if we have no test inputs. Filter:{BDDRULESETSAMPLES}";
  foreach(var ruleSet in ruleSets)
  {
    string inBetweenCucumber = BDDTools.ConvertBDDRuleSetToCucumberString(ruleSet);
    Log.Information($"In between Cucumber: {inBetweenCucumber}");
    BDDRuleSet roundTripRuleSet = BDDTools.ConvertCucumberStringToBDDRuleSet(inBetweenCucumber);
    Assert.IsTrue(BDDTools.RuleSetsEquivalent(ruleSet, roundTripRuleSet), $"fail if ruleSet '{ruleSet.ToString()}' does not round trip to same input.");
  }
}
```

The above test methods do not work well on their own. They
rely on trusting that both `ConvertBDDRuleSetToCucumberString()` and 
'ConvertCucumberStringToBDDRuleSet()' do a basically correct
transformation. They especially rely on `RuleSetsEquivalent()` to work correctly.

Checking the checking with round-trips
-------------------------------------------
One way to check `RuleSetsEquivalent()` would be to alter the same set
of `BDDResultSet` and check if `RuleSetsEquivalent()` returns false. We
can also use a round-trip pattern for this check.
```
public void RuleSetsEquivalent_lotsofrulesets()
{
  // testDocLibrary initialized on test class startup
  var ruleSets = ruleSetRepository.FetchRuleSets(BDDRULESETSAMPLES);
  Assert.AreNotEqual(0, ruleSets.Count, $"Fail if we have no test inputs. Filter:{BDDRULESETSAMPLES}";
  int emptyRuleCounter = 0;
  foreach(var ruleSet in ruleSets)
  {
    string inBetweenCucumber = BDDTools.ConvertBDDRuleSetToCucumberString(ruleSet);
    if(string.IsNullOrEmpty(inBetweenCucumber) { emptyRuleCounter++};
    Log.Information($"In between Cucumber: {inBetweenCucumber}");
    BDDRuleSet roundTripRuleSet = BDDTools.ConvertCucumberStringToBDDRuleSet(inBetweenCucumber);
    Assert.IsTrue(BDDTools.RuleSetsEquivalent(ruleSet, roundTripRuleSet), $"fail if ruleSet '{ruleSet.ToString()}' does not round trip to same input.");

    ruleSet.Rules[0].WhenClause = ruleSet.Rules[0].WhenClause + "_alteration";
    inBetweenCucumber = BDDTools.ConvertBDDRuleSetToCucumberString(ruleSet);
    Log.Information($"Altered in between Cucumber: {inBetweenCucumber}");
    BDDRuleSet alteredRoundTripRuleSet = BDDTools.ConvertCucumberStringToBDDRuleSet(inBetweenCucumber);
    Assert.IsFalse(BDDTools.RuleSetsEquivalent(roundTripRuleSet, alteredRoundTripRuleSet), $"fail if altered ruleSet '{ruleSet.ToString()}' round trip is same as unaltered roundtrip.");
  }
  Assert.AreEqual(0, emptyRuleCounter, "Fail if we have any empty rules. We checked all the others, but there is some test data to correct. Empty count:{emptyRuleCounter}, Total rules: {ruleSets.Count}.");
}
```

Extending the technique
---------------------------------------------
Imagine we want to go beyond whatever samples we have collected ourselves.
Imagine we build a Cucumber randomizer, something capable of emitting
random, valid, Cucumber scripts. We could modify the test method as follows:

```
public void CheckCucumberRoundTrip_1millionrandom()
{
  // testDocLibrary initialized on test class startup
  for(int i = 0; i < 1000000 ; i++)
  {
    var testDocCucumber = CucumberRandomizer.MakeRandomCucumberString();
    BDDRuleSet ruleSet = BDDTools.ConvertCucumberStringToBDDRuleSet(testDocCucumber);
    Log.Information($"In between BDDRuleSet: {ruleSet.ToString()}");
    string roundTripCucumber = BDDTools.ConvertBDDRuleSetToCucumberString(ruleSet);
    Assert.AreEqual(testDocCucumber, roundTripCucumber, $"fail if does not round trip to same input.");
  }
}
```

The above might give us some false positives if any of the random Cucumber
strings convert back to a semantically equivalent, but maybe exact string different
input. That kind of problem is difficult with string input. We could change it up with a fuzzer
instead.
```
public void CheckCucumberRoundTrip_fuzzedcucumbersroundtriptooriginal()
{
  // testDocLibrary initialized on test class startup
  var testDocs = testDocLibrary.FetchTestFiles(CUCUMBERRULESETFILES);
  Assert.AreNotEqual(0, testDocs.Count, $"Fail if we have no test inputs. Filter:{CUCUMBERRULESETFILES}";
  foreach(var testDoc in testDocs)
  {
    // make a fuzzed version of each of the inputs
    for(int i = 0; i < 100; i++)
    {
      string testDocCucumber = testDoc.ReadAllText();
      string fuzzyCucumber = TestTools.CucumberFuzzerToSimilarString(testDocCucumber);
      Log.Information($"fuzzyCucumber:{fuzzyCucumber}");
      BDDRuleSet ruleSet = BDDTools.ConvertCucumberStringToBDDRuleSet(testDocCucumber);
      Log.Information($"In between BDDRuleSet: {ruleSet.ToString()}");
      string roundTripCucumber = BDDTools.ConvertBDDRuleSetToCucumberString(ruleSet);
      Assert.AreEqual(testDocCucumber, roundTripCucumber, $"fail if testDoc '{testDoc.Filename}' variation{i} does not round trip to original input.");
    }
  }
}
```

This method works on the assumption that the prior check, `RuleSetsEquivalent_lotsofrulesets`
passes if all the values in 'CUCUMBERRULESETFILES' round trip to the
same input string. We also rely on `CucumberFuzzerToSimilarString()` to fuzz
an output in a way that ought to create the same `BDDRuleSet` as its input string.
We would have to build those libraries, but that is why test
engineering is interesting.
