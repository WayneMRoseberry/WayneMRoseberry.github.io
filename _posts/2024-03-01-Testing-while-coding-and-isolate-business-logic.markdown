One of the patterns I like to use while coding is to isolate the
business logic into testable units of code independent from any
IO or interaction from external objects. I want to be able
to test the decisions inside the business logic, as well as enforce
and protect the contract between my components with tests
that run fast and check very specific conditions. This reduces
risk at the system level but establishing expected behaviors as
precisely as possible in the smallest pieces of code as
possible.

In this article, I describe an example of one design pattern
I like to use. To do this I am targeting the following goals:

1. Keep all IO as thin as possible, only about data retrieval and transmission in the simplest form as I can
2. Own all decisions about data validation, errors, expected transformations, conditional behavior inside the unit
3. Write a coded check for every decision that the unit owns
4. Use the mocks as a way to define the expected contracts and force test failure when the contract is violated

There is a live version of this article on my <a href="https://www.youtube.com/@SoftwareTesting-bv6di">YouTube channel, Software Testing</a>. I recorded myself
making the changes, running the tests, fixing my bugs, and generally
confusing myself, all for your edification.

<a href="https://www.youtube.com/watch?v=BB4ZVPfS4b8&t=6s">Wayne is testing: unit tests to isolate business logic from IO</a>

The example - an issue reporting method
===============================================
The method in this example allows for retrieval of issue reports
from an issue reporting service. The architecture is designed as
follows:

![Diagram showing how the IssueProvider is consumed by Repository, which calls the constructor giving it both the EmbeddingProvider and IssueDatabase, the latter of which has getIssueReport that the IssueProvider calls](/assets/dupiq_getIssueReport_arch.svg)

The top level object is called a Repository, and it has several methods
which need to access an IssueProvider, which offers issue based services
and functions. The IssueProvider has a method called getIssueReport which
will return an issueReport from where it is stored, but also has business
the business logic for input validation and error handling. Issue providers
can vary in their implementation based on the type of technology and method
they use to carry out their business logic.

This particular issue provider is uses semantic searching to do some of its
work, and bases that on word embeddings and a vector database. It also persists
some data in a regular datbase. Because of that, the constructor
to this IssueProvider takes two objects, an IssueDatabase and an
EmbeddingsProvider. For the getIssueReport method, it only uses the
IssueDatbase, which also implements getIssueReport.

The unit test described in this article covers the EmbeddingIssueProvider.getIssueReport method.
The Repository is tested separately with a mock of the issue provider.

Testing EmbeddingIssueProvider.getIssueReport() simple case
==================================================
The first test I created called getIssueReport with simple, valid inputs
and expected to get back a valid IssueReport object.

```
describe("getIssueReport tests", function () {
    it("existing report", function () {
        var issueDatabase = MockIssueDatabase.getDatabase();
        issueDatabase.getIssueReport = function (t, p, i) {
            return [new CommonSchema.IssueReport("test message", mindate, "100")];
        };

        var embeddingprovider = MockEmbeddingProvider.getEmbeddingProvider();
        var issueProvider = EmbeddingsMaker.getIssueProvider(embeddingprovider, issueDatabase);

        var result = issueProvider.getIssueReport("tenant1", "project1", "report1");
        expect(result.ReportId).toEqual("100");
    });
```

There are several important aspects the above code.

The mocks define the contract
-----------------------------------------------------
The first definition we encounter is that the constructor to the
EmbeddingIssueProvider expects the two providers, one for the embedding provider,
and one for the issue database (_this is hidden behind getIssueProvider_).

The next definition is that the issue database object is expected to implement a
method called `getIssueReport()` that takes three arguments, an id for a tenant, a project,
and the issue report. The test above is perhaps being too permissive in its checks, as
it only checks that the return result is correct. I ought to fix it to also
check that the expected values of `"tenant1", "project1"`, and `"report1"` are passed
through as well.

Another important part is the test code only mocks the methods it believes ought
to be called in this particular case. This gets more interesting when we talk
about the error validation cases later. In support of this, the mock also
does something in its default implementation:

```
class MockIssueDatabase {
...
    getIssueReport(tenantId, projectId, reportId) { throw Error(NOTIMPLEMENTEDEXCEPTION); };
    }
```

MockIssueDatabase throws an error saying it is not implemented if anything
calls it without the test code overriding the mock behavior. The point of
this is to check any errors in the product not handling conditional
logic properly and falling through to use its dependencies at times
when it should not.

