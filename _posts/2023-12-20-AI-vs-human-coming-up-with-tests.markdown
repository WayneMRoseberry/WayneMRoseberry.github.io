Two testers and an AI comparing cases
================================================================
![A cartoon depicting two testers at computers across from an anthropic AI figure](/assets/twotestersversusai.jpg)

In a recent article, I describe some 
<a href="https://waynemroseberry.github.io/2023/12/18/Diary-of-a-developer-making-some-time-for-test-lists.html">
tests I created while writing some code</a>, comparing them to tests I created a day
later and some tests created by another tester.

I also asked ChatGpt to create tests for the same problem. I was
curious what it would be like to compare the different lists.

What I found:
- Between myself and another tester, we came up with 22 different test ideas
- I came up with 9 ideas the other tester did not come up with
- The other tester came up with 8 ideas I did not come up with
- ChatGpt came up with 16 ideas neither of us came up with
- ChatGpt repeated most everything we said, several times
- After two iterations of re-prompting, ChatGpt started repeating most of its prior ideas, several times

The test problem is a simple API testing problem. The ideas
from ChatGpt were relevant and useful. Given more time and a
chance for back and forth questioning, I am fairly confident
that all parties in the experiment would have come to the same
set of test ideas.

The idea contribution from AI was not bad. Not brilliant, but not
bad. The exercise so short (10 minutes) that the time saved by
ChatGpt (9 minutes) is small compared to the follow up time it would
take to remove duplicates, find the unique contributions. But even with
that, the exercise give some support to the notion of LLMs as tools
for accelerating up idea generation.

The contrived and artificial conditions of the experiment
================================================================
This kind of "race the AI" test is not a realistic comparison because
the people are not working under realistic conditions. Their real skills
are hobbled and crippled by the context. Likewise, the
prompting method I used is not indicative of an approach that underwent
tuning and optimization. As many people are hopefully learning, prompt writing is
not like talking to a human being. It is a form of programming with
its own subtleties necessary to get a desired result.

I limited myself and the other tester to 10 minutes to come up with
test ideas for a method I had created.

I constrained myself and the other tester to only consider one method,
and one specific usage of that method.

The tester knew no more about the method than the name of the method,
its purpose, and a description of the data structures it used. They
were not given an opportunity to ask me any clarifying questions or
to explore application behavior.

ChatGpt was given a prompt describing the problem, and after
every response was given the prompt "_please continue with more test ideas_".
This was repeated until one minute had elapsed.

The question presented to all
=================================================================
Below is the prompt - the only change between then prompt and what I asked
a human to respond to is that I constrained the human to 10 minutes
coming up with test ideas:
>Come up with a list of test ideas for the given method.
>For sake of the exercise, pretend that the tests are to focus on the case where SchemaElement.Type = ElementType.Choice. Pretend the developer has just added the "Choice" type, and the tests are to focus on how GetRandomExample deals with it.
>
>```
>/// <summary>
>/// Produces a valid random string where the requirements
>/// for the data string are described in the DataSchema object
>/// passed to the method.
>/// </summary>
>/// <param name="schema">Describes the requirements for a valid data object.</param>
>/// <returns>A random string that is valid according to the schema.</returns>
>/// <exception cref="ArgumentException">In the case where any element in schema is invalid.</exception>
>public static string GetRandomExample(DataSchema schema)
>
>data type definitions are provided below.
>
>public class DataSchema
>{
>	public string Name;
>	internal IList<SchemaElement> Elements;
>	public void AddElement(SchemaElement element)
>}
>
>public class SchemaElement
>{
>	public string Name { get; set; }
>	public object Value { get; set; }
>	public object MinValue { get; set; }
>	public object MaxValue { get; set; }
>	public ElementType Type { get; set; }
>}
>
>public enum ElementType
>{
>	StaticValue, // the string value of the Value element is returned
>	Choice, // the return value is randomly selected from an array of choices in the Value element
>	Optional, // randomly return either an evaluation of Value or string.Empty
>	ElementList, // the Value element represents a list of SchemaElement objections
>	RangeNumeric // randomly return a numeric value between MinValue and MaxValue, inclusive
>}
>```

Two humans take a swing at the tests
============================================

