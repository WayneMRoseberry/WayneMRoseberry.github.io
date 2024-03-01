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

Testing EmbeddingIssueProvider.getIssueReport()
==================================================
