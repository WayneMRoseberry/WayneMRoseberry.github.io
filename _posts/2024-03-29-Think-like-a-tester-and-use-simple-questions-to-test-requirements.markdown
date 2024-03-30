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

Simple Requirements from a Model and BDD:
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

From this, I was able to start my testing.
> Requirements can come in almost any form. They may be diagrams, lists, formal expressions like Gherkin
> entries in a ticketing system like Jira, or large specification documents. You can work with almost
> any format.

Asking Simple questions to review the requirements
================================================
A large set of requirements can be daunting. They are usually long (unlike the short example
in this document, but I will get to that). There are usually complex but
maybe unstated relationships between features and behaviors. There is also
something about reading "_The application will flip the pancake in the air..._"
that numbs the mind into blind acceptance, suppressing questions like "_What if
the pancake wasn't there? What if the pancake wasn't solidly cooked yet? What if
there wasn't enough clearance to do the flipping?_"

One way to simplify it is to ask simple questions. There are
many questions one could ask. For this exercise, I used a
short list:

Clarifying questions:
-----------------------------------------------
- _How does this happen?_ This is useful when an action seems to happen as if by magic. The requirements specify something that is supposed to happen, but do not indicate the steps that made it possible. This is a bug in the requirements themselves, and the clarification will prove useful in further analysis.
- _Where does this go?_ Many bugs in a system fall out of storage or sending data somewhere. Steps after and between storage points are often fragile, and usually drive requirements changes to fix bugs in the design.

Exploratory questions:
------------------------------------------------
- _What happens if failured occured here/now?_ Bugs and new requirements tend to become more apparent if we ask what happens when a given step, action, or requirement fails. Dependent actions, steps and requirements often fail even worse, and we usually find we need some kind of new recovery requirement, or perhaps a different way to meet that requirement which avoids failing in that way.
- _What is the user supposed to do when this happens?_ This question is very effective with failure modes, because very often the user has to take some kind of action, or at least needs to know something. Maybe there is nothing the user can do, and we need them to know that. Maybe somebody else, such as an operator or support person, is the only one who can address the problem. Even for non-errors, sometimes the end result of an action needs to guide the user in a good direction.