My list
> GetRandomExample: Choice type
> - single choice
> - double choice
> - n choices
> - choice of different element types -> each of one
> - choice of recursive item
> - choice of resursion with non-terminating loop
> - choice of invalid item (e.g. ranges with crossed min/max values)
> - choice of non-existent reference
> - is every choice eventually picked -> is random range truly respected
> - how "random" is the distribution?
>
>   - perfectly distributed? (seems should not be)
>   - ordered? (seems should not be)
>   - predictable? (seems should not be)
> - load -> can we generate over and over without failure

A list I got from a tester friend of mine
> 1. Array lengths - shortest, very long, empty
> 2. Other properties in schema being short/long/empty/null should not affect it (or should?)
> 3. Security requirements (not a security expert) - does it protect against injection
> 4. Checking the implementation of "random" by running it repeatedly and making sure it returns different results
> 5. Checking that the randomness will not consistently skip certain locations within the array
> 6. Checking different formats of elements in the array, they could be strings, arrays, lists, ints, custom objects
> 7. How do you convert from the objects in the array to string format - tests around those
> 8. The arrays are in a list - use different list lengths, 0, 1, many
> 9. Arrays of different object types within the same list
> 10. How does it combine the end value when there are multiple elements in the list
> 11. Is the randomness consistent across multiple elements in the list

When you compare my ideas with those from the second tester,
(using my own intuitive biased way of counting what are and are not similar)
we shared 5 ideas between us, 9 only from me, 8 only from the other tester.

AI gets its turn
==============================================
This is ChatGpt's response:
>ChatGPT
>
>Certainly! Testing the GetRandomExample method with the new Choice type involves ensuring that it correctly handles the random selection of values from an array of choices. Here are some test ideas to consider:
>
>Basic Functionality Test:
>
>Scenario: Provide a DataSchema with a single element of type Choice.
>Expectation: The returned string should be one of the choices specified in the Value property of the SchemaElement.
>Multiple Choices Test:
>
>Scenario: Provide a DataSchema with an element of type Choice containing an array of multiple choices.
>Expectation: The returned string should be one of the choices specified in the Value property of the SchemaElement.
>Empty Choices Test:
>
>Scenario: Provide a DataSchema with an element of type Choice containing an empty array of choices.
>Expectation: An ArgumentException should be thrown since there are no choices available.
>Null Choices Test:
>
>Scenario: Provide a DataSchema with an element of type Choice where the Value property is set to null.
>Expectation: An ArgumentException should be thrown since there are no choices available.
>Mixed Type Choices Test:
>
>Scenario: Provide a DataSchema with multiple elements, including some of type Choice and others of different types.
>Expectation: Ensure that only elements of type Choice are considered for random selection.
>Repeated Execution Test:
>
>Scenario: Call the GetRandomExample method multiple times with the same DataSchema.
>Expectation: The returned string should be different on each invocation, indicating random selection.
>Large Choices Array Test:
>
>Scenario: Provide a DataSchema with a large array of choices in the Value property of an element of type Choice.
>Expectation: The method should handle and randomly select from a large array of choices efficiently.

So far all of the above are good test ideas that make sense
for the feature. They also all match against something said
by our human testers.

>Negative MinValue Test:
>
>Scenario: Provide a DataSchema with an element of type Choice where MinValue is a negative number.
>Expectation: Ensure that the method handles negative MinValue appropriately and still selects choices from the array.

The above is not mentioned by myself or the other tester, although
it is an interesting twist on invalid values. It is specified if
MinValue should be anything when Choice is set, which makes this an interesting
test idea. Vague specifications sometimes introduce bad behavior.

+1

>Choice Array with Other Types Test:
>
>Scenario: Provide a DataSchema with elements of type Choice and other types in the Value property.
>Expectation: Ensure that the method correctly handles elements of type Choice while ignoring others.

Also matches what was said above.

>Large Number of Choices with Repeated Values Test:
>
>Scenario: Provide a DataSchema with a large array of choices that includes repeated values.
>Expectation: Confirm that the method still randomly selects from the choices, even if some values are repeated.

This one is an interesting test to check for randomness.
+1

