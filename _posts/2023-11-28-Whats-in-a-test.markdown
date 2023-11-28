Testing in layers
======================
![image depicting a group of testers looking at a large many layered puff pastry](/assets/testinginlayers.jpg)
I posted an <a href="https://waynemroseberry.github.io/2023/11/27/Automation-refactoring-as-the-app-changes.html">
article recently</a> about how I changed my end to end automation test
methods as the underlying product changed. I made reference to unit 
and component tests in that article. In this article
I describe some of that other testing and how it relates to the
feature architecture.

End to End Test Diagram
======================
I want to start with the end to end testing of a feature called "Report Issue."
This feature is exposed via a REST API, and allows one to submit an
issue report as a string, from which the system will either indicate
the identity of a previous existing issue if reported before, or create
a new issue and return the new identity. 

The GET_ReportIssue() test procedure
----------------------
The test method is relatively simple:

```
[TestMethod]
public void GET_ReportIssue()
{
    string testMessage = "somethingnew";
    string requestUri = IssueIdentityApiTestsHelpers.BuildReportIssueGetRequestUri(testMessage, UriBase, _sharedTenantId, _sharedProjectId);
    WebRequest webRequest = IssueIdentityApiTestsHelpers.CreateGetRequest(requestUri, _adminToken);
    WebResponse webResponse = webRequest.GetResponse();
    using (var responseReader = new StreamReader(webResponse.GetResponseStream()))
    {
    string response = responseReader.ReadToEnd();
    Console.WriteLine(response);
    IssueProfile issueProfileResponse = JsonSerializer.Deserialize<IssueProfile>(response);
    Assert.AreEqual(testMessage, issueProfileResponse.exampleMessage, "Fail if the example message of the returned issue is not what was sent.");
    Assert.AreEqual(true, issueProfileResponse.isNew, "Fail if the issue was not reported as new.");
    }
}
```
The method constructs a URI to the REST API, makes a request, and checks the return value.
There are variables in the method which indicate some work that happens during test
setup:
- _sharedTenantId: issue reports live in projects, which live in tenants, so a tenant is created during test setup
- _sharedProjectId: same as above
- _adminToken: a JWTBearer token which holds claims that both authenticate and authorize the user, fetched during test setup

ReportIssue() Product Behavior
----------------------
Behind the REST API, the product code does the following:
- check tenant information to determine which role in the user claims aligns to the current tenant
- fetch word embeddings for the issue reported from a Word2Vec model
- query a vector database to see if any previously reported issues are within similarity threshold
- if nothing matches, store the embeddings vector database as a new issue
- if new issue, store the issue profile in the issue database
- store the issue report in the issue database

The product design supports an extensible, pluggable set of providers and interface
implementations to allow execution on different storage systems, embedding solutions, or
vector databases. A diagram of the current implementation, showing all the points of
interaction during the single "webRequest.GetResponse()" call above is shown below:

![A diagram showing all the components involved in the GET_ReportIssue method](/assets/reportissue_end_to_end_relationships.png)

The connection from Client to REST API on the "ReportIssue" bubble corresponds to the
"webRequest.GetResponse()" call in the code excerpt above. The other connections
from the client to REST API all correspond to test setup. I left test cleanup out
of the diagram, as it was getting too cluttered.

Reflections on the Get_ReportIssue end to end test
----------------------
I knew there was a lot going on behind the scenes when this test method
executes, but I was still taken by surprise when I tried to draw the picture. It
was richer, and more complex than I might have thought. It is even simplified. For example, the
stored procedures that write data actually connect to tables from which
later read operations happen, which is probably useful to demonstrate the integration
relationship between operations. Those earlier writes to tenant and project
tables affect the later call to ReportIssue that fetch information about
the tenant.

Very early on with this test I might have had a few times where the
main functional behavior, reporting an issue, failed to work correctly.
There were not many of those instances, and the stopped failing there
very quickly. There are more direct tests at the unit level which check
that functionality piece by piece.

The bugs that came up during the end to end testing were mostly
about integrating components together or doing multiple operations over
time to complete longer sequences. Sql connection leaks show more easily,
as the end to end tests leave the SUT running across iterations. Mistakes
made creating a user token, or getting the right claims came up. Underlying
SQL queries which were not filtering the WHERE clause precisely enough. All
of these things slipped past the lower level tests, some because it slipped
my mind at the time when writing the check, and some because the problem
does not exist until all the components come together.

