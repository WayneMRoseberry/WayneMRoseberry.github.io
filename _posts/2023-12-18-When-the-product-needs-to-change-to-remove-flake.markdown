The test needs control
===================================
![A cartoonish looking image of a person using a stylus to change code on a computer screen](/assets/changeproductcodetofixflakytest.jpg)
I have an example of a flaky test which I wrote just
yesterday. I have seen it demonstrate flaky behavior, and
I addressed it not by removing the flaky behavior, but by
changing the test procedure so that the flake happens less
often.

That is bothering me. I want to change it. I am going to change
it and talk about the change in the article.

Let's clear up some terminology
===================================
"Flaky test" is a misnomer.
"Test which I wrote", by some people's definition of the word
'test' is also a misnomer.

What I wrote was a method in C# which I use to check
a method in a project I am working on. I run that check
when I test the method. When used as a noun, a test is
an event.

The "flake" pertains to the results. When I executed the
check, it did not give the same results every time. Even when
I had not changed the product code, it would sometimes yield
a PASS, and sometimes yield a FAIL. The results are inconsistent.

"Flaky tests" really means "inconsistent results."

The flake in action
===================================
The test is a check of the method __GetRandomExample()__, which takes
a __DataSchema__ object as input and returns a valid random string based
on that schema. One of the options in the schema allows a __SchemaElement__
in that schema to have a set of choices, where __GetRandomExample()__ should choose
from the elements in the list. The point of the check is to see if
give N choices (4 in this case), does __GetRandomExample()__ consider all
N choices - will it eventually pick all of them?

The challenge is that the method as written is fundamentally random. Which
choice it uses cannot be controlled. My first pass at the method used repetition
to increase the probability that __GetRandomMethod()__ would eventually pick
all 4 items. It calls the method 20 times, and then checks that each of the
values in the choice elements were returned at least once.

```
[TestMethod]
public void GetRandomExample_choicefourvalues()
{
  DataSchema schema = new DataSchema();
  SchemaElement[] elemArray = new SchemaElement[]
  {
    new SchemaElement () { Name="choice1", Value="ch1", Type=ElementType.StaticValue },
    new SchemaElement () { Name="choice2", Value="ch2", Type=ElementType.StaticValue },
    new SchemaElement () { Name="choice3", Value="ch3", Type=ElementType.StaticValue },
    new SchemaElement () { Name="choice4", Value="ch4", Type=ElementType.StaticValue }
  };
  SchemaElement element = new SchemaElement()
  {
    Name = "element1",
    Value = elemArray,
    Type = ElementType.Choice
  };
  schema.AddElement(element);
  List<string> results = new List<string>();
  for(int i =0; i < 20; i++)
  {
    string result = DataMaker.GetRandomExample(schema);
    if(!results.Contains(result))
    {
      results.Add(result);
    }
  }
  Assert.IsTrue(results.Contains("ch1"),"Fail if results does not contain ch1");
  Assert.IsTrue(results.Contains("ch2"), "Fail if results does not contain ch1");
  Assert.IsTrue(results.Contains("ch3"), "Fail if results does not contain ch1");
  Assert.IsTrue(results.Contains("ch4"), "Fail if results does not contain ch1");
}
```

Using this method, I successfully found an off-by-one bug in
the code (I make those mistakes all the time, I lean heavily on my
unit tests for that).

I then selected "Run until fail" in the test explorer window in Visual Studio.
This executes the selected test methods up  to 1000 times, and stops if
they fail. The 128th iteration of the test method failed. I knew there
would be flake in the results, but I wasn't sure how likely it was. I decided
1/128 was too much. I changed the code to iterate 40 times as follows, and this ran 1000
with consistent results...

```
  for(int i =0; i < 40; i++)
  {
    string result = DataMaker.GetRandomExample(schema);
    if(!results.Contains(result))
    {
      results.Add(result);
    }
  }
```

There is still flake waiting to happen, it just happens
less frequently.

Isn't that good enough?
===================================
This is my project, so maybe it doesn't matter, at least not as far as this
test method goes.

Control and testing
----------------------------------
But the flake means the test procedure has lost control of the test conditions.
I make a special point of saying "test procedure" because the loss of control
whether we are writing code to test or not. If this was some application with
buttons to push instead of an API to call, the person testing would still be
unable to control the test condition.

Loss of control is an interesting problem in testing. Sometimes as testers we
want to surrender control to the system to create a more unpredictable set of
conditions. This is a technique that works well when we are not sure what
we are looking for and instead are seeking ways to let failure shake itself out.

