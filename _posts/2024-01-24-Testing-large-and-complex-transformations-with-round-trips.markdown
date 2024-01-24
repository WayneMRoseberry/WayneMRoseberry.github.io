
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
possible Cucumber inputs. We write a test method that
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
