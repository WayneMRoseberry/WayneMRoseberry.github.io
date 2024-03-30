Testing from requirements is a great way to get ahead of a problem. We can only
test something that exists, and at the requirements stage the thing only thing
we have that exist are our ideas. If we can find defects in the ideas, we can fix
them before the ideas become product code. The hard part is finding flaws in something
that we cannot watch run. We have to imagine issues and problems that are suggested
or implied or inevitable from the requirements.

In this article I describe a requirements testing exercise I engaged in with
a project of my own. I use a few techniques that were effective and relatively
easy ways to expose problems not immediately visible in the first iteration
of the requirements. Those techniques are:

1. walk through any steps described in the requirements
2. ask for clarification
3. ask "what would happen if this step failed?"
4. ask "what is the user supposed to do at this point?"
5. ask if new issues indicate new requirements

Let's look at the example.

System Under Test: A duplicate issue reporting detection application
=================================================
I have a project I call DupIQ that detects duplicate issue
reports coming from automation, bugs, or customer feedback. It gets
embeddings for the incoming report, and then searches a vector
database for prior issues. If any issues are above a certain similarity
threshold, the incoming report is marked as a duplicate of that issue,
and the report is written to an issue database. If no prior issues are
above the similarity threshold, a new issue is created, the embeddings
are stored in the vector database, and the new issue and report
are stored in the issue database.

Simple Requirements form a Model and BDD:
================================================
I created a model using TestCompass to describe requirements for
the "Report Issue" feature.

![A flow chart depicting a simplified state model for DupIQ's "Report Issue" feature](/assets/dupiq_reportissuemodel.png)

Instructing TestCompass to create a 100% path traversal of the model,
we can then export the cases as a Gherkin formatted description of
the requirements:

> ```
>  Scenario: Report Issue - TC1
>    Given: an issue has been reported
>    When: top issue similarity > threshold (YES)
>    Then: Link report to prior issue
>    And: Write Issue Report to Database
>
>  Scenario: Report Issue - TC2
>    Given: an issue has been reported
>    When: top issue similarity > threshold (NO)
>    Then: Write new issue embeddings
>    And: Link report to new issue
>    And: Write new issue profile
>    And: Write Issue Report to Database```