But when we are checking something, we almost always want to know exactly
what we are checking for. We want to know the conditions under test, we want
to know the inputs to the system, the outputs and everything about the system
state relative to that check. If we cannot control these things we cannot
make a deterministic check. Deterministic checks are important for things like
unit tests, build validation, CI/CI pipelines or any other process where we
cannot afford false positives or false negatives.

Bad code smells and tight coupling
----------------------------------
Beyond testing, a test condition you cannot control is usually a sign
of poorly written code. It is bad enough there is behavior in the code
that cannot be deterministically checked. That also means there is
behavior in a single method relying on something it cannot control
either. This suggests an internal tight coupling of two different purposes
which might be better to separate and couple more loosely. The code
is inflexible. It is difficult to change. A loose coupling would
make it easier to change.

Rethinking __GetRandom...__
==================================
The problem is that producing random values from the __DataSchema__ object
is hard-coded into the method. There is no way to externally override
the random behavior and control it. There is also no way to change
the method so it does something other than produce a random value. Maybe
what we want is the ability to enumerate over all possible values for
__DataSchema__ and each call to the method would get the next. We need a
way to control that behavior.

Make GetRandomValue more generalized
===================================
My plan is to do the following:
1. Rename the method to __GetValue()__ (Visual Studio makes this very easy, and the code has not been released yet, so it doesn't break anything to do the rename)
2. Introduce an interface that can be called to return values in ranges, passing an instance of that interface to the method
3. Implement a version of the interface which imitates the current random number behavior in the existing code and call it instead of the hard-coded call to a Rand object
4. During test, mock the interface so that it returns exactly the value we need for testing

I am still thinking about this plan. I haven't started on it yet.
In particular, I am not sure if I want to create an interface or if
I want to use simpler means, such as passing a delegate to the method
instead. It will depend on how much utility I believe I will eventually
load into that interface later.

Make an interface
-----------------------------------
The prior code used a static object of type Random...

```
static Random rand = new Random();
```

And then would use it later like so:

```
if (element.Type.Equals(ElementType.Choice))
{
  SchemaElement[] elements = (SchemaElement[])element.Value;
  int chosen = __rand.Next(elements.Length)__;
  result = EvaluateElement(result, elements[chosen]);
}
```

I extracted the calls to rand.Next into a new method and replaced all calls to __rand.Next__
with this new method.

```
public int ChooseNumber(int length)
{
  return rand.Next(length);
}
```

I then pulled this method and the object __rand__ into a new class.
This got a little bit messy (not shown here) because the original
methods were all static, and you cannot define an interface
around static methods. I made the new class (__RandomChooser__)
non-static and ChooseNumber as a member of that instance. 

```
public class RandomChooser : IChooser
{
   static Random rand = new Random();

  public int ChooseNumber(int length)
  {
    return rand.Next(length);
  }
}
```

I then extracted an interface from the RandomChooser class.

```
public interface IChooser
{
  int ChooseNumber(int length);
}
```

Change everything in product to use the new interface
----------------------------------
I want to keep the original methods static, and I haven't built
much code around the methods, so I a bunch of stuff to break
as I made modifications. Normally I keep everything working
by building overloads of a method so I can pass the new
interface and objects into a new version of the method, and
create an instance of it in the original method. This
allows me to use the automatic refactoring tools in Visual
Studio very nicely. But it got very messy based on the
way I was ordering my changes.

I added IChooser as a parameter to __GetExample()__ (_previously
GetRandomExample_).

```
public static string GetExample(DataSchema schema, IChooser chooser)
```
There are several private methods which were now also calling
__ChooseNumber__, so I had to add IChooser to their calling
signatures as well.

Changing the tests
----------------------------------
I was going to go immediately to mocking an IChooser interface,
but the pattern I always use is to throw a _NotImplementedException__
inside the mock until the test method overrides behavior. This meant
I would have to update every single test with an appropriate mock override.

I opted for a temporary cheat instead. Instead of using the mock, I
passed in a RandomChooser object, preserving the original behavior pre-test.
The following test method is an example of the new behavior on the call
to GetExample.

```
[TestMethod]
public void GetRandomExample_singlestaticvalue()
{
  DataSchema schema = new DataSchema();
  schema.AddElement(new SchemaElement() 
  {
    Name = "element1",
    Value = "val1",
    Type = ElementType.StaticValue
  }
  );
  string result = DataMaker.GetExample( schema, new RandomChooser());
  Assert.AreEqual("val1", result);
}
```