>Invalid Type for Choices Test:
>Scenario: Provide a DataSchema with an element of type Choice but with choices of an incompatible type.
>Expectation: An ArgumentException should be thrown, indicating that the choices are of an invalid type.
>These test scenarios cover a range of cases to ensure that the GetRandomExample method functions correctly with the new Choice type.
>
>User
>
>please continue with more test ideas
>
>ChatGPT
>
>Certainly! Here are some additional test ideas to further cover various scenarios for the Choice type in the GetRandomExample method:
>
>Nested Choices Test:
>
>Scenario: Provide a DataSchema with an element of type Choice where one or more choices are themselves elements of type Choice.
>Expectation: The method should handle nested choices and select from the inner choices as needed.
>Choice with StaticValue Test:
>
>Scenario: Include elements of type StaticValue within the choices array of an element of type Choice.
>Expectation: Confirm that the method handles the presence of StaticValue elements within the choices array.
>Choice with ElementList Test:
>
>Scenario: Include elements of type ElementList within the choices array of an element of type Choice.
>Expectation: Ensure that the method handles the presence of ElementList elements within the choices array.
>Choice with RangeNumeric Test:
>
>Scenario: Include elements of type RangeNumeric within the choices array of an element of type Choice.
>Expectation: Confirm that the method handles the presence of RangeNumeric elements within the choices array.
>Choice with Optional Test:
>
>Scenario: Include elements of type Optional within the choices array of an element of type Choice.
>Expectation: Ensure that the method handles the presence of Optional elements within the choices array.

The above are all repeats of something said prior, although they take
the form of specifically mentioning every class rather than generalizing as
"Choice with one of each element type"

>Choice within ElementList Test:
>
>Scenario: Create a DataSchema with an element of type ElementList where each element in the list is of type Choice.
>Expectation: Confirm that the method handles the combination of ElementList and Choice types.

This one is different rather than considering a Choice element and
then what is in the Value for Choice, this one considers having Choice be
the value for something else.

+1

>Boundary Testing for Choices Array:
>
>Scenario: Provide a DataSchema with an element of type Choice where the choices array has the minimum and maximum allowed number of elements.
>Expectation: Verify that the method handles the edge cases of the choices array size.

Prior cases mentioned boundary on number of choices.

>Concurrency Test:
>
>Scenario: Simulate concurrent calls to the GetRandomExample method with the same DataSchema.
>Expectation: Ensure that the method behaves correctly under concurrent access, providing different random examples without conflicts.

I am going to be conservative and call this similar to load testing.

>Performance Test:
>
>Scenario: Create a DataSchema with a large number of elements, including multiple elements of type Choice.
>Expectation: Evaluate the performance of the method by measuring the time taken for generating random examples and ensure it meets acceptable performance criteria.

Prior test ideas did not include data size as a performance characteristic.

+1

>These additional test scenarios aim to explore more complex situations and edge cases to ensure the robustness and correctness of the GetRandomExample method, especially with the new Choice type.
>
>User
>
>please continue with more test ideas
>
>ChatGPT
>
>Certainly! Here are some more test ideas for the GetRandomExample method focusing on the Choice type:
>
>Combination of Element Types in Choices Test:
>
>Scenario: Create a DataSchema with an element of type Choice where the choices array includes elements of various types (e.g., StaticValue, RangeNumeric, Optional).
>Expectation: Verify that the method can handle and randomly select from a mix of element types within the choices array.
>Choice with Duplicates Test:
>
>Scenario: Include duplicate choices in the choices array of an element of type Choice.
>Expectation: Confirm that the method can handle duplicate choices and still randomly select from them.
>Empty DataSchema Test:
>
>Scenario: Provide an empty DataSchema (no elements).
>Expectation: The method should throw an ArgumentException since there are no elements to generate a random example.
>Choice with ElementList of Choices Test:
>
>Scenario: Create a DataSchema with an element of type Choice where the choices array includes an element of type ElementList, and each element in the list is of type Choice.
>Expectation: Confirm that the method handles nested choices within an ElementList correctly.
>Mixed Data Types in Choices Test:
>
>Scenario: Include choices of different data types (e.g., strings, integers, booleans) in the choices array of an element of type Choice.
>Expectation: Ensure that the method handles diverse data types within the choices array and selects randomly based on the specified types.
>Choice with RangeNumeric and Optional Test:
>
>Scenario: Include elements of type RangeNumeric and Optional in the choices array of an element of type Choice.
>Expectation: Verify that the method correctly handles a combination of RangeNumeric and Optional elements within the choices array.
>Choice with Invalid Value Test:
>
>Scenario: Provide an element of type Choice with an invalid Value (e.g., a non-array object).
>Expectation: The method should throw an ArgumentException due to an invalid value type.
>Choice with Null Element in Choices Array Test:
>
>Scenario: Include null as one of the choices in the choices array of an element of type Choice.
>Expectation: Confirm that the method handles null values within the choices array and does not throw exceptions.

