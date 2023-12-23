The error messages matter
===========================================================
![Image of an angry man at a computer with an error message on the screen, and for some reason a hamburger and french fries at the desk](/assets/wrongerrormessage.jpg)

_I wrote this article as I was writing tests and fixing
my code. The thoughts and observations are live accounts. 
This means you get to observe all the suspense and drama
and excitement of watching me change my mind, make mistakes,
and learn something new as I go._

I wrote a unit test yesterday to cover the case where a data
structure has an infinite loop. I don't want the code to
hang, overflow the stack, or use up a lot of resources
as it loops on the data over and over.

The method under test is __DataMaker.GetExample()__, which
takes a __DataSchema__ object as an argument, and returns a
string which is a valid example of the data described by the
schema. One of the data types in the schema is an __Element.Reference__
which provides a name to another schema that the code looks
up in the provided __ISchemaStore__ object.

The test code uses __MockSchemaStore__ and overrides the
__GetSchemaElement__ call to return a __DataSchema__ object
whose only element is a reference to the same schema. This creates
an infinite loop in the schema structure with no alternative means
for the schema to ever terminate. The test method expects
the exception __InfinitelyRecursiveSchemaException__ in this case.

```
[TestMethod]
[ExpectedException(typeof(InfinitelyRecursiveSchemaException))]
public void GetExample_reference_circular_throws()
{
  SchemaElement schemaElement = new SchemaElement()
  {
    Name = "testelem",
    Value = new DataSchemaReference() { Name = "testref", NameSpace = "test.namespace" },
    Type = ElementType.Reference
  };
  MockSchemaStore schemaStore = new MockSchemaStore();
  schemaStore.overrideGetSchemaElement = (d) =>
  {
    SchemaElement schemaElement2 = new SchemaElement()
    {
      Name = "testelem",
      Value = new DataSchemaReference() { Name = "testref", NameSpace = "test.namespace" },
      Type = ElementType.Reference
    };
    DataSchema dataSchema = new DataSchema() { };
    dataSchema.AddElement(schemaElement2);
    return dataSchema;
  };
  MockChooser mockChooser = new MockChooser();
  DataSchema inputSchema = new DataSchema();
  inputSchema.AddElement(schemaElement);
  string result = DataMaker.GetExample(inputSchema, mockChooser, schemaStore);
}
```

I wrote the above method before changing the product code to detect
recursion and throw (TDD practice is to perform the test first, watch
the test fail, then change the product code until the test passes), and
the exception it did throw surprised me:

```
 GetExample_reference_circular_throws
   Source: datamakerunittests.cs line 349
   Duration: 2.6 min

  Message: 
Test method threw exception System.InvalidCastException, but exception datatools.datamaker.InfinitelyRecursiveSchemaException was expected. Exception message: System.InvalidCastException: Unable to cast object of type 'datatools.datamaker.SchemaElement' to type 'datatools.datamaker.DataSchemaReference'.

  Stack Trace: 
DataMaker.EvaluateElement(String result, SchemaElement element, IChooser chooser, ISchemaStore schemaStore) line 68
DataMaker.GetExample(DataSchema schema, IChooser chooser, ISchemaStore schemaStore) line 24
DataMaker.EvaluateElement(String result, SchemaElement element, IChooser chooser, ISchemaStore schemaStore) line 69
DataMaker.GetExample(DataSchema schema, IChooser chooser, ISchemaStore schemaStore) line 24
datamakerunittests.GetExample_reference_circular_throws() line 373
RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)
MethodBaseInvoker.InvokeWithNoArgs(Object obj, BindingFlags invokeAttr)
```

I was anticipating a stack overflow as __GetExample()__ called itself
forever recursively (see the stack above in the error message, that is what it started to do).
Instead, at two levels deep it throws an __InvalidCastException__. The code
at the point of error looks like this:

```
if (element.Type.Equals(ElementType.Reference))
{
  // The InvalidCastException happens in the following line
  DataSchema referencedSchema = schemaStore.GetSchemaElement((DataSchemaReference) element.Value);
  result = result + GetExample(referencedSchema, chooser, schemaStore);
}
```

I haven't figured out the problem yet. I am more interested in this moment
as something to write about than I am in solving the problem. It is very possible
there is something wrong in the mock, a test code mistake, but that skips an important
point.

