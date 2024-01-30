Sometimes you need to think ahead more than one step at a time
=====================================================
![Stick figure image of a person following a trail of food on the ground, as a monster waits ahead, hiding behind a rock.](/assets/onestepatatime.png)

_I was dissatisfied with the images that Bing Image Creator generated for the title prompt above, so I made my own cartoon._

I found myself using a bad habit unit testing pattern that
set me up for a bunch of testing code re-write. There is a principle
in TDD which is to write only what you need for one behavior at a time.
The trick is, you don't always know what that is really going to
look like, and you can easily get yourself into a situation where
later changes invalidate a lot of testing procedures.

You need to look a little further ahead down the trail to avoid trouble.

Blindly only writing what I needed one step at a time
=====================================================
I was testing a method that has a signature like this:

```
// given a schemaDef generates a random example of valid
// data which conforms to that schema.
// arguments:
//  schemaProvider: allows for look up of schema definition objects by namespace and name
//  decider: makes decisions for schema objects which require a choice, range or option
//  schemaDef: defines the valid schema for the data
// returns:
//  a string value of valid data in the schema format specified
function getRandomExample(schemaProvider, decider, schemaDef)
```

I was using a TDD approach, so writing code for the testing one at a time,
using only the values needed one at a time, and implementing
functionality one at a time.

The simplest test takes a `SchemaDef` with an object of type
`StaticSchemaValue` at the root. Such an object requires no
decisions, and no lookup, hence there is no need to provide
a working `schemaProvider` or `decider` object. It would
even be incorrect behavior were any methods on those objects
called, so taking that in mind, I wrote the unit test such
that it passes in a null for both objects. (this is in Node.js and JavaScript,
the first of which I have never tried before now, and the latter which
I rarely work with, so maybe some of the code in this article is going
to make others cringe):

```
    it("GetRandomExample StaticSchemaObject", function () {
        let staticObject = new CommonSchema.StaticSchemaObject("val1");
        let schemaDef =  new CommonSchema.SchemaDef("schemadef1", staticObject);
        expect(DataMaker.getRandomExample(null, null, schemaDef).SchemaName).toEqual("schemadef1");
        expect(DataMaker.getRandomExample(null, null, schemaDef).ExampleValue).toEqual("val1");
    });
```

Then I needed to write some mocks
=======================================================
I proceed this way until I write the first check where I pass in
a `ReferenceSchemaObject`, which gives the name of a schema definition
that should be fetched from the `schemaProvider` passed in and then
evaluated. I implement a mock class inside the test method and use it:

```
    it("GetRandomExample ReferenceSchemaObject", function () {
        let refSchemaObject = new CommonSchema.ReferenceSchemaObject("namespace1", "refschemadef");
        let testSchemaDef = new CommonSchema.SchemaDef("schemadef1", refSchemaObject);

        let passedInNamespace = "";
        let passedInSchemaName = "";
        class providerMock = function() {
          constructor() {
            this.getSchemaDef = function (namespace, schemaName) {
            passedInNamespace = namespace;
            passedInSchemaName = schemaName;

            let newSchemaObject = new CommonSchema.StaticSchemaObject("refval1");
            let refSchemaDef = new CommonSchema.SchemaDef("madeupname", newSchemaObject);
            return refSchemaDef;
            };
          }
        }

        let mock = new providerMock();

        let example = DataMaker.getRandomExample(mock, null, testSchemaDef);
        expect(passedInNamespace).toEqual("namespace1");
        expect(passedInSchemaName).toEqual("refschemadef");
        expect(example.SchemaName).toEqual("schemadef1");
        expect(example.ExampleValue).toEqual("refval1");
    });
```

By this time I am about a dozen or so unit tests in, and have used the same pattern
for the methods that need a `decider` mock. I leave the other class null, sticking
with the "_only write what you need_" mantra.

I think of some more testing around robustness and input checks
===============================================================
I consider some requirements I want to write checks for:
>- _DataMaker.getRandomExample should throw if any of the objects passed to it are null._
>- _DataMaker.getRandomExample should throw if any of the objects passed have not defined functions it needs to use._

I realize that when I write the checks for the above requirements, and then
change `getRandomExample` so the checks pass, all the other checks are going to
fail. This is because they all pass in `null` for both the `schemaProvider` and
`decider` arguments.

