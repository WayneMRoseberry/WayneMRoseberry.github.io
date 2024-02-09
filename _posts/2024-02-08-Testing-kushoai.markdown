I performed a testing session against Kusho.ai, an AI-driven testing
tool automatically creates test cases for REST API based applications.
It boasts other features. In this testing session, I focused on
creation of cases automatically from an import of Postman test collections,
augmentation of those collections through natural language descriptions
of test cases, execution of those tests, and then a comparison of
doing the equivalent activity in Postman.

I consider this kind of test scenario based testing, simulating
a user attempting to solve a problem using the product. I also
consider it a competitive comparison test, which can help
bring out issues of unmet expectations, but also identify
ways the product brings something new and valuable to the customer.

For this problem, I needed something real to test.
===================================================
![a screenshot of the DataMaker WebUI, which has dropdowns for schema categories, schema names, json schema definition, and example data in the schema format](/assets/datamaker_webui.png)

I have created a web-based tool, 
<a href="http://datamakerjs-f3b6b7d13de0.herokuapp.com/">
Datamaker</a>, which allows the
creation of random data samples based on a schema. The tool
exposes a REST API for viewing available schema, creating
new schema, and producing random examples. The tool is
currently under development as I write this article, so
the need and interest in testing the tool is real. I am
not just simulating a target customer, I am really a
target customer.

I prepared a Postman Collection
-------------------------------------------------------
I created a <a href="/assets/Datamaker collection.postman_collection.json">Postman collection to Json</a> for import into
Kusho. The time spent creating the collection may have been
about 10 minutes. In the collection were five API requests to different URL
endpoints:
```
- GET: https://datamakerjs-f3b6b7d13de0.herokuapp.com/api/namespaces
- GET: http://datamakerjs-f3b6b7d13de0.herokuapp.com/api/schemadefs?namespace=baseobjecttypes
- GET: http://datamakerjs-f3b6b7d13de0.herokuapp.com/api/schemadef?namespace=baseobjecttypes&schemaname=initialalpha1to8charstring
- GET: http://datamakerjs-f3b6b7d13de0.herokuapp.com/api/schemadef/getrandomexample?namespace=baseobjecttypes&schemaname=initialalpha1to8charstring&count=1
- POST: http://datamakerjs-f3b6b7d13de0.herokuapp.com/api/schemadef
```
The final POST request above creates a schema definition. The body of the
request is in the <a href="/assets/kusho_ai%20postman%20collection%20import%20and%20authoring%20comparison%20use%20case%20testing.pdf">
test report</a> that I presented to the founder of Kusho.ai.

Scenario Based Testing
===================================================
Scenario based testing is easier when you understand the customer
problem well. In the case of Kusho.ai, the target end user are people who are trying to
test a software product, which is me. I am not a novice with testing,
coding, or technology (although I don't use Postman often), but
Kusho promises to save on some of that effort, and that is part of
what I am checking for and testing.

As of right now (February 8th, 2024) the founder of Kusho.ai describes
the product as in a beta stage. This tempers the flavor of the
feedback. Problems are not surprising when a product is beta.
Feature gaps are expected, and testing of use case and
competitive product comparison provide some perspective on those
gaps. Especially if the person taking the time to test understands
the customer needs well that adds some value to the feedback.

I started with the import
---------------------------------------------------
The Kusho.ai user interface is very small, which makes it easy to find
the main functionality. I chose to create a new test suite. From that
I selected to do so from importing a Postman collection, using the one
I created earlier.

> _Postman also allows creation of test suites by describing the REST API
> under test. I had done this earlier, but I wanted to investigate their
> import story.

I was presented with a screen saying I would receive an email when the import
processing was complete.

_Time to deviate from being a normal, well-behaved, nervouse user..._