The bug is that __element.Value__, which is of type __Object__ in the __SchemaElement__ data
structure is not of the correct type,  __DataSchemaReference__. This is not a bug which
the method __GetExample_reference_circular_throws__ was designed to hit, we got to it
by accident. But it is a real bug, and one the code ought to handle.

Let's make a new test method.

Fail if this isn't the right error
==================================================
The new test method intentionally sets the __Value__ parameter of the __SchemaElement__
object to something other than a __DataSchemaReference__, and sets the __Type__ parameter
to __ElementType.Reference__. The override on __GetSchemaElement__ is removed, which should
throw if the method is called, as the Mock throws __NotImplementedException__ on the
method if it is not overridden. This is important, because we want the check on
validity of the element to happen before the call to the __MockSchemaStore__, so we
want the method to fail if that call happens.

More importantly, it also sets the expected exception to
__ArgumentException__ instead of __InvalidCastException__.
```
[TestMethod]
[ExpectedException(typeof(ArgumentException))]
public void GetExample_reference_notdataschemareference_in_value()
{
  MockSchemaStore schemaStore = new MockSchemaStore();
  MockChooser mockChooser = new MockChooser();

  SchemaElement schemaElement = new SchemaElement()
  {
    Name = "testelem",
    Value = "not a reference",
    Type = ElementType.Reference
  };
    DataSchema inputSchema = new DataSchema();
    inputSchema.AddElement(schemaElement);
    string result = DataMaker.GetExample(inputSchema, mockChooser, schemaStore);
}
```
I run the test method and it fails, so we know the method has
been seen to fail when the ArgumentException is not thrown.

The fix I am trying first is inside a method called __IsValidElement()__ to which
I have added the following condition:
```
else if (element.Type.Equals(ElementType.Reference) && !element.Value.GetType().Equals(typeof(DataSchemaReference)))
{ 
  return false;
}
```

I run the test method again. It passes.

Why do we care which exception is thrown?
-----------------------------------------------------
The difference between the two is exceptions is in the reason
they are thrown.

The __InvalidCastException__ is thrown because the main business
logic of the code does not recognize there is something wrong in
the data, and proceeds to take action. It encounters the error
accidentally midway through its procedure, where any attempt
to clean up what has already happened is too difficult to
understand and control.

The __ArgumentException__ is thrown because the main business
logic of the code intentionally checks if the data is valid and
stops before going any further. There is no accident. The code
can decide to stop at a point where cleanup is manageable. It also
can return information the caller might use, which in this case
is the element name in the schema, passed as the argument name to the exception.

The rule of thumb I like to use when testing is that
any error exception thrown by a dependency of the product code
should be treated as an incorrect error bug. In our case the dependency
is really in the C# language, but __InvalidCastException__ still indicates
a situation where the error state happened in code paths we do not directly
control, at points we did not anticipate. The same is true of errors
like IO failures, database errors, exceeding the indices of dictionaries,
exceptions from third party libraries, and more. What we want to see are the following:

- The error or exception is something selected, sometimes created, by the product code rather than handed to it by a dependency
- The product code is in control of the application state when the error occurred
- As much attempt as was possible has been made for the internal business logic to understand and handle the error
- As much information as is possible is passed along to the caller of the code, which may even be useful to the end user

Is there an exception to these rules about exceptions?
-----------------------------------------------------
Certainly, but it is better to say "pay attention to your context" then
to try to make a list of rules.

In my experience, sometimes you are in a situation where the underlying state
of the dependencies matters more to the user than the intermediary business
logic. I have seen this a lot in test frameworks or testing libraries, where
some patterns bubble up lower errors rather than wrap them.

The way I feel about exceptions like these is to make the decision
intentionally instead of by accident. Look at the guidelines I offer above
and if you have a reason why you need to do different, then do it.

Getting back to that infinite recursion test
======================================================
My expectation was that the infinite recursion test was still
going to fail. I hadn't changed the product code to detect the
recursion, I had only put in a check for mismatched data types
when the element type was __ElementType.Reference__.

What I expected was that now, intead of failing with an
__InvalidCastException__, the method would fail with an
__ArgumentException__.