Business logic and IO are separated
--------------------------------------------------------
The business logic necessary to satisfy this particular check is almost
nothing. A simple pass through and return from the issue database `getIssueReport` method
would suffice to pass the check. The important part, though, is that the
check is able to exercise that business logic without having to
establish a connection to a real database.  The test sets up its own
version of the issue database, eliminating all IO, and takes full control
of the dependency behavior.

Testing for a missing issue report and checking contract enforcement of error ownership expectations
========================================================
One case I wanted to cover was when an issue report is requested
that is not in the database. The design I want is that the issue provider
will throw in that case, meaning the caller will handle that as an error
to either take mitigating action or report up to the end user.

I want to keep the ownership of that error state inside the issue provider,
and I want the error that the caller deals with to always be the same, no
matter what the underlying technology. This means I want the issue provider
to be the code that decides to make the throw and owns the identity of the
error.

This is made even more interesting by the way database technologies usually
work. When we query for an item from a database, when something does not
match the search condition, we normally get an empty list instead of an
error. There is therefore going to be an expectation of the issue database
provider object, expressed in the contract to return an empty result
set in the case where the item is not found. Meanwhile, the expectation
is still that in this case the EmbeddingIssueProvider will throw an 
error saying the issue report does not exist.

```
it("no existing report", function () {
    var issueDatabase = MockIssueDatabase.getDatabase();
    issueDatabase.getIssueReport = function (t, p, i) {
        return [];
    };

    var embeddingprovider = MockEmbeddingProvider.getEmbeddingProvider();
    var issueProvider = EmbeddingsMaker.getIssueProvider(embeddingprovider, issueDatabase);
    expect(function () { var result = issueProvider.getIssueReport("tenant1", "project1", "report1"); }).toThrow(EmbeddingsMaker.ISSUEREPORTDOESNOTEXIST);
});
```

Testing for invalid inputs and checking proper abort
==================================================
One of the things I want to check is that if the inputs are invalid
the EmbeddingsIssueProvider stops processing at the
appropriate time. The test code which implements this
check is as follows:


```
it("null tenant", function () {
    var issueDatabase = MockIssueDatabase.getDatabase();

    var embeddingprovider = MockEmbeddingProvider.getEmbeddingProvider();
    var issueProvider = EmbeddingsMaker.getIssueProvider(embeddingprovider, issueDatabase);
    expect(function () { var result = issueProvider.getIssueReport(null, "project1", "report1"); }).toThrow(CommonSchema.NULLARGUMENT);
```
The design of this has some important attributes.

De-coupled business logic and IO allows us to check expectations
---------------------------------------------------
Knowing whether the code appropriately stopped processing
after discovering inputs were invalid would require unnatural
tying of test code to implementation were the business logic
and IO tightly coupled. Most of the time, damage from continued
processing after error checks or exceptional conditions is tied
to IO, persisting system state in a way that is only partially
valid.

When we decouple the IO from the business logic we can do simple
and easy checks for handling exceptional conditions up by
using mocks (like ours) which throw if their methods are called
without being overridden. This makes for a very small, compact
check that will very safely fail if the implementation fails to
abort before calling IO on the condition checked.

Checking only one invalid input at a time
---------------------------------------------------
There are three inputs to this method, `tenantid, projectId`, and `reportId.` Each
one of them could be null. The test code above checks only one of
those methods instead of all at once. This is because typical abort
logic will stop processing upon hitting triggering the first guard condition.
The actual implementation in the method looks like this:

```
if (tenantId == null || projectId == null || reportId == null) {
    throw Error(CommonSchema.NULLARGUMENT);
}
```
For the above, so long as `tenantId` is null, none of the others conditions will evaluate. This means
that were there a bug such as the following, having only a check which confirms all three
at once would miss the error:

```
// look closely at the typo which replaces == with ||
if (tenantId == null || projectId == null || reportId || null) {
    throw Error(CommonSchema.NULLARGUMENT);
```

Summing up...
=================================================
The ideas I present here are not too unusual or new. These are pretty common
design patterns that work well to keep code testable, architecture
extensible, and tests more reliable. I like using real examples based on
whatever I was doing in the last day or so because it show show ideas
that may seem distant and conceptual when talked about abstractly can look when
it is something you mean to keep and use for real.

The principles that I demonstrated in this example were:
- isolating IO from business logic
- keeping IO providers thin
- owning conditional logic, errors, exception handling inside the unit
- using mocks to define and enforce contracts

It is a pattern I use quite a lot, and it has worked well for me.
