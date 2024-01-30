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

I proceed this way until I write the first check where I pass in
a `ReferenceSchemaObject`, which gives the name of a schema definition
that should be fetched from the `schemaProvider` passed in and then
evaluated. I implement a mock (I should probably use a mocking framework, but
I tend to just write the mocks myself):

```
class ProviderMock {
    constructor() {
        this.getSchemaDef = function(namespace, schemaName) { throw "not implemented"; };
    }
}
exports.ProviderMock = ProviderMock;
```
and then use the mock:
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
