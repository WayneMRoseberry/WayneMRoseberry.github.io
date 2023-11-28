Let's look at some automation
=============================
This last week I added authentication and authorization to
a REST API project of mine. I had some end to end tests covering
basic functionality of the API already built, and those tests
needed to change to continue working. In this article, I am going to talk about
some of those changes.

An Example Method GET_ReportIssue
============================
As I stated, these tests run end-to-end. I start up an instance of the
REST API locally on my box, and the tests use the application via the endpoints on
the box. 

Pre-Authentication
----------------------------
Prior to authentication, one of the tests looked like this:
```
[TestMethod]
public void GET_ReportIssue()
{
    string testMessage = "somethingnew";
    string requestUri = IssueIdentityApiTestsHelpers.BuildReportIssueGetRequestUri(testMessage, UriBase, _sharedTenantId, _sharedProjectId);
    WebRequest webRequest = IssueIdentityApiTestsHelpers.CreateGetRequest(requestUri);
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
It is difficult to tell, but the line that says "CreateGetRequest" which builds the WebRequest object
doesn't add an authentication header. The endpoint was allowing anonymous users to make the call.
```
static internal HttpWebRequest CreateGetRequest(string requestUri)
{
    var request = (HttpWebRequest)WebRequest.Create(requestUri);
    request.Method = "GET";
    request.ContentType = "application/json";
    request.Headers.Add("Access-Control-Allow-Origin", "*");
    return request;
}
```
Preparing for Authentication, JWTBearer Token
----------------------------
Before the website could do full authentication, knowing who a person is, and
authorization, knowing what they are allowed to do, I need to setup basic
check that authentication and authorization mechanisms were in place, even
if they didn't do anything yet. I added JWT token support to the website. This
meant that any user accessing the site needed to have a token to use any
of the features. I added an API to issue tokens - not checking just yet who
the user was - and then marked the API as requiring the token. This meant I
needed to add supporting code to my automated suite to get a token.

To this point, all the tests were running as an anonymous user. I also
had to create a service admin user to initialize the system settings.
The test code fetches the service admin token and stores that in
a private local variable. The call to CreateGetRequest was modified to
take that token as a parameter.

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

Multiple User Roles
----------------------------
After changing the system to require a token, I changed the system to require a password. This
meant a change to the code that fetched the user claims, but otherwise the rest of
the test code was the same. I decided at that time to also add multiple user
identities on the system with different roles. Some of those roles include
a tenant admin (the service divides up into different data regions called tenants), tenant writer (can
publish issues) and tenant reader (can read issues, but cannot write them).

I chose to add a test which accesses the features as the different role types
prior to the system enforcing role levels. I added a new test method I called
Get_ReportIssue_allroles. A bit of a misnomoer, as there are technically
more roles beyond the ones I put in the script, but they represented the
different levels where authorization might change. I also made them distinct
from the basic Get_ReportIssue which was using the serviceadmin role.

More important, I refactored the code. The Get_ReportIssue method
is substantially shorter:
```
[TestMethod]
public void GET_ReportIssue()
{
    string userToken = _adminToken;
    GetReportIssueForUserToken(userToken);
}

[TestMethod]
public void GET_ReportIssue_allroles()
{
    Console.WriteLine("Check with tenant admin token.");
    GetReportIssueForUserToken(_tenantAdminToken);
    Console.WriteLine("Check with tenant writer token.");
    GetReportIssueForUserToken(_tenantWriterToken);
    // this test was written before enforcing readonly on readers. The next step will fail when that
    // change is implemented.
    Console.WriteLine("Check with tenant reader token.");
    GetReportIssueForUserToken(_tenantReaderToken);
}

private void GetReportIssueForUserToken(string userToken)
{
    string testMessage = "somethingnew";
    string requestUri = IssueIdentityApiTestsHelpers.BuildReportIssueGetRequestUri(testMessage, UriBase, _sharedTenantId, _sharedProjectId);

    WebRequest webRequest = IssueIdentityApiTestsHelpers.CreateGetRequest(requestUri, userToken);
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
I chose to be a little sloppy with my test design. The reader role only works
temporarily. Once authorization is implemented, the ReportIssue method is supposed
to return a 401 Unauthorized exception. The script above, as written and noted
in the comments, would fail once the authorization was put in place. I went with that anyway.

Implementing Authorization and More Test Script Changes
----------------------------
I got ahead of the change on this one. After I submitted the prior code to the repository, I
changed the test code to expect a throw (the .NET WebRequest object treats an HTTP 401
request as an exception, and not a very specific one at that.. so the code below is
imprecise and has a false negative if the service ever threw for a different reason - I should
probably fix that...) when the user is a reader and tries to submit an issue report.

I also added implemented cross-tenant authorization checks so that users from one tenant
would not be prevented from writing/reading data in a different tenant, but also still
would be able to access their own. Note how I added a new supporting method that takes the
tenantid as an argument. This was accomplished with a simple refactoring on the existing helper (not shown), which
now calls the new helper method.
```
[TestMethod]
public void GET_ReportIssue()
{
	string userToken = _adminToken;
	GetReportIssueForUserToken(userToken);
}

[TestMethod]
public void GET_ReportIssue_allroles()
{
	Console.WriteLine("Check with tenant admin token.");
	GetReportIssueForUserToken(_tenantAdminToken);
	Console.WriteLine("Check with tenant writer token.");
	GetReportIssueForUserToken(_tenantWriterToken);
}

[TestMethod]
[ExpectedException(typeof(System.Net.WebException))]
public void GET_ReportIssue_reader_shouldthrow()
{
	Console.WriteLine("Check with tenant reader token.");
	GetReportIssueForUserToken(_tenantReaderToken);
}

[TestMethod]
[ExpectedException(typeof(System.Net.WebException))]
public void GET_ReportIssue_tenantadmin2_tenant1_shouldthrow()
{
	Console.WriteLine("Check with tenantadmin2 token.");
	GetReportIssueForUserToken(_tenantAdminToken2);
}

[TestMethod]
public void GET_ReportIssue_tenantadmin2_tenant2()
{
	Console.WriteLine("Check with tenantadmin2 token.");
	GetReportIssueForUserTokenAndTenant(_tenantAdminToken2, "tenantadmin 2 reporting new issue", _sharedTenantId2, _sharedProjectId2);
}
```
