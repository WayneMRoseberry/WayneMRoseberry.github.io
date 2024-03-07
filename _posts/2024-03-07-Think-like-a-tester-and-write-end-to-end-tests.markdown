When the testing has to get real
============================================
![Stick figure cartoon depicting someone testing a sled dog harness on a sawhorse dog model and trying to convince their tiny dog to put it on](/assets/sleddogharness.png)

_We have two pugs, and I thought the notion of putting a sled dog
harness on a pug would be funny. The hard part is depicting a pug as
a stick figure._

In this article, I describe a transition between testing approaches
as the code under test changes. In this case, we move up
levels, from low level style testing in unit tests toward
higher level integration tests. I had to do this while working
on my own project as I implemented the layers that work with
the Sql database. I found it interesting how this one simple
change in what I was writing completely changed the way I
wrote the tests.

> Everything in testing is based on context. The code you are
> testing, the problems you are looking for will guide your
> testing methodologies.

How IO affects code behavior and our testing
=============================================
At some point most applications need to write code that reads
from or writes to IO. Anything writing over the network, to a
file, to a database. When you do this, actions you may have
previously mocked become "real", by which I mean the data
is still there  after the test performs the operation, and it will
affect other behavior on the system.

By a technical definition, any testing done on code of this
sort is not a unit test. Unit tests exclude code that reads from
or writes to IO. It is necessarily an integration test or component
test, or possibly an end to end test.

We can demonstrate this difference by comparing the tests for two
components, one that does not use IO, and one for the code which
does the IO on behalf of the other component. The latter takes a
necessary and unavoidable step toward end-to-end testing that
the first does not.

The key lies in the mocking, or absence of it

Testing business logic that is isolated from IO
==============================================
Business logic isolated from IO is more flexible, more resilient, easier
to change, and for sake of this conversation, much easier to test. We design
the code with a dependency injection enabled pattern by passing in objects to
provide all the IO services for the business logic. This makes it
easy to change the IO service behavior, replace frameworks, re-use
services, and to easily mock service behavior in our test code.

In the example below, `SemanticSearchIssueProvider` accepts two objects, an
`EmbeddingProvider` and an `IssueDatabase`. In our test code, we mock the
two providers and override the methods that `SemanticSearcProvider.getIssueProfile()` will
call. In this case, the mocked method returns an array with a single `IssueProfile` object
in it.
```
def test_getIssueProfile(self):
    embeddings = MockEmbeddingProvider()
    issuedb = MockIssueDatabase();
    def mygetIssueProfile(tenantId, projectId, issueProfileId):
        return [IssueProfile(issueProfileId, "test message", date.min)]
    issuedb.getIssueProfile = mygetIssueProfile
    issueprovider = SemanticSearchIssueProvider.SemanticSearchIssueProvider(embeddings, issuedb)
    issueProfile = issueprovider.getIssueProfile("tenant1","project1","profile1")
    self.assertEqual(issueProfile.IssueProfileId, "profile1","Fail if profile ids do not match.")
```
The above mocking behavior is important because it allows the test to do only one thing, which
is check the behavor of `SemanticSearchIssueProvider.getIssueProfile` when the `IssueDatabase.getIssueProfile()`
method returns an array with one issue profile in it. The test does not need to precede the test with
an add operation (specifcally `addIssueProfile`), nor does it need to worry about prior state
of the database, because the database does not exist.

> When testing business logic, mock the dependencies and especially IO in a
> way that lets the test check behavior with fewest steps necesary.

