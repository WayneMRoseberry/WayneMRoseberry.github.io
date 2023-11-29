Getting all mathy to think like a tester
========================================
This is article is part of a series where I describe
about interview questions I used to use to
check a testing candidate for strong testing
behaviors.

This question tends to do well at prompting candidates
to demonstrate either knowledge of, or intuition
for, formal testing construct with data
and processing of information. It requires some
degree of basic mathematical aptitude, probably
middle school level, but also some sense of where
realistic physical limits of computers and
problem solving have to introduce difficulty,
complexity, and most probably bugs.

We imagine a calculator...
========================================
I don't have a working code example for this
problem, because I exploit the free form
nature of role play to introduce conditions
that the candidate brings up. I modify parameters,
make up pretend behaviors so I can observe
their responses. If I had a real application, I
wouldn't have that freedom to explore their
behaviors.

The script goes like this:

> "Imagine you are testing a command line
> calculator application. Whatever you
> type it treats as a mathematical formula
> and dislays the result of the calculation
> on the next line after you hit the RETURN key.
>
> For simplicity sake, assume the calculator
> only supports the following operations:
> addition, subtraction, multiplication, and division.
>
> How would you go about testing this application?

I watch, listen, take notes and respond as they
describe what they want to do.

Green Flags
========================================
Good testing candidates tend to stand out strongly
when given a problem of this class. I have another
article where I describe a UI-based interview
question, and the green flag responses are similar. Sufficient
number of them tend to outweigh the red flags. It
is rare you get a mix, but even when you do, there
is often enough promise from just a couple
green flags that you can signs of a promising tester.

Asks clarifying questions
-----------------------------------------
Testing challenges assumptions, often by testing the
assumptions directly. A good tester recognizes that
many of our the mistakes and errors in product
design or coding come from from mistaken assumptions.
They also recognize that mistaken assumptions can
blind us to problems, or lead us to believe a desired
behavior is incorrect. A good tester will almost
always lead with questions. Some examples I see a lot
for this question:

>- What is the product used for? Why a calculator on a command
> line where there are so many calculator solutions?
>
>- Who is the primary user of this application? Are they mathematical experts
> or more like novice users? How much technical and domain
> competence can we assume they have?
>  
>- What are the ranges of valid inputs? What are the invalid inputs?
>
>- What environment do we expect to run this in? Just Windows,
> or other platforms? Does it only run form the Windows
> command shell, or should it work in other similar command
> shell environments?
>
>- What performance expectations do we have for this? Is it meant
> to run just one user on a machine at a time, or are we exposing
> this in a shared fashion for many people to run at once? Are there
> any data and speed expectations with regard to calculation size,
> complexity, memory persistence, running calculations, cached results?

The possible questions are endless, and almost any direction of questions
are good. What you want to see is the candidate express ideas
that might affect expectations and outcome, new test ideas.

Fights back against something I tell them
-----------------------------------------


Use what they know already
-----------------------------------------
Built into the design of the
