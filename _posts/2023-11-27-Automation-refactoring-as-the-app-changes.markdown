Let's look at some automation
=============================
![A picture of a beaver working on tests](/assets/beaverwritingtestcode.jpg)
This last week I added authentication and authorization to
a REST API project of mine. I had some end to end tests covering
basic functionality of the API already built, and those tests
needed to change to continue working. In this article, and at the risk
of embarassing myself by displaying my code, I am going to talk about
some of those changes.

An Example Method, GET_ReportIssue
============================
As I stated, these tests run end-to-end. I start up an instance of the
REST API locally on my box, and the tests use the application via the endpoints on
the box. 

Pre-Authentication
----------------------------
Prior to authentication, one of the test methods looked like this:
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
Get_ReportIssue_allroles. A bit of a misnomer, as there are technically
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

Some observations on the code and how it changed over time
======================================
One of the reasons I wanted to write this article was to give some real
examples of how coding patterns for test code can change over time depending
on need and convenience.

Just do it hard-coding versus parameter flexibility
-------------------------------------
The first iteration of the test method is very much "just do it." Values are hard-coded
instead of parameterized. They are very much one-and-done without much re-use and flexibility.
A good example of this is the way values like the service admin claims token, or
the tenant id was hard coded in the script, as if they would never change over the life
of the suite. That changed as the feature set changed, and testing requirements
needed to cover more testing conditions. Test methods were refactored to take
parameterized instead of hard-coded values.

Show all of it to me right here versus simplify with helper libraries
-------------------------------------
My first version of the GET_ReportIssue test method put all the steps in the
method. This is a pattern I use with unit tests, where quickly seeing exactly what is
happening without having to scroll or even move the eyes around the screen
is a huge benefit. Even though the GET_ReportIssue test method was end-to-end
and using a lot of extra support (WebRequests, WebResponse), it was easier to
leave all that there while the code was short.

As the number of interesting test conditions increased, it became more important
to simplify the view. The focus was moving from step by step analysis to which
permutations and conditions were under test. Shared code also became more important,
wherein changes and fixes to one piece of code logic would need to apply to every instance,
and the code logic was starting to get more complex. This is when I chose to refactor the
body of GET_ReportIssue into a helper routine.

How about those exceptions?
-------------------------------------
This isn't so much a change over time as it is a matter of decisions I made about what
constructs to use to execute the requests and check outcomes. MSTest has a very
convenient attribute, ExpectedException, which makes it very easy to indicate that for
a given method a throw is expected. It is not as easy, however, to check further data
on the exception, such as content of an exception message.

I have another pattern I use a lot which I might adopt here. It looks like this:
```
try
{
    DoThingIAmTesting(stuff_that_ought_to_throw);
    Assert.Fail("Should not get this far because app should have thrown.");
}
catch (SpecificExceptionIWant e)
{
   Assert.AreEqual("some value inside the exception...", e.ExceptionParameterIWantToCheck, "This is how I check exact exception behavior.");
}
```
This is far less elegant than the ExpectedException attribute, but it allows for very fine
grained control when checking the exception.

Check one thing at a time, right?
-------------------------------------
For sake of shorter spans of text, I let you off easily. The GET_ReportIssue test
method was really quite short. Some of the other test methods are longer, requiring
calls to three or four API methods as they string together for initialization, condition, and validation.

This is something that bugs me. I don't really like uber tests either when it
comes to suites that check app behavior. But I also find the "check one thing at a time"
rule ridiculously naive. Yes, it makes investigation easier. Yes, it tends towards
suites that run more consistently. Yes, it scales out test execution better, allowing
more execution in parallel, and thus faster return on longer suites. There are bugs that
live in the connections of features strung together, and it is the end-to-end suite where I like
to do all that stringing.

Over the course of working on this suite I have data corruption, orphaned and widowed data,
sql connection leaks, 3rd party suite timeouts, and a variety of other conditions that only
shook out because I was stringing multiple features together. Not long sequences, but definitely
a long cry from "check one thing at a time."

Why not save time and write it all at the end?
-------------------------------------
One way to interpret this article is that by taking an incremental, one change to the
tests at a time approach, I was burning a lot of time changing and updating tests.

Another way to interpret this article is that by accepting the cost of changing and
updating tests, I had a suite I could run as I went to check basic functionality.

I have a lot of people argue both sides. Don't write the end to end tests until the
code is done versus get the code in as early as possible to get code stability under control.

I chose the latter. I already had a substantial unit test suite covering basic
functionality, and an integation suite getting to address IO and data storage
integration. But even then, introducing the REST API and all the complexities of
stringing underlying functionality to expose what a user would need to do created
conditions that a full end-to-end test to cover it. The more functionality I exposed in
the UI, the more I found myself fixing bugs at all layers, and the testing burden
I picked up as I went. I needed something I could run quickly that covered at least the basic
end-to-end API functional points. This meant writing a suite a bit early in the REST
API stage and changing it as I hooked in more and more of the finishing pieces like
described here.

About the code and my projects
====================================
The project I reference is one I have been working on since beginning of the summer
of 2023. I describe it <a href="https://waynemroseberry.github.io/Projects">here on my projects page</a>, but a
short version is it is an open-source project that uses ML trained models (nothing big, just
word embeddings) to match automation failures. It is a topic I intend to cover in a later article,
but the ability to automatically detect, in real time, when automation failures are
new or duplicates of prior failures, was a game changer in the Microsoft Office team.