The above all either repeat prior ideas from ChatGpt or
human testers list.

>Choice with ElementList of Empty Choices Test:
>
>Scenario: Create a DataSchema with an element of type Choice where the choices array includes an element of type ElementList with an empty list of choices.
>Expectation: The method should throw an ArgumentException since there are no choices available in the ElementList.

This is a new idea.
+1

>Choice with Large Choices Array and Performance Test:
>
>Scenario: Provide a DataSchema with an element of type Choice where the choices array is very large.
>Expectation: Evaluate the performance of the method and ensure that it remains within acceptable performance thresholds even with a large choices array.

This one is ChatGpt repeating itself.

>These test scenarios aim to cover a wide range of situations, including edge cases and potential issues, to thoroughly validate the behavior of the GetRandomExample method with the new Choice type.
>
>User
>
>please continue with more test ideas
>
>ChatGPT
>
>Certainly! Here are additional test ideas for the GetRandomExample method, specifically focusing on the Choice type:
>
>Choice with Choices of Varying Length Test:
>
>Scenario: Create a DataSchema with an element of type Choice where the choices array includes arrays of different lengths.
>Expectation: Confirm that the method handles choices of varying lengths appropriately and selects from them without issues.

Varying lengths in the same choice list is a different idea.
+1

>Choice with Choices of Different Data Types Test:
>
>Scenario: Include choices of different data types (e.g., strings, integers, booleans) in the choices array of an element of type Choice.
>Expectation: Ensure that the method handles diverse data types within the choices array and selects randomly based on the specified types.
>Choice with Choices Containing Null Test:
>
>Scenario: Include null as one of the choices in the choices array of an element of type Choice.
>Expectation: Confirm that the method handles null values within the choices array and does not throw exceptions.

ChatGpt repeating itself.

>Choice with Choices of Different Min/Max Values Test:
>
>Scenario: Create a DataSchema with an element of type Choice where the choices array includes elements with different MinValue and MaxValue properties.
>Expectation: Verify that the method considers the individual MinValue and MaxValue properties of each choice when making random selections.

This one is new, although similar to the attempt at invalid negative MinValue.
+1

>Choice with Choices of Various Element Types Test:
>
>Scenario: Include elements of various types (e.g., StaticValue, RangeNumeric, Optional) in the choices array of an element of type Choice.
>Expectation: Confirm that the method correctly handles choices with diverse element types and randomly selects from them.
>Choice with Choices Containing Duplicates Test:
>
>Scenario: Include duplicate choices in the choices array of an element of type Choice.
>Expectation: Verify that the method can handle duplicate choices and still randomly select from them.

ChatGpt repeating itself

>Choice with Choices of Large Strings Test:
>
>Scenario: Include large strings as choices in the choices array of an element of type Choice.
>Expectation: Ensure that the method can handle and randomly select from large strings without performance issues.
>Choice with Choices Containing Special Characters Test:
>
>Scenario: Include choices in the choices array of an element of type Choice that contain special characters.
>Expectation: Verify that the method correctly handles and randomly selects from choices with special characters.
>Choice with Choices Containing Whitespaces Test:
>
>Scenario: Include choices in the choices array of an element of type Choice that contain leading/trailing whitespaces.
>Expectation: Confirm that the method trims whitespaces and randomly selects from choices without considering the whitespaces.
>Choice with Choices Containing Empty Strings Test:
>
>Scenario: Include empty strings as choices in the choices array of an element of type Choice.
>Expectation: Ensure that the method can handle and randomly select from choices that include empty strings.