Unit Tests for ReportIssue
======================
Every single rectangle under "Backend Service" in the diagram above has a corresponding
test or set of tests targeting it. 

IssueRepository - top level object
----------------------
The following test method exercises the ReportIssue method in the IssueRepository class.
This class is a higher level class that utilizes together two interfaces, an IIssueDbProvider for
storing issues and reports, and an IIssueIdentityProvider for determining whether an
issue is new or previously reported. A mock is provided for both interfaces, with
overrides specified in the test method for each of the methods called during
ReportIssue. The mock is design so that any method not overridden will throw a
NotImplementedException if called.

```
[TestMethod]
public void ReportIssue_newissue()
{
  IssueReport passedInIssueReport = null;
  IssueProfile passedInIssueProfile = null;

  MockIssueDbProvider dbProvider = new MockIssueDbProvider();
  dbProvider.overrideConfigure = (s) => { };
  dbProvider.overrideAddIssueReportTenantConfigProjId = (i, t, s2) => { passedInIssueReport = i; };
  dbProvider.overrideAddIssueProfileTenantConfigProjId = (i, t, s2) => { passedInIssueProfile = i; };

  MockIssueIdProvider issueIdentityProvider = new MockIssueIdProvider();
  issueIdentityProvider.overrideAddIssueProfile = (i, t, p) => { };
  issueIdentityProvider.overrideGetRelatedProfilesTenantConfigProjId = (i, c, t, p) => { return new RelatedIssueProfile[] { }; };
  issueIdentityProvider.overrideCanHandleIssue = (i) => { return true; };
  issueIdentityProvider.overrideConfigure = (s) => { };

  IssueRepository issueRepository = new IssueRepository(dbProvider, new IssueIdentityManager(new IIssueIdentityProvider[] { issueIdentityProvider }, null));
  DateTime testdate = new DateTime(2000, 01, 02);
  EmptyTenantManager emptyTenantManager = new EmptyTenantManager();
  IssueProfile issueProfile = issueRepository.ReportIssue(new IssueReport() { IssueMessage = "testmessage", IssueDate = testdate }, emptyTenantManager.DefaultTenant(), string.Empty);
  Assert.IsTrue(issueProfile.IsNew, "Fail if the issue is not new.");
  Assert.IsNotNull(passedInIssueReport, "Fail if the passed in issue report is null.");
  Assert.IsNotNull(passedInIssueProfile, "Fail if the passed in issue profile is null.");
  Assert.AreEqual(testdate.ToString(), issueProfile.FirstReportedDate.ToString(), "Fail if the first reported date is not as expected.");
  Assert.AreEqual("testmessage", passedInIssueProfile.ExampleMessage, "Fail if the example message is not expected value.");
  Guid temp;
  Assert.IsTrue(Guid.TryParse(passedInIssueReport.IssueId, out temp), "Fail if issue id is not a guid.");
  Assert.AreEqual("testmessage", passedInIssueReport.IssueMessage, "Fail if did not get expected issue message on passedInIssueReport.");
}
```
The point of the IssueRepository is to provide the key business
logic for the ReportIssue behavior, independent of storage technology
or service platform. Implementations of IIssueIdentityProvider and IIssueDbProvider
might enable execution SQL, MySql, PineCone, Word2Vec, various Azure storage solutions,
AWS services, or a variety of other technology.

IIssueDbProvider - AddIssueProfile
----------------------------
I am going to skip the IssueIdentity layer between IssueRepository and IIsueDbProvider
because I used the same testing ideas there as in IssueRepository. A more
interesting thing happes at IIssueDbProvider where the product code gets closer to the database
layer.

It is almost impossible to separate all business logic from data
storage, and it is difficult to test data tier business logic in unit
tests running during build processes that do not have access to the
storage platform. I like to do as little business logic in the database
as I can, except in cases where the database is better at the heavy
lifting.

To support this, the IIssueDbProvider implementation for
SQL server takes a helper object. All the sql connections and
queries are handled by the helper. The IIssueDbProvider does
all the conversion of query results to the classes and data
types used by the upper level tiers. This allows testing of
all business logic except for IO in unit tests, in
memory.

