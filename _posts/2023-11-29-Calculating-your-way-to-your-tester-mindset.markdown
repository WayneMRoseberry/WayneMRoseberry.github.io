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
One of the reasons I role play this interview is to give the
candidate a chance to apply skepticism to things other people
tell them.

Pretend the candidate suggested the test "+1 + 3" because
they want to know if the calculator handles leading operators.

I interject.

 > _stopping candidate_
>
> Me: "Let's say after you do that, the application emits a blank line.
> What do you do"
>
> Candidate: "I try it again."
>
> Me: "Let's say it emits another blank line."
>
> Candidate: "I try another calculation. Maybe '1 + 2', something that I know works."
>
> Me: "Let's say it emits another blank line.
> Let's say it from this point forward emits a
> blank line for everything you enter. What do you do?"
>
> Candidate: "I show the problem to the developer."
>
> Me: "The developer says that leading operators are invalid
> input because all the basic calculation operators have to
> take two inputs. The developer says nobody that knows
> basic math would enter such a formula and expect it to work."

A good testing candidate would treat that response with
skepticism. Maybe the developer is correct in so far as what
is important for the application, maybe we do not care. But
do we know that for sure?

A good testing candidate will start to craft a strategy for
how to respond to the developer answer. Maybe there are others
who understand the customer better. Maybe you have prior
customer usage information to present as evidence. Maybe
the failure mode is more extreme than described. Maybe the
tester should try more tests, or research, or investigate.

Whatever they do, they treat what I say with skepticism.

Uses what they know already
-----------------------------------------
The calculator is stripped to simplicity by design. It allows
the candidate to focus on math that they would have known
very well even as a child. I don't want to worry about whether
or not the candidate knows multiple ways to calculate
Nth roots. I want to know how the candidate uses existing knowledge
of math to check calculations.

For example, the commutative law for addition and multiplication states
thats that order of operations is irrelevant. So long as we
are just doing that one operation, we can have any number of values
in whatever order, and they will also calculate to the same value.

This law can serve as a simple oracle that helps us
cover a great degree of complexity. All of the following calculations
can be checked for consistency via this rule:

```
11 + 15 + 72 + 183 + 9 + 0.43 + 65
15 + 11 + 72 + 183 + 9 + 0.43 + 65
65 + 0.43 + 9 + 183 + 72 + 11 + 15
...
```
We can build any set of numbers size N, re-arrange the order
of the numbers, and perform the same operation across them and
every re-arrangement ought to apply  the same final answer as all
others in the list.

We could use that same oracle to check parsing affect on calculation.
Consider the following from the same
data set, which ought to return the same
results as every other row in the prior
set:

```
11+15+72+183+9+0.43+65
11+ 15+ 72+ 183+ 9+ 0.43+ 65
11 +15 +72 +183 +9 +0.43 +65
11 + 15 + 72 + 183 + 9 + 0.43 + 65
11  +  15  +  72  +  183  +  9  +  0.43  +  65
```

Anybody scoffing the likelihood such a set of test data might expose
a bug in the calculator has never tested (or perhaps written)
a parser before.

The above is just an example, and the important part to focus
on is a good tester will bring things they already know and
search for ways to use them to build good test ideas.

They know where the hard parts and error cases are
-------------------------------
Simple arithmetic, let's talk about a simple error case.

Divide zero.

There are any number of other tricky math problems. Order of
operations is fun. If we allow parentheses, parentheses depth
and application of the distributive property get
interesting. Decimal number precision is cool. So are irrational
numbers. If we wanted to, we could spend hours (didn't mathematcians
used to spend years calculating decimal tables for common
problems?) coming up with good tests for any of those.

But let's look just a little bit at divide by zero. Simplest case:

> "How about if we try '1/0', what does it do?"

I like to defer reward for the simple cases. I might say something
like "It displays a message that says 'Divide zero error'."

A good candidate is rarely satisfied. Maybe divide by zero
on such a short calculation was too simple. They offer a few more
designed to make it more difficult for the calculator
to detect when the divide by zero is happening:

```
1/(1-1)
2/(1-1)
0/(1-1)
0(1/(0))
1/((5/3) * -1(6 - 12/6))
... etc.
```

A good tester knows how to make a problem a little bit harder,
or how to explore a black box to see if it behaves differently
based on different conditions that ought to yield similar results.

They smell trouble and intuit inner limitations
-------------------------------
This next example is so predictable, and aligns so well with good
test candidates that it came up almost every time, and doing
well with this problem predicted doing well with the rest.

In almost every interview, the candidate would ask about
size limits. If they didn't at all they would tend to perform
poorly overall, but I would still prompt them if not. Unprompted,
it tended to go like this:

> Candidate: "What is the largest number allowed?"
>
> Me: "The developer tells you there is no limit. You can perform calculations on any number of any size."

Good candidates know this answer cannot be true. I have found
that even when a candidate has no programming experience, knows
nothing at all about how numerical data is represented and stored
inside the computer, some of them will still immediately reject this claim.
They know everything has limits, and those limits have implications.

A good candidate will start exploring. They try to get information
out of me, like what the maximum ought to be. I always reflect
back to them with "What do you do to figure that out?"

What they do then is start enumerating cases with ever larger and larger
numbers. They offer me some seemingly huge example, and I respond with

> "You try that, and it gives you the mathematically correct result."

A really good candidate, even if the have no computer programming
background, knows something very different is happening, even if they
do not know what. Someone with programming experience ought to
realize what is up right away, and I actually hold that experience
against them if they do not catch on.

The technical issue is that if the calculator processes or returns
numbers that exceed the maximum word size of the chipset/platform, then
the calculator application cannot be using the math operations built
into the chip. It is no longer getting its math for free. It had
to do the math on its own. The implications of a math library intended
to do calculations on number of unlimited size, while necessarily capped by
the size of the hardware (memory, storage, what have you) also has to
handle all sorts of problems with memory allocation and de-allocation
associated with values that shrink and grow as the calculation dictates.

As surprising as it may seem, I have had liberal arts majors without
even two seconds of computer programming experience describe that problem
to me. They tell me they are worried about things like storage and changing
data needs as you numbers increase from values like 9999999999...etc....99999999 to the
next digit up simply by adding one to them, or back down as one takes 1000000000..etc...00000
and subtracts one.

It isn't that the test candidate has to design the data structures and
algorithms for an unbounded calculator. It is that they have to intuit
when something is very different from normal and start to piece together when
that is a problem. From there, they start to build a strategy for problems
that might have never existed otherwise. One might have never worried (much) about
memory leaks or garbage collection optimization on a simple calculator app
until contemplating the implications of unlimited numbers.