Each of these are specific examples of special values in the
strings assigned to the Value property, each new. I am giving
one point each.
+4

>These additional test scenarios aim to cover a variety of scenarios, including data types, lengths, special characters, and potential edge cases that may arise when working with the Choice type in the GetRandomExample method.
>
>User
>
>please continue with more test ideas
>
>ChatGPT
>
>Certainly! Here are more test ideas for the GetRandomExample method, focusing on the Choice type:
>
>Choice with Choices of Large Numeric Range Test:
>
>Scenario: Include choices in the choices array of an element of type Choice that represent a large numeric range (e.g., [Int32.MinValue, Int32.MaxValue]).
>Expectation: Confirm that the method handles large numeric ranges and randomly selects values within the specified range.

Min and Max numeric range at same time is a new idea
+1

>Choice with Choices Containing Unicode Characters Test:
>
>Scenario: Include choices in the choices array of an element of type Choice that contain Unicode characters.
>Expectation: Verify that the method correctly handles and randomly selects from choices with Unicode characters.

I am treating this like a duplicate because ChatGpt already said "special characters" without
specificity, and not much different is implied here either. After one says "special characters"
I give no further points until they specify which special characters with a reason why.

>Choice with Choices of Different Case Test:
>
>Scenario: Include choices in the choices array of an element of type Choice that vary in case (e.g., "Choice1", "choice1", "CHOICE1").
>Expectation: Confirm that the method is case-sensitive and treats differently cased choices as distinct options.

This is a new case, and one I probably would not have introduced because I know
how the code (_currently_) works and my brain just assumes it is meaningless.
+1

>Choice with Choices of DateTime Values Test:
>
>Scenario: Include DateTime values as choices in the choices array of an element of type Choice.
>Expectation: Ensure that the method handles DateTime values within the choices array and randomly selects dates appropriately.
>Choice with Choices Containing Escape Characters Test:
>
>Scenario: Include choices in the choices array of an element of type Choice that contain escape characters (e.g., "\n", "\t").
>Expectation: Verify that the method correctly handles and randomly selects from choices with escape characters.
>Choice with Choices Containing HTML Tags Test:
>
>Scenario: Include choices in the choices array of an element of type Choice that contain HTML tags.
>Expectation: Confirm that the method handles and randomly selects from choices with HTML tags without issues.

Getting specific on types of characters. I am giving points for each
category.
+3

>Choice with Choices of Different Data Structures Test:
>
>Scenario: Include choices in the choices array of an element of type Choice that represent different data structures (e.g., arrays, dictionaries).
>Expectation: Ensure that the method can handle and randomly select from choices with diverse data structures.

This is technically a duplicate of something the other tester mentioned
in notes about tests - because Value is of type Object, it can accept
anything - so how is the method going to handle it.

A very clever case, because it is tempting to dismiss it, but it is legitimate.

>Choice with Choices Containing Control Characters Test:
>
>Scenario: Include choices in the choices array of an element of type Choice that contain control characters (e.g., ASCII control characters).
>Expectation: Verify that the method correctly handles and randomly selects from choices with control characters.
>Choice with Choices Containing Long Strings Test:
>
>Scenario: Include very long strings as choices in the choices array of an element of type Choice.
>Expectation: Confirm that the method can handle and randomly select from very long strings without performance issues.

ChatGpt repeating itself.

>Choice with Choices of Different Encodings Test:
>
>Scenario: Include choices in the choices array of an element of type Choice that are encoded differently (e.g., UTF-8, UTF-16).
>Expectation: Ensure that the method can handle and randomly select from choices with different encodings.

It finishes with a special category of special characters not previously mentioned.
+1

>These additional test scenarios continue to explore various aspects of the Choice type, considering different data types, encodings, and scenarios that may affect the behavior of the GetRandomExample method.

Total ChatGpt only test ideas: 16

Nobody's job is being replaced, but they might save some time typing
========================================
I see some value in this exercise, but nothing I would call a game changer.

Mostly helpful idea generation, prompts for thinking, and maybe some
time saved typing.

I do see the possibility that other people, putting more time
into tools that aide in the LLM prompting and input collection
might make something even more useful of it.

But in all those cases, what I really see is people in
charge of the process, using the tools to help.