To do this, the mock below returns a DbDataReader object. This
keeps the SQL IIssueDbProvider from having to deal with
sql connections itself - the data can be constructed in memory
if needed.
```
public void GetIssueProfile()
{
	DateTime nowTime = DateTime.Now;
	MockSqlIOHelper mockSqlIOHelper = new MockSqlIOHelper();
	mockSqlIOHelper.overrideGetIssueProfile = (s, t, p) =>
	{
		DataTable dt = new DataTable();
		dt.Columns.Add("Id", typeof(string), string.Empty);
		dt.Columns.Add("ExampleMessage", typeof(string), string.Empty);
		dt.Columns.Add("FirstReportedDate", typeof(DateTime), string.Empty);
		dt.Rows.Add("testissue", "testmessage", nowTime);
		return dt.CreateDataReader();
	};
	SqlIssueDbProvider sqlDbProvider = new SqlIssueDbProvider(mockSqlIOHelper);
	IssueProfile issueProfile = sqlDbProvider.GetIssueProfile("testissue", new TenantConfiguration(), string.Empty);
	Assert.AreEqual("testissue", issueProfile.IssueId, "Fail if issue profile id does not match expected.");
	Assert.AreEqual("testmessage", issueProfile.ExampleMessage, "Fail if example message does not match expected.");
	Assert.AreEqual(nowTime.ToString(), issueProfile.FirstReportedDate.ToString(), "Fail if date does not match expected value.");
}
```
The advantage here is we can isolate platform and technology
specific business logic from that which is independent of
technology or platform.

There is a challenge to this approach. Note that the override on the
mock is creating a data table schema. When you write tests this way, it
is very easy for the data table schema to get out of line with the
real product schema. That misalignment may or may not affect test behavior. One
way to avoid this, which I have not implemented here, is to create
a method on the product which creates a datatable in the expected
schema format.

SqlDbIOHelper - GetIssueProfile
----------------------------
Testing SqlDbIOHelper is something that can no longer fit within
the strict constraints of a unit test. We are testing SQL queries,
so there needs to be a SQL server running with the proper tables, schema,
security, roles, and stored procedures configured. I run this
test separately from my unit tests. At the moment I run it at home
on my own machine. Were this at work, I would have a lab and have
procedures to deploy the SQL server and configure it. Right now I 
do all of that myself prior to execution.

The test below is more rudimentary than I would like. There are many properties
on an issue profile, and this one only checks the ID. Lots of bugs to miss here,
so I probably should tend to some cleanup.

The key point of this example is the pattern. It uses the interface
the same way that IIssueDbProvider does. It expects a DbDataReader object from
the call to GetIssueProfile, and from there it pulls the information
it needs from the reader.

Notice an important part, and a reason why this is not a unit test. Prior
to making the call to the tested method (GetIssueProfile) the procedure
calls "AddIssueProfile" to put data in the database. A unit test would
not do that. Instead, whatever IO provider fetches the data would be overridden
to present whatever the test needed as if the database had been populated. We
are testing stateful IO now, and our test has to jump up a layer of complexity.

```
[TestMethod]
public void GetIssueProfile()
{
    SqlIssueDbIOHelper sqlIssueDbIOHelper = new SqlIssueDbIOHelper(SqlConfig);
    sqlIssueDbIOHelper.AddIssueProfile(
      new IssueProfile() { ExampleMessage = "test msg", FirstReportedDate = DateTime.Now, IsNew = true, IssueId = "testissue1" },
      tenantConfiguration,
      projectId);

      string issueId = string.Empty;
      using (DbDataReader reader = sqlIssueDbIOHelper.GetIssueProfile("testissue1", tenantConfiguration, projectId))
      {
       while (reader.Read())
       {
          issueId = reader["Id"].ToString().Trim();
          break;
       }
      }
    Assert.AreEqual("testissue1", issueId, "Fail if we do not get the expected issueid.");
}
```

