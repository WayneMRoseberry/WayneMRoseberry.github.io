Dear _[[ INSERT TESTER NAME HERE ]]_, you have been selected to win _[[ INSERT PRIZE NAME HERE ]]_!
=====================================
![A picture of a cat playing MadLibs on a whiteboard](/assets/catplayingmadlibs.jpeg)

I have a testing technique that reminds me of form letters, like those mass
mailings for sweepstakes promotions or credit card sales. Even more, it reminds
me of MadLibs, where you fill in words for placeholders.

I stumbled across this technique a day or so ago  in a LinkedIn post when summarizingsome test preparation.
The actual preparation was a page or two of tables that I combined together to come up with test
cases for a new feature I was preparing to add. LinkedIn posts don't allow pages of
material, and I didn't have the time to write a longer article, so I was forced into coming up with
a short format to express the test ideas. Here is the post:

>Sometimes testing design is tedious and straightforward, but like a lot of work, still needs to happen.
>
>I am sometimes surprised in the mundane. There are interesting considerations, things easy to forget. 
>Today I was contemplating the tests needed in response to adding authentication and authorization 
>with a project of mine, and most of it really is _"try << action >> as << role >> when << authorized | unauthorized >>"_, 
>but even that had some optimizations and cases that I felt much better after writing down. 
>The test design also reminded me of some use cases I need to take into account on the feature.

The phrase _"try < action > as < role > when < authorized | unauthorized >"_ summarizes the page
and a half of my tables and matrices in one short statement. I realized everything I was doing
could be crystalized and understood by the readers without having to express any of the details.

The infinite and manageable power of boilerplate
=====================================
I sometimes think of logic or language or ideas as a form of algebra. As much as "mX + b" can describe the slope of an
infinite number of lines with an infinite number of slopes, so too can a statement describe an infinite
number of similiar situations. Consider this statement:

_Take the << animal >> to the << location >>._

 Any version of that statement is about taking some animal to some place, but there are also
 an infinite number of forms that statement could take:

* _Take the dog to the vet._
* _Take the monkey to the zoo._
* _Take the parakeet to 120 degrees north and 45 degrees west._

On first blush, one might have thought we were bound by a finite number of animals and
a finite number of locations, but if you notice on my parakeet example, we
open up the possibility of infinite statements by finer and finer precision on
what we mean by location. If we allow ourselves imaginary animals, or imaginary
specific animals ("Freddy the dog", "Freddy the Third, the dog", "Josephine the three finned wallobat")
we wind up with the same possibility there.

Despite contemplating infinity, we can still wrap our heads around the basic
idea of taking some animal to some place. The structure and content of the boilerplate
makes it easier to imagine. There are implied meanings that come with the boilerplate
that helps us understand the possibilities of outcomes, situations, conditions, and problems.

A testing example
=====================================
My example phrase is from a real testing problem I was working on. Let's look at it again:

_"try __< action >__ as __< role >__ when __< authorized | unauthorized >__"_

The problem I was working on was a REST API that allows reporting of problem reports
(test failures, bugs, etc.) and will automatically determine if the issue is new or
one previously reported. I had most of the functionality implemented to this point, but
I had not added authentication and authorization, the ability to identify who is
trying to use the API and whether they were allowed to do what they are trying to do. I
was imagining the tests I would want to run. I created a list of things I could do with
the system:

- report issue
- search for similar issues
- search for prior reports of an existing issue
- CRUD (Create, Read, Update, Delete) operations on issue reports
- CRUD operations on issues
- CRUD operations on projects
- CRUD operations on tenants
- CRUD operations on users

I then created a list of roles that a user might occupy on the system:

- ADMIN
- Writer
- Reader

 The roles are relative to the following objects on the system, where any given thing a user might try to do may be
 authorized or unauthorized for that role:

 - Service (contains tenants)
 - Tenant (contains projects)
 - Project

I then wrote down a combination of these lists together that multiplied out to
describe several different ways to check the authorization behaviors of different
actions for different types of users. 

It was after making these combinations that I realized they were all of the form:

_"try < action > as < role > when < authorized | unauthorized >"_

It was mostly a list of simple test ideas, although it got complex when considering combinations 
like _"Attempt __(action)__ a write operation in Tenant1-Project2 as a user who __(role)__ is a writer and __(authorized|unauthorized)__ allowed to write in Tenant1-Project1, but only read in Teant1-Project2"_. 
This is the kind of thing that comes out when you take a very mechanical and 
methodical approach to constructing test cases. You start to see combinations implied by the 
blind, naive mixing of variables that might not occur to you when writing the code. In the case of the code,
there is a real risk of a bug in this case because of the way the database is built. It
would be easy for a query to mistakenly check on the Tenant for a user's role and
not also specify the Project, for example, when checking if a user is allowed to
do some operation.

My list of test ideas was not too long, but I found the boilerplate version of
it as a really easy way to describe what the testing would cover. It also forms
a basis for considering more testing. Are they any actions I have not thought of?
What of role relationships? It was during creation of my test data model that I realized
I had forgot to define a role for Service Admin - the user that would create all the other
users in the service.

Are you going up or down?
=========================
In the story above, I started by building detailed lists, combining them, and after the
fact realizing there was a pattern which described them all very simply. In this case I was
working up from the bottom - details to generalities.

I could have come the other direction, going from the top down. I could have started by realizing
that there would be a bunch of testing where users in different roles would try different actions
for which they were either authorized or unauthorized. In this case, rather than having a bunch
of details and trying to make sense of them, I start from the boilerplate statement, the pattern
and try to fill in the variables for Action, Role, and whether the expectation for each combination
is Authorized or Unauthorized.

It doesn't matter which way you start. I know some people who prefer to start with
general concepts and let the details come later. I know other people who much prefer to
look at all the details first and consider generalizations later. It doesn't even matter if
you use either approach at all, this is just a way of expressing, analyzing, and discovering
ideas that might prove helpful. I happen to use it a lot, but not every time for sure.

More Ideas for Easier Test Planning
===================
This idea isn't expressed in my book, but there are plenty of other similar, easy to use ideas in it.

<a href="https://www.amazon.com/Writing-Test-Plans-Made-Easy/dp/1478333693">![Writing Test Plans Made Easy](/assets/writingtestplanscover.jpg)</a> 
