Sometimes you need to think ahead more than one step at a time
=====================================================


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

I was using a TDD approach, so writing tests one at a time,
using only the values needed one at a time, and implementing
functionality one at a time.

The simplest test takes an SchemaDef with an object of type
StaticSchemaValue at the root. Such an object requires no
decisions, and no lookup, hence there is no need to provide
a working `schemaProvider` or `decider` object. It would
even be incorrect behavior were any methods on those objects
called, so taking that in mind, I wrote the unit test such
that it passes in a null for both objects. (this is in Node.js, JavaScript):

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
I consider some requirements I want to write a check for:
>- _DataMaker.getRandomExample should throw if any of the objects passed to it are null._
>- _DataMaker.getRandomExample should throw if any of the objects passed have not defined functions it needs to use._

I realize that when I write the checks for the above requirements, and then
change `getRandomExample` so the checks pass, all the other checks are going to
fail. This is because they all pass in `null` for both the `schemaProvider` and
`decider` arguments.

This exposes a misleading point in a dogmatic "_only write what you need_"
approach using TDD. The gotcha is "_and you really should be sure about what
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
