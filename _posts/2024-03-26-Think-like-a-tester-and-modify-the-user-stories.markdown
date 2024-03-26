User stories are convenient and effective tools for testers, but so often the stories
which drive product design and coding are simplified and short. They do very well
in a demo, are good for guiding a developer or designer for how the product ought
to look and behavior, but are almost always too simple and too easy to work
well as ways to find problems in a product.

When I am testing, I like to put choose and modify user stories based on test
questions. I look for things that might take the product into areas that do not
work as well, don't fit the user needs the way we would want.

I employed user stories to test TestRigor, an AI-driven test case generator that
uses natural language description of test goals to create the steps for a test
case. I have tried prior testing with TestRigor before, but I hadn't written
anything where I targeted the natural language test case generation. I decided
to try that today.

A video of the testing session is also available <a href="https://www.youtube.com/watch?v=rJV0Dck9G6s">here</a>
on my <a href="https://www.youtube.com/@SoftwareTesting-bv6di">YouTube channel, "Software Testing"</a>.

The story behind the approach
=========================================
__The Personna__

A user story often comes with a personna that helps us frame the story, sets
down some assumptions of what that person wants, knows, how they do things.
For my personna, I intentionally chose a non-technical user, a business analyst
at Ford Motor corporation, chartered with creating test cases for their company's
auto-leasing website, Forddrive.com.

I chose a non-technical person because I wanted to focus on part of TestRigor's
goal of helping companies achieve test coverage without having to have trained
and skilled programmers write the automation.

__The Stories: TestRigor__
There are two levels of story in this problem, one is the personna
as a user of TestRigor, and the other as an employee of Ford Motor.
For the first, I am selecting stories that I hope will push TestRigor's
AI case generation features a bit with the following conditions:
- the end user knows none of TestRigor's jargon or coding language, knows nothing of web-page technical constructs
- the user will describe the test without precise stepwise instructions
- the cases will require multiple steps to properly execute
- the cases will rely on checking states that will emerge only after execution of multiple steps

This is kind of a meta-layer on the stories that is entirely based on
my interests in finding issues in TestRigor. In reality, our personna
wouldn't be interested in finding problems with TestRigor at all, so there
is a bit of contrivance in my approach, but I also believe the constraints
describe very realistic stories that the personna would want to test. Read
on to see what I mean.

> It helps to build your user stories with a testing purpose that goes beyond
> the motivation for the stories that were picked by the business.

__The Stories: Forddrive.com__
I chose two basic simple checks based on the existing website at <a href="https://www.forddrive.com">https://www.forddrive.com</a>.
Under each I also indicated a set of expections.

1. Check that the links to leasing locations point to valid and correct pages
- The script will find the Locations menu and its links
- All the links under the Locations menu will be investigated
- The script will check that the correct city name is on the resulting page 
3. Check that the Chat help link opens a functioning chat help session, asks for user name, close when confirmed
- The script will find the Chat help link (not on front page, but on Contact Us)
- The script will launch the Chat window
- The script will check that user name is requested
- The script will close the window when done

> _This check for closing the window is there because I have found from other testing
> sessions in TestRigor that after the test completes browser window sessions can sometimes
> stay open with a live connection and active session for a long time. I want to make sure
> there isn't a chat window left hanging._

I also added a third story that check how TestRigor would do constructing checks
for behaviors that are going to fail, perhaps where the user was mistaken about
a behavior. I modified the Chat story to check for a condition that is not true:

3. Check that the Chat help link opens a functioning chat help session and the chat assistant requires membership id before offering support