That is not what happened:

```
 GetExample_reference_circular_throws
   Source: datamakerunittests.cs line 367
   Duration: 2.6 min

  Message: 
Test method threw exception System.InvalidCastException, but exception datatools.datamaker.InfinitelyRecursiveSchemaException was expected. Exception message: System.InvalidCastException: Unable to cast object of type 'datatools.datamaker.SchemaElement' to type 'datatools.datamaker.DataSchemaReference'.

  Stack Trace: 
DataMaker.EvaluateElement(String result, SchemaElement element, IChooser chooser, ISchemaStore schemaStore) line 68
DataMaker.GetExample(DataSchema schema, IChooser chooser, ISchemaStore schemaStore) line 24
DataMaker.EvaluateElement(String result, SchemaElement element, IChooser chooser, ISchemaStore schemaStore) line 69
DataMaker.GetExample(DataSchema schema, IChooser chooser, ISchemaStore schemaStore) line 24
datamakerunittests.GetExample_reference_circular_throws() line 373
RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)
MethodBaseInvoker.InvokeWithNoArgs(Object obj, BindingFlags invokeAttr)
```
It returns the same __InvalidCastException__ on the same line.

This is a different bug. The product code is somehow treating the case introduced in
__GetExample_reference_circular_throws__ differently than the one introduced
in __GetExample_reference_notdataschemareference_in_value__, even though
I had tried to write the case to cover what I thought was the same condition.

There are two important points about testing I would like to indicate now.
1. This current bug is a demonstration of why we want the product code to be in control of the errors it throws
2. We may be testing looking for one thing, but very often we wind up finding something entirely different - and that is VERY GOOD, despite what an endless supply of automation and Agile consultants will try to tell you about testing methodology

In which we make mistakes because we forget about things our tools do
==================================================
I ran the test method, in the debugger.
In the failure message the __InvalidCastException__ was thrown
after two levels of recursion.

In an earlier version of my testing code, I was assigning the wrong type of object
to the __Value__ propert of the __SchemaElement__ inside the __MockSchemaStore__,
but I had changed it to the correct data type (__DataSchemaReference__) only to
see the __InvalidCastException__ error persist. I had expected the test method to fail on
an abort for running too long.

During debug, I found that __GetExample()__ was exceeding two
levels of recursion. I went as deep as five levels, no exception thrown
before I concluded the problem might not really be an invalid cast.
I let it run to end, and got the same error.

Then I cleared the test results pane. With the failure message no longer
there, I ran the method again. Instead of a failure message, this was displayed
on the status bar:
> Test run aborted: 0 tests (O passed, 0 Failed, 0 Skipped) run < 1 ms

I had been chasing a phantom bug. The product code was, seemingly, handling
the invalid reference element consistently. The problem was that Visual Studio,
in deciding to abort my test (for infinite recursion? not sure) was leaving up
the same failure message as prior runs.

Even this snipe hunt or cryptid creature expedition demonstrates my point
about the importance of error messages. When we observe an error message while
running a test, even when we know the data or test conditions might represent
an invalid state or a negative state, we should investigate the error message. Is
it one the product returned intentionally, or by accident? In your investigation,
you might discover you are testing incorrectly, and that is important to fix.

Erring on the side of errors
==================================================
I wrote this article to demonstrate how I like to deal with errors while
I am testing.
- Investigate if the error message or exception is intentional
- Be suspicious of errors thrown by lower level dependencies
- Check if the product has control of its state/business logic at the point of error
- Look for side effects, errors that come up while testing something else, as they are usually a hint at another good test

Postscript... what about that infinite recursion?
-------------------------------------------------
I might write another article about this, but the infinite recursion
test is making me re-evaluate the product design. Parsing the data structure
to return a result is more difficult if that same code has to watch for
infinite recursion. I am wondering if the check needs to be handled
at a different point, perhaps submission of the schema objects to
the SchemaStore?

Product code changes a lot based on testing feedback. I describe one
way this happens in <a href="https://waynemroseberry.github.io/2023/12/18/When-the-product-needs-to-change-to-remove-flake.html">
"When the product needs to change to handle flake"</a>, but that article
was about changes that don't really alter the overall API flow whereas
this one might.