This exposes a misleading point when applying a dogmatic "_only write what you need_"
approach using TDD. The gotcha is "_...and you really should be sure about what
you __REALLY__ need._" I haven't written that many tests yet, but I am going to
need to change them. But I also want to enforce the idea that both `schemaProvider` and
`decider` are only called when they are supposed to.

Fortunately there is a decent pattern for that
===============================================
I moved the mocks from in-line in the function to in their own file. I changed
their implementation so that every method on the mock throws if called, which would
fail any check that did not need them called where they were. Any check that needs
the methods called would override those methods.
```
class ProviderMock {
    constructor() {
        this.getSchemaDef = function(namespace, schemaName) { throw "not implemented"; };
    }
}
exports.ProviderMock = ProviderMock;
```

I then changed the check to instantiate an instance of the class,
and override the method behavior as needed:

```
    it("GetRandomExample ReferenceSchemaObject", function () {
        let refSchemaObject = new CommonSchema.ReferenceSchemaObject("namespace1", "refschemadef");
        let testSchemaDef = new CommonSchema.SchemaDef("schemadef1", refSchemaObject);

        let passedInNamespace = "";
        let passedInSchemaName = "";

        let mock = new ProviderMock();
        mock.getSchemaDef = function (namespace, schemaName) {
            passedInNamespace = namespace;
            passedInSchemaName = schemaName;

            let newSchemaObject = new CommonSchema.StaticSchemaObject("refval1");
            let refSchemaDef = new CommonSchema.SchemaDef("madeupname", newSchemaObject);
            return refSchemaDef;
        };

        let example = DataMaker.getRandomExample(mock, null, testSchemaDef);
        expect(passedInNamespace).toEqual("namespace1");
        expect(passedInSchemaName).toEqual("refschemadef");
        expect(example.SchemaName).toEqual("schemadef1");
        expect(example.ExampleValue).toEqual("refval1");
    });
```
Now the task ahead of me is to re-write the prior checks using a pattern like this one.
The new pattern more explicitly enforces the "do not call the provider if you
do not need it" because the code in the mock will run, and it will throw. The
throw is going to happen inside whatever method is called, which will give a much
more clear indication of what went wrong rather than having the code under test
fail trying to access a null object.

```
    it("GetRandomExample StaticSchemaObject", function () {
        let staticObject = new CommonSchema.StaticSchemaObject("val1");
        let schemaDef =  new CommonSchema.SchemaDef("schemadef1", staticObject);
        expect(DataMaker.getRandomExample(new ProviderMock(), new DeciderMock(), schemaDef).ExampleValue).toEqual("val1");
    });
```

The key takeaway, keep eyes on both what is immediately in front of you as well as down the road
==================================================
The purpose of only doing one thing at a time and only write what you need when you
need it in TDD is to keep your attention focused on one check at a time. Most of the
steps in TDD are about crafting checks that will fail if the product does not do
what the check is looking for, and a one step at a time approach keeps you from
doing things like implementing everything ahead of having the check in hand that would
fail if some requirement was not implemented yet. That is the important part - and the
general idea is to know why we adopt a given practice.

But the complement to that practice is you have to anticipate somewhat where you
are going as well. In this case, even though the arguments are not used yet, they will
be, and implementing guards to make sure they are valid is going to necessitate change to
any check code that was not passing valid versions of whatever that argument was. In this case, perhaps
the rule of thumb might be:

> Even when a parameter, argument, object or other input is not needed for a given check,
> do not fall into the temptation of passing an invalid version of whatever that input might
> be except in the case of checking how invalid inputs are handled by the system under test.

That guideline in hand, and similar ones, I have some to fix some test code now...

If you are curious, the code is available on my github repository
====================================================
The code for this project is here: <a href="https://github.com/WayneMRoseberry/datatools">https://github.com/WayneMRoseberry/datatools</a>.
I wrote it once already using ASP.NET in C#. I decided to try a version in Node.js
because it seemed simpler, smaller, and I wanted to learn how Node.js. That is located
in the <a href="https://github.com/WayneMRoseberry/datatools/tree/main/datatools.datamakerjs">https://github.com/WayneMRoseberry/datatools/tree/main/datatools.datamakerjs</a>
sub directory.