Reference Material
=======================
If you want to see the DOT code that draws the diagram, you can
<a href="https://dreampuf.github.io/GraphvizOnline/#digraph%20G%20%7B%0A%0A%20%20subgraph%20cluster_0%20%0A%20%20%7B%0A%20%20%20%20style%3Dfilled%3B%0A%20%20%20%20color%3Dlightgrey%3B%0A%20%20%20%20node%20%5Bstyle%3Dfilled%2Ccolor%3Dwhite%5D%3B%0A%20%20%20%20a0%3B%0A%20%20%20%20a1%3B%0A%20%20%20%20a2%3B%0A%20%20%20%20a3%3B%0A%0A%20%20%20%20label%20%3D%20%22REST%20API%22%3B%0A%20%20%7D%0A%20%20subgraph%20cluster_backend%0A%20%20%7B%0A%20%20%20%20%20%20style%3Dfilled%3Bcolor%3Dgray%3B%0A%20%20%20%20%20%20label%20%3D%22Backend%20Service%22%3B%0A%20%20%20%20%20%20%0A%20%20%20%20subgraph%20cluster_repository%20%0A%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20style%3Dfilled%3Bcolor%3Dwhite%3B%0A%20%20%20%20%20%20%20%20b0%3B%0A%20%20%20%20%20%20%20%20label%20%3D%20%22IssueRepository%22%3B%0A%0A%20%20%20%20%20%20%20%20subgraph%20cluster_identitymanager%20%0A%20%20%20%20%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20c0%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20label%20%3D%20%22IssueIdentityManager%22%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20subgraph%20cluster_identityprovider%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20d0%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20d1%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20label%20%3D%20%22IssueIdentityProvider%22%3B%0A%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20subgraph%20cluster_IVectorHelper%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20label%20%3D%20%22Word2Vec_Pinecone_VectorHelper%22%0A%20%20%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20subgraph%20cluster_Word2Vec%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20label%20%3D%20%22Word2Vec%22%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20k0%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%7D%0A%20%20%20%20%20%20%20%20%20%20%20%20%7D%20%0A%20%20%20%20%20%20%20%20%7D%20%0A%20%20%20%20%7D%0A%20%20%20%20subgraph%20cluster_IssueDbProvider%0A%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20style%3Dfilled%3Bcolor%3Dwhite%3B%0A%20%20%20%20%20%20%20%20label%3D%22IssueDbProvider%22%0A%20%20%20%20%20%20%20%20j0%3B%0A%20%20%20%20%20%20%20%20j1%3B%0A%20%20%20%20%20%20%20%20%0A%20%20%20%20%20%20%20%20subgraph%20cluster_IssueDbHelper%0A%20%20%20%20%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20label%3D%22IssueDbHelper%22%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20m0%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20m1%3B%0A%20%20%20%20%20%20%20%20%7D%0A%20%20%20%20%7D%0A%20%20%20%20subgraph%20cluster_IUserManager%0A%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20style%3Dfilled%3Bcolor%3Dwhite%3B%0A%20%20%20%20%20%20%20%20label%3D%22IUserManager%22%3B%0A%20%20%20%20%20%20%20%20e0%3B%0A%20%20%20%20%20%20%20%20subgraph%20cluster_IUserDbProvider%0A%20%20%20%20%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20label%20%3D%22IUserDbProvider%22%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20g0%3B%0A%20%20%20%20%20%20%20%20%7D%0A%20%20%20%20%7D%20%0A%20%20%20%20subgraph%20cluster_ITenantManager%0A%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20style%3Dfilled%3Bcolor%3Dwhite%3B%0A%20%20%20%20%20%20%20%20label%3D%22ITenantManager%22%3B%0A%20%20%20%20%20%20%20%20f0%3B%0A%20%20%20%20%20%20%20%20f1%3B%0A%20%20%20%20%20%20%20%20f2%3B%0A%20%20%20%20%20%20%20%20f3%3B%0A%20%20%20%20%20%20%20%20f4%3B%0A%20%20%20%20%20%20%20%20f5%3B%0A%20%20%20%20%20%20%20%20f6%3B%0A%20%20%20%20%20%20%20%20f7%3B%0A%20%20%20%20%20%20%20%20subgraph%20cluster_ITenantDbProvider%0A%20%20%20%20%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20%20%20%20%20label%3D%22ITenantDbProvider%22%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20h0%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20h1%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20h2%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20h3%3B%0A%20%20%20%20%20%20%20%20%20%20%20%20h4%3B%0A%20%20%20%20%20%20%20%20%7D%0A%20%20%20%20%7D%0A%20%20%7D%0A%20%20%0A%20%20subgraph%20cluster_storagetier%0A%20%20%7B%0A%20%20%20%20%20%20style%3Dfilled%3Bcolor%3Dgray%3B%0A%20%20%20%20%20%20label%3D%22storage%22%3B%0A%20%20%20%20%20%20subgraph%20cluster_SQLDb%0A%20%20%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20style%3Dfilled%3Bcolor%3Dwhite%3B%0A%20%20%20%20%20%20%20%20%20%20label%3D%20%22SQL%20DB%22%3B%0A%20%20%20%20%20%20%20%20%20%20s0%3B%0A%20%20%20%20%20%20%20%20%20%20s1%3B%0A%20%20%20%20%20%20%20%20%20%20s2%3B%0A%20%20%20%20%20%20%20%20%20%20s3%3B%0A%20%20%20%20%20%20%20%20%20%20s4%3B%0A%20%20%20%20%20%20%20%20%20%20s5%3B%0A%20%20%20%20%20%20%20%20%20%20s6%3B%0A%20%20%20%20%20%20%20%20%20%20s7%3B%0A%20%20%20%20%20%20%7D%20%20%0A%20%20%20%20%20%20subgraph%20cluster_Pinecone%0A%20%20%20%20%20%20%7B%0A%20%20%20%20%20%20%20%20style%3Dfilled%3Bcolor%3Dwhite%3B%0A%20%20%20%20%20%20%20%20%20%20label%20%3D%20%22Pinecone%22%3B%0A%20%20%20%20%20%20%20%20%20%20l0%3B%0A%20%20%20%20%20%20%20%20%20%20l1%3B%0A%20%20%20%20%20%20%7D%20%20%20%20%20%20%0A%20%20%7D%0A%0A%20%20%0A%0A%20%20%0A%0A%20%20%0Astart%20-%3E%20a0%3B%0Astart%20-%3E%20a1%3B%0Astart%20-%3E%20a2%3B%0Astart%20-%3E%20a3%3B%0A%0Aa0%20-%3E%20b0%3B%0Aa2%20-%3E%20f6%3B%0Aa3%20-%3E%20f7%3B%0Ab0%20-%3E%20c0%3B%0Ac0%20-%3E%20d0%3B%0Aa1%20-%3E%20e0%3B%0Aa1%20-%3E%20f0%3B%0Aa1%20-%3E%20f1%3B%0Ae0%20-%3E%20g0%3B%0Af0%20-%3E%20h0%3B%0Af1%20-%3E%20h1%3B%0Aa1%20-%3E%20f2%3B%0Af2%20-%3E%20h2%3B%0Ab0%20-%3E%20f3%3B%0Ab0%20-%3E%20j0%3B%0Ab0%20-%3E%20j1%3B%0Ac0%20-%3E%20f4%3B%0Ac0%20-%3E%20f5%3B%0Ac0%20-%3E%20d1%3B%0Ad0%20-%3E%20k0%3B%0Ad0%20-%3E%20l0%3B%0Ad1%20-%3E%20l1%3B%0Aj0%20-%3E%20m0%3B%0Aj1%20-%3E%20m1%3B%0Am0%20-%3E%20s0%3B%0Am1%20-%3E%20s1%3B%0Ah0%20-%3E%20s2%3B%0Ah1%20-%3E%20s3%3B%0Ah2%20-%3E%20s4%3B%0Ag0%20-%3E%20s5%3B%0Af6%20-%3E%20h3%3B%0Af7%20-%3E%20h4%3B%0Ah3%20-%3E%20s6%3B%0Ah4%20-%3E%20s7%3B%0A%0Aa0%20%5Bshape%3Drectangle%2C%20label%3D%22ReportIssue%22%5D%3B%20%20%0Aa1%20%5Bshape%3Drectangle%2C%20label%3D%22GetClaimToken%22%5D%3B%0Ab0%20%5Bshape%3Drectangle%2C%20label%3D%22ReportIssue%22%5D%3B%0Ac0%20%5Bshape%3Drectangle%2C%20label%3D%22GetOrCreateIssueProfile%22%5D%3B%0Ad0%20%5Bshape%3Drectangle%2C%20label%3D%22GetRelatedIssueProfiles%22%5D%3B%0Ae0%20%5Bshape%3Drectangle%2C%20label%3D%22GetUserPasswordHash%22%5D%3B%0Af0%20%5Bshape%3Drectangle%2C%20label%3D%22GetTenants%22%5D%3B%0Ag0%20%5Bshape%3Drectangle%2C%20label%3D%22GetUserPasswordHash%22%5D%3B%0Ah0%20%5Bshape%3Drectangle%2C%20label%3D%22GetTenants%22%5D%3B%0Af1%20%5Bshape%3Drectangle%2C%20label%3D%22GetTenantUserAuthorization%22%5D%3B%0Ah1%20%5Bshape%3Drectangle%2C%20label%3D%22GetTenantUserAuthorization%22%5D%3B%0Ah3%20%5Bshape%3Drectangle%2C%20label%3D%22AddTenant%22%5D%3B%0Ah4%20%5Bshape%3Drectangle%2C%20label%3D%22AddProject%22%5D%3B%0Af2%20%5Bshape%3Drectangle%2C%20label%3D%22GetUserServiceAuthorization%22%5D%3B%0Ah2%20%5Bshape%3Drectangle%2C%20label%3D%22GetUserServiceAuthorization%22%5D%3B%0Af3%20%5Bshape%3Drectangle%2C%20label%3D%22GetTenantConfiguration%22%5D%3B%0Aj0%20%5Bshape%3Drectangle%2C%20label%3D%22AddIssueProfile%22%5D%3B%0Aj1%20%5Bshape%3Drectangle%2C%20label%3D%22AddIssueReport%22%5D%3B%0Af4%20%5Bshape%3Drectangle%2C%20label%3D%22GetTenantProfile%22%5D%3B%0Af5%20%5Bshape%3Drectangle%2C%20label%3D%22GetProject%22%5D%3B%0Af6%20%5Bshape%3Drectangle%2C%20label%3D%22AddTenant%22%5D%3B%0Af7%20%5Bshape%3Drectangle%2C%20label%3D%22AddProject%22%5D%3B%0Ad1%20%5Bshape%3Drectangle%2C%20label%3D%22AddIssueProfile%22%5D%3B%0Ak0%20%5Bshape%3Drectangle%2C%20label%3D%22Predict%22%5D%3B%0Al0%20%5Bshape%3Drectangle%2C%20label%3D%22Query%22%5D%3B%0Al1%20%5Bshape%3Drectangle%2C%20label%3D%22Upsert%22%5D%3B%0Am0%20%5Bshape%3Drectangle%2C%20label%3D%22AddIssueProfile%22%5D%3B%0Am1%20%5Bshape%3Drectangle%2C%20label%3D%22AddIssueReport%22%5D%3B%0As0%20%5Bshape%3Drectangle%2C%20label%3D%22sproc_InsertOrUpdateIssueProfile%22%5D%3B%0As1%20%5Bshape%3Drectangle%2C%20label%3D%22sproc_InsertOrUpdateIssueReport%22%5D%3B%0As2%20%5Bshape%3Drectangle%2C%20label%3D%22tbl_tenantconfigurations%22%5D%3B%0As3%20%5Bshape%3Drectangle%2C%20label%3D%22tbl_tenantusers%22%5D%3B%0As4%20%5Bshape%3Drectangle%2C%20label%3D%22tbl_serviceuserauth%22%5D%3B%0As5%20%5Bshape%3Drectangle%2C%20label%3D%22tbl_IssueIdentityUserPasswordHashes%22%5D%3B%0Aa2%20%5Bshape%3Drectangle%2C%20label%3D%22AddTenant%22%5D%3B%20%20%0Aa3%20%5Bshape%3Drectangle%2C%20label%3D%22AddProject%22%5D%3B%20%20%0As6%20%5Bshape%3Drectangle%2C%20label%3D%22InsertOrUpdateTenant%22%5D%3B%0As7%20%5Bshape%3Drectangle%2C%20label%3D%22InsertOrUpdateProject%22%5D%3B%0A%0A%20%20start%20%5Bshape%3DMdiamond%2C%20label%3D%22Client%22%5D%3B%0A%7D">
follow the link here.</a> DOT is my preferred language for quick graph sketches.

All of the code above is stored in my <a href="https://github.com/WayneMRoseberry/DupIQ">DupIQ github repository</a>. 
It is almost certain that if it has been a while since this article was published that some of the code will change, so
consider this a snapshot of a moment in time.
