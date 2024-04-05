I found a bug in a project of mine that had been there for a while,
but I only discovered it when I added some UI around an API
call I hadn't exposed yet. That made certain tests easier to
do, which led me to trying some ideas I hadn't  when I was
thinking of the testing problem in isolation.

A data generation tool, and adding new data schemas
===================================================
The tool is called "Datamaker", and it generates random
data examples based on schema definitions. There is a REST API
which allows CRUD operations on schema definitions. I also created
a simple UI for the viewing randomly generated example data.

I updated the UI to allow editing of the example schema. I did
this because an acquaintance of mine wanted to practice API
testing, and I wanted to make it easier for them to see the
editing in action with a UI example.

![screenshot of datamaker page with new "Edit" button circled](/assets/datamaker_neweditbutton.png)

The website has several example data schemas already there. I
had created them with the REST API. It stores them in a database
on the back end (_"database" is not true - the current implementation
stores them in a json file that is re-written every time a schema
definition is changed or added_).

![screenshot of datamaker page with new "Edit" button circled](/assets/datamaker_editschemaUI.png)

The new editing page is a text area with a "Save" button on it. The
schema is presented as Json. It is primitive, and requires the user
know the correct json to describe the schema. This is aided somewhat
by letting the user pick example schema items and edit them. So long
as they keep the data in the same format it should work.

Right?

Simple end to end test cases are really good ways to find coding mistakes
====================================================
The first test case I tried was as follows:
1. open the datamaker website home page
2. select "Edit" on the default schema item
3. click "Save" on the unchanged schema
4. click the "Back" arrow to see the opening screen, refresh, view the list of available schema definitions

That did not seem to present any problems that I could see in the UI. My expectation
was that nothing should change. Time for the next test.
1. start from the datamaker website home page
2. select "Edit" on the default schema item
3. change the "SchemaName" value to something else, "defaultschema2"
4. click "Save" on the unchanged schema
5. click the "Back" arrow to see the opening screen, refresh, view the list of available schema definitions

I expected to see "defaultschema2" added to the list of available schema definitions. It was not.
My UI is incomplete, slapped together quickly, and it doesn't present any status on
the save, so there was no error in the UI indicating any problem. I had to debug.

Testing the failure
--------------------------------------------------
I set a breakpoint in the backend code that processes the POST request
for the schema definition creation. Stepping through, it hits this code
inside a switch statement:

```
default:
{
    throw `invalid SchemaObjectTypeName`;
    break;
}
```

The switch statement was inside the method ` schemaObjectHasInfiniteLoop(schemaProvider, schemaDefName, schemaObject, seenAlreadyArray)`.
I already had unit tests around that method, so I stoped the end-to-end test and
moved my testing down several layers to the unit level.

I copied the schema definition in the UI into a string and write a new unit test.

```
it('staticschemaobject', function () {
    var jsonstring = '{ "SchemaName": "defaultschema", "RootSchemaObject": {"ObjectTypeName": "StaticSchemaObject","StaticValue": "val1"},"Namespace": "schemaexamples"}';
    var def = JSON.parse(jsonstring);
    var loops = DataMaker.schemaHasInfiniteLoop(new ProviderMock(), "schemaexamples", def);
    expect(loops).toEqual(false);})
```

This test hit the same condition. I had the problem isolated. Now I could figure out the fix. I will
describe that in a bit, but after I fixed that problem I ran more end-to-end tests in the UI
to find instances of schema that would likewise fail on creation. I found another one, and
created a unit test for it as well.

```
it('non-named static value', function () {
    var jsonstring = '{ "SchemaName": "defaultchoiceschemaexample", "RootSchemaObject": { "ObjectTypeName": "ChoiceSchemaObject", "ChoiceArray": [  "choice1", { "ObjectTypeName": "StaticSchemaObject", "StaticValue": "val1" } ] }, "Namespace": "schemaexamples" }';
    var def = JSON.parse(jsonstring);
    var loops = DataMaker.schemaHasInfiniteLoop(new ProviderMock(), "schemaexamples", def);
    expect(loops).toEqual(false);
})
```

I fixed both problems and re-ran the entire unit test suite. It passed. I ran some
end-to-end tests in the UI, and as far as I can tell everything that had been working was
still working, and the two cases I fixed were working as well.

Let's take a look at the fix, and think about why I missed this bug
================================================
The function 'schemaObjectHasInfiniteLoop' has a switch statement in it
that makes different decisions based on the type of the current node in
the schema. I show an altered version of that switch statement below
with the code blocks redacted for readability, except for the two
new cases I added for a blank typeName and the name `StaticSchemaObject`.
The error case was that if the node were for any of those types of
values it would fall through to the default, which would throw the
error `invalid SchemaObjectTypeName`.

```
    switch (typeName) {
        case 'ReferenceSchemaObject':
            {
...
            }
        case 'SequenceSchemaObject':
            {
...
            }
        case 'ChoiceSchemaObject':
            {
...
            }
        case 'OptionalSchemaObject':
            {
...
            }
        case 'RangeAlphaSchemaObject':
            {
...
            }
        case 'RangeNumericSchemaObject':
            {
...
            }
// The following two cases are the new items added for the fix
        case 'StaticSchemaObject':
            {
                return false;
            }
        case '':
            {
                return false;
            }
        default:
            {
                throw `invalid SchemaObjectTypeName`;
                break;
            }
    }
```

The fix was simple. The bug was simple. The data state that exposed the bug
was simple. It was so simple I had no problem creating a unit test to reproduce the
failure. It took me longer to edit out the end of line feeds in the sample
data than it did making the test code. Why did I miss this bug until
after I made changes to the UI to allow editing of the schema?

That pesky developer mindset
------------------------------------------------
