At some point most applications need to write code that reads
from or writes to IO. Anything writing over the network, to a
file, to a database. When you do this, actions you may have
previously mocked become "real", by which I mean the data
is still there are after you do the operation, and it will
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

Code without IO
==============================================

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

Code with IO
==============================================

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
