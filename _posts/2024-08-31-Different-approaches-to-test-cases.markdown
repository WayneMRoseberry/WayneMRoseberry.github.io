
# Different ways of representing test cases
The following list of different ways of representing test cases go from high detail/high cost to low detail/low cost.
- Fully-described test cases in list form
- Titles only test cases with shared steps
- Grouped test cases with shared steps
- Grouped test cases with implied steps
- Test case ideas and themes

Over the years, I progressed from high detail and high cost lists of test cases, 
steps fully described, to low detail and low cost groupings of test case ideas, 
relying on the knowledge and skill of the tester to understand the steps without me writing them down.
I also sometimes cover test case ideas and themes and know a lot of testers that
get a lot done with that low level of detail.

I describe here how I view these different representations. One may find a reason to prefer or
need each, although I have also found that sometimes people represent their test cases in
a way that is not suitable for the problem they are working on or situation they find themselves in.

# Fully-described test cases in list form.
Test cases fully described in lists represent the most formal, detailed format for test cases. Every
single instance of something to test where conditions or inputs are different is
described as its own test case with its own steps. Steps are detailed, permitting little to
no variation.

**Title**: Do thing to the stuff with value = "John Smith"
1. open app
2. click on Page1 from top ribbon
3. click "Add thing" button
4. first name, enter "John", last name, enter "Smith"
5. click OK
   
**EXPECTED**
List of things has one new row with First Name == "John", Last Name == "Smith"

**Title**: Do thing to the stuff with blank last name
1. open app
2. click on Page1 from top ribbon
3. click "Add thing" button
4. first name, enter "John", last name leave blank
5. click OK

**EXPECTED**
List of things has one new row with First Name == "John", Last Name is blank

The usual goal for this approach is to have a set of instructions which may be followed
by someone (or something) that knows nothing of how the product works. The cases could be
given to anyone and that person would know how the case is executed. This approach is used
when cases are passed around a group of people who do not know the product well, when
some other entity has demanded such a format for review. Such test cases are almost always
stored in a formal test case management system of some sort as a separate entry per case.

This approach is difficult to maintain and tedious to review. It is difficult to
understand a larger context when reading the case details. It is difficult to visualize
what is changing from case to case. Small changes in the product behavior invalidate large
collections of steps, demanding suite maintenance over time to keep the cases relevant and
up to date. The approach sometimes has the effect of limiting the imagination of testers
who follow steps and procedures blindly without looking beyond the recorded actions and
expectations to explore new possibilities, record unanticipated problems.

# Titles only test cases with shared steps:
Titles only test cases maintain the same one test case per variation pattern
as fully-described test cases, but the steps for the test cases are shared centrally
and authored in such a way that the steps adapt to the changes in test conditions and inputs.
Actual steps are stored in separate collateral from the test case itself.

**Title**: Do thing with stuff with First Name = "John", Last Name = "Smith"
- see "Do thing to the stuff with different values" steps in test plan

**Title**: Do thing with stuff with First Name = "John", Last Name = <blank>
- see "Do thing to the stuff with different values" steps in test plan

In test plan:
**Title**: Do thing to the stuff with different values
1. open app
2. click on Page1 from top ribbon
3. click "Add thing" button
4. enter value for fields specified in case
5. click OK

**EXPECTED**
List of things has one new row with columns corresponding to test case having matching values

The goal of this approach is to preserve the same degree of ability to have someone that does not
know the product execute the cases, as well as case by case accountability, but with reduced document
maintenance cost.  It means that along with cases stored in a case manager, the people running or reviewing the cases
will need access to the test plan document somewhere.

There is still a high cost of case creation and maintenance for the planning documentation, and even the
test cases themselves. Each case needs entry in the case management system, even if full step articulation has been
removed. The original steps need to be written, and need to be changed as the product changes.

Sometimes this approach is used in conjunction with less expensive approaches when the test procedure steps are not obvious,
or must be exceptionally precise. That sometimes happens when a test problem is very difficult to understand, even for
an expert.

# Grouped test cases with shared steps

**Title**: Do things with stuff and different input values

In test plan:
**Title**: Do thing to the stuff with different values
1. open app
2. click on Page1 from top ribbon
3. click "Add thing" button
4. enter value for fields specified in case
5. click OK

**EXPECTED**
List of things has one new row with columns corresponding to test case having matching values

**Cases**:
First Name, Last Name over values of ["user1", empty, " " <space>, "1234"] each

# Grouped test cases with implied steps

**Title**: Do things to the stuff with different values
Apply the following set of input values to adding a new thing to the stuff list, and check if the list view shows the new item with the values specified.
**Cases**:
First Name, Last Name over values of ["user1", empty, " " <space>, "1234"] each

# Test case ideas and themes

**Title**: Different values for thing across input spaces
Explore affect of different data across the following features allow user to view or edit Thing in some way 
- new thing control
- stuff list
- select thing action menu
- display thing on notifications
- assign thing control

**Cases**:
First Name, Last Name over values of ["user1", empty, " " <space>, "1234"] each
