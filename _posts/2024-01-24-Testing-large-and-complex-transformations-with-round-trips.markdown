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
