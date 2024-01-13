I skipped my unit tests and found an even bigger mess I had made earlier
========================================================
![Cartoon of a kind of dragon monster developer sitting at a messy desk reading a book titled Unit Tests](/assets/diaryoflazydeveloperthatskippedunittests.jpg)
_I do not know what kind of creature this cartoon is supposed
to depict. It looks like a dragon, maybe? The prompt to the Bing Image Creator
was "Diary of a developer who got lazy and skipped the unit tests."_

Sometimes I get impatient and skip to end-to-end testing,
usually when I am working with adding IO capabilities to
my code, and almost always get into trouble when doing that.
That happened this time, but I also found a problem in my
API and data structure designs which creeped in mostly
due to not really thinking about the end-to-end use case very well.
This is a reminder that while TDD leads to better code design,
that is not an excuse to defer big picture design to one-step-at-a-time
discovery. You need to anticipate eventual usage ahead of time
and bake that into the assumptions you make as you go through
the TDD process.

And of course, it was end-to-end testing that exposed my mistake.
Chew on that little paradox for a bit...

Some sloppy testing alerted me to a problem
========================================================
I say sloppy testing because from a development discipline
perspective I should have approached the problem differently.

I have a project I am working on that allows one to describe
data based on a schema and from that create random examples of
that schema for sake of testing. I rewrite this tool every
couple years to learn new technology or programming techniques.

This morning I added the ability to save the schema to a file.
I jumped immediately to end to end testing.
I thought I had it working, and was building out a tool to
allow command line generation of data schemas save in
the saved schema files when I found a problem. Suddenly the
tool stopped generating random examples. It was creating blank
strings.

I looked at the saved schema files. They were complete. 

```
{
  "basedatatypes": {
    "Alphas": {
      "SchemaName": "Alphas",
      "Elements": [
        {
          "Name": "alpharanges",
          "Value": [
            {
              "Name": "namealphaslower",
              "Value": null,
              "MinValue": "a",
              "MaxValue": "z",
              "Type": 6
            },
            {
              "Name": "namealphasupper",
              "Value": null,
              "MinValue": "A",
              "MaxValue": "Z",
              "Type": 6
            }
          ],
          "MinValue": null,
          "MaxValue": null,
          "Type": 1
        }
      ]
    },
(this kept going from here... a whole bunch of json...)
```

I traced the code on launch and saw that when it was enumerating the elements
to generate output, the collection of elements in the schema
for a given type was empty (see the in code comment):

```
		public static string GetExample(DataSchema schema, IChooser chooser, ISchemaStore schemaStore)
		{
			string result = string.Empty;
// when I traced the code, this foreach block was getting skipped - Elements was empty
			foreach(SchemaElement element in schema.Elements)
			{
				if(!SchemaElement.IsValidElement(element))
				{
					throw new ArgumentException($"Element is invalid: {element.Name}");
				}
				result = EvaluateElement(result, element, chooser, schemaStore);
			}

			return result;
		}
```
Something was going wrong loading the saved schema data.

Looking closer at the problem
=========================================
This piece of code started the problem:

```
		public FileBasedSchemaStore(string schemaFilePath) 
		{
			this._schemaFilePath = schemaFilePath;
			if(System.IO.File.Exists(_schemaFilePath))
			{
				var file = System.IO.File.ReadAllText(_schemaFilePath);
				_schemaDict = System.Text.Json.JsonSerializer.Deserialize<Dictionary<string, Dictionary<string, DataSchema>>>(file);
			}
			else
			{
				WriteSchemaDictionaryToFile();
			}
		}

		private void WriteSchemaDictionaryToFile()
		{
			var schemaText = System.Text.Json.JsonSerializer.Serialize(this._schemaDict, new JsonSerializerOptions { WriteIndented = true });
			System.IO.File.WriteAllText(_schemaFilePath, schemaText);
		}
```

Well, it didn't REALLY start the problem. The problem started much earlier
in this class definition, it just didn't show up until I wrote the code
above. In the definition below, all the properties with "Value" in their
name are of type object, allowing multiple types of objects to be assigned
to the property:

```
	public class SchemaElement
	{
		public string Name { get; set; }
		public object Value { get; set; }
		public object MinValue { get; set; }
		public object MaxValue { get; set; }
		public ElementType Type { get; set; }
	}

	public enum ElementType
	{
		StaticValue,
		Choice,
		Optional,
		ElementList,
		RangeNumeric,
		Reference,
		RangeAlpha
	}
```

The problem with this is when `System.Text.Json.JsonSerializer.Deserialize()` tries to
assign the propery type to the Value property. The case I hit was when `Type==ElementType.Reference`,
for which the type of object for the `Value` property is supposed to be a custom class
of my own, `DataSchemaReference`. It looks like this:

```
	public class DataSchemaReference
	{
		public string NameSpace { get; set; }
		public string Name { get; set; }
	}
```
Everything was working fine prior to loading the schema dictionary from a
Json file. This is because all the schema creation was done explicitly in
code, most of that unit tests. The data type was under control, and easy to
keep in conformance to the expected type for the kind of schema element being
created.

I got lazy and skipped my unit tests
========================================
I wrote a lot of unit tests for the DataSchema objects. There are also
a whole host of unit tests for a data generator that uses those objects.

I skipped unit tests when I wrote the file-based schema storage provider
seen above. I skipped them because I wanted to see the functionality quickly
and play with persisted schemas so I could experiment with building more
complicated schema out of them by chaining refrences. I was impatient, because
I wanted to do something. That impatience drove me to tie the business logic
of the file based storage provider with the code that handles file IO. Let's look
at that the offending lines again.

Here:
```
var file = System.IO.File.ReadAllText(_schemaFilePath);
_schemaDict = System.Text.Json.JsonSerializer.Deserialize<Dictionary<string, Dictionary<string, DataSchema>>>(file);
```

And here:
```
var schemaText = System.Text.Json.JsonSerializer.Serialize(this._schemaDict, new JsonSerializerOptions { WriteIndented = true });
System.IO.File.WriteAllText(_schemaFilePath, schemaText);
```

There are a few problems here.

1. Saving to file and loading from file are linked with json serialization and deserialization and cannot be tested separately
2. Serializing to and from the schema dictionary, which is a two-layered dictionary of dictionaries of `DataSchema` objects, cannot be tested separately from serializing `DataSchema` objects
3. Json serialization is tied to the FileBasedSchemaProvider, which means the format for loading and persisting schema is tied to only that provider and cannot be used by other providers, such as a database or cloud-based storage
4. Existing tests already treat use polymorphism for the Value property, which means a real change is value is going to have to require changing the tests. This is not really a problem, but had this been released code, then the change in behavior would break existing use of the API.
   
My code has made a mess of things, and the biggest reason this is not
much of a problem is the code is still just mine, not in use by anyone else.

The changes I intend to make and the path I intend to take
===========================================
The following is a tenative plan. Some of this is going to require some exploration,
some of which I am going to do via unit testing.

1. Create a separate interface for loading and saving Json streams to file
2. Move Json serialization and deserialization into the `datatools.datamaker` namespace (as opposed to the `datatools.datamaker.dataprovider`) namespace, making persistence format a core feature of the schema objects
3. Change the `Dictionary<string,Dictionary<string, DataSchema>>` object to a class of its own, which might not be necessary, but it gives me more control over class behavior
4. Get separate true unit tests for Json serialization and deserialization of all the relevant objects, separate from persisting the serialized format to file
5. Explore if changes to the `SchemaElement` properties are needed to handle different datatypes serializing into them or not 

Exploring with unit tests, you say? I thought that wasn't possible.
-------------------------------------------
There is a popular notion out there
that one does not do exploration via unit tests, but I have found that sometimes I can
use unit test authoring as a way of exploring my options for implementation. On this
problem, I am especially curious about how to approach the `Value` property on the
`SchemaElement` class, whether I can leave it polymorphic (`type==Object`), or if I should
create different properties for the different value types.

Curious about the project?
============================================
The project I describe in this article is located <a href="https://github.com/WayneMRoseberry/datatools">
on my github site located here</a>. As of now I don't have any published
binaries, and the main executable project doesn't build
anything useful yet. That might change over time. This
project is more something for me to experiment and maybe
generate article ideas than it is a serious endeavor.