Testing the code which implements the IO
==============================================
We have completed implementation of `SemanticSearchIssueProvider` and have started work
on the IO components. In this example, we are working on `SqlIssueDatabaseProvider`, which
implements the read and write operations to an SQL database built on Postgresql. Below is the
test code for the matching `getIssueProfile()` method on the `SqlIssueDatabaseProvider` provider. It
looks very similar, but there are important differences.
```
def test_getIssueProfile(self):
    profile = IssueProfile("test_getIssueProfile", "this is a test", date.max)
    SqlIssueDatabaseProvider.addIssueProfile("tenant1","project1", profile)

    rows = SqlIssueDatabaseProvider.getIssueProfile("tenant1", "project1", "test_getIssueProfile")
    self.assertEqual(len(rows),1,"fail if the length of return set is not equal to 1")
    self.assertEqual(rows[0].IssueProfileId,profile.IssueProfileId, "Fail if profile ids do not match.")
    self.assertEqual(rows[0].ExampleMessage, profile.ExampleMessage, "Fail if messages do not match.")
    self.assertEqual(rows[0].firstReportDate, profile.firstReportDate, "Fail if reported dates do not match.")
```
There are no mocks in the code above. Further, the call to ` SqlIssueDatabaseProvider.getIssueProfile` is
preceded by a call to `SqlIssueDatabaseProvider.addIssueProfile`. The test code is not faking out the
return of data from IO. The test code is actively exercising the real IO, writing data to the database so
it can check the retrieval behavior later.

> When testing code that reads and writes to IO, you will be forced into real read and write
> operations that are often paired with each other to confirm. The testing is unavoidably at least
> an integration test.

Let's look at the product code:
```
def getIssueProfile(tenantId, projectId, issueProfileId):
    sql = """SELECT issueprofileid, examplemessage, firstreporteddate FROM issueprofiles WHERE tenantId = %s AND projectId = %s and issueProfileId = %s"""
    args = (tenantId, projectId, issueProfileId)
    rows = SqlIssueDatabaseProvider.executequery(sql, args)
    result = []
    for r in rows:
        issueProfile = IssueProfile(r[0],r[1],r[2])
        result.append(issueProfile)
        return result
...
def executequery(sql, args):
    config = load_config()
    try:
        with psycopg2.connect(**config) as conn:
            with conn.cursor() as cur:
                cur.execute(sql,args)
                rows = cur.fetchall()
                return rows
    except(Exception, psycopg2.DatabaseError) as error:
        print(error)
```
Someone may suggest mocking out the sql database by writing a mock for `psycopg2`. That would allow for
more control of the test problem. But it also misses an important part of the test.

In the code above, there are two critical lines that are very fragile and have an enormous affect on the
behavior of the code:
```
    sql = """SELECT issueprofileid, examplemessage, firstreporteddate FROM issueprofiles WHERE tenantId = %s AND projectId = %s and issueProfileId = %s"""
    args = (tenantId, projectId, issueProfileId)
```
These two lines build the Sql query, and the most reliable way to discover
problems introduced by those two lines is to run them against a real SQl server
which implements the real schema for the application, because all of our problems from
this point forward are about whether the query is compliant with the schema, well-formed
and whether the Sql server is going to return what we need or not.

> When testing integration points, particularly when data passed to the external
> dependency is complex, real connections to real running instances of the dependency are
> necessary to check for problems in the data or interaction.
or to submitting code to the
repository. This does suggest that 

When the problem gets real, the testing needs to get real
========================================================
It is important we understand how changes to the code under test, and to the
testing problem drive our testing strategy. Mocks and fakes and stubs are
powerful tools for testing and facilitating checks during development. 
They achieve that power by hiding the real behavior of the dependencies, because
real behavior introduces variation and complexity and cost and pwerformance penalties.
This is a good thing when the logic of the problem under test must be isolated from
the behavior of the dependency.

Some test problems are about the integration of the dependency. In the example above, the
database provider crafts database queries and responds to database behaviors. Any mock
or fake or stub of that system is going to fall short of the actual system behavior, and
it is response to actual system behavior we need to observe in the test. The testing changes.
It becomes more complex, removes its mocks, and uses a real instance of the dependency.

The important part is to let the problem guide the decisions. Use context. Use actual
code behavior. Consider how you would know if something was wrong, and create the
test condition which makes observation of something going wrong highly probable. There
are a lot of guidelines for how to approach testing, and all of those guidelines have
a context for why they say what they do. Some guidelines might seem in conflict what
what I describe in this article, but that is because they are intended for a different
problem and different context. Context is how we bust through dogma and let
the testing get real.
