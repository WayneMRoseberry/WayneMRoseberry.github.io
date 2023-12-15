Sometimes testing is about methodical analysis
==================================
![Image depicting a tester writing test ideas on a whiteboard](/assets/testerwhiteboardtestcases.jpg)
This is an article in a series where I describe
interview questions I used to use to screen
testing candidates. My intent is to demonstrate
some of the behaviors I observed in candidates
who did particularly well, and contrast them against
behaviors for those that did poorly. I hope
someone reading this might find it useful
to adopt a mindset, or practices, which make
for productive testing.

The question in this article is a coding problem.
I used it for SDETs. It is a difficult coding
problem, and I found almost without fail over
more than a decade using this question that if
the candidate did poorly on the testing part
of the problem they never got the algorithm
correct. It was one of the most reliable predictions
of success and failure I have ever seen.

The nature of the testing on this question
is very analytical, very targeted and specific. It
does poorly for broad, open-ended thinking. It
does not touch at all on systems thinking. It
is an excellent screen for methodical, organized
problem solving.

It was the question presented to me by Gerry Weiler,
the manager of the MSN 1.0 testing team back
in 1994 when I interviewed for a position on the
team. It was a real problem his team had to
solve on Microsoft's Macro Assembler several years
before.

Let's pretend you are working on an editor that has to support English and Japanese text...
===================================
I would introduce the problem as follows:

> You are working on a program that accepts text in both
> English and Japanese format. The text is encoded in a
> scheme called Shift+JIS that supports a mixture of
> single byte and double byte characters in the same stream.
> An English character has the high bit of its single
> byte set to 0. A Japanese character has the high bit of its
> high byte set to 1, while the remaining bits can be one
> or zero.
>
> We need a function that determines if the last character
> in a buffer in Shift+JIS format is English or Japanese, one
> byte or two bytes. The buffer is defined as a byte pointer to
> the beginning of the buffer, and a byte pointer to the end of the buffer.
> The function should return 1 if the last character in the buffer is
> English, and 2 if the last character in the buffer is Japanese.
> An example of the function declaration is here:
>
> ```
> // returns:
> //    1 if last character is English
> //    2 if last character is Japanese
> //    0 if buffer is invalid
> int NumBytes(byte * pbBeg, byte * pbEnd)
> ```

Spoiler alert: if you like to solve problems on your own, then the section 
titled "The test cases get to the heart of the problem" might ruin
it for you. It doesn't show the code (I save that for the end of the
article), but the test cases in that section make the answer almost
obvious, and that's no fun for the puzzle solving inclined.

Green Flags
===================================
The green flags in this question are very targeted, very specific
to coding interviews, especially for testers that are going to
code a lot.

Writes the tests first
-----------------------------------
This is not critical, because some candidates write the code
first and test after and do a decent job of it. But every
single candidate who wrote a successful algorithm did
so after they wrote out the test cases - they completely
re-wrote the code after the test cases were already
there. The candidates who started with the test cases would
fly through the actual code.

The test cases are methodical
-----------------------------------
Methodical can vary a lot. Some people wrote test cases that
might not have been ordered neatly and organized cleanly, but
there was a structure to which cases the candidate considered next.
Some people would write test cases in neat, clean lines and
rows of tabular data. Some chose tables, some chose trees, some
just wrote short phrases of their data sets.

Methodical and organized cases, for this question, leaned
heavily to success. There is a class of bug that almost
every person who tries to solve this problem will introduce,
and methodical analysis of the problem tends to bring
the potential for that bug forward. Which brings us to the
next point...

The test cases get to the heart of the problem
-----------------------------------
With most coding questions, there is some fundamental problem
that is the heart of the algorithm, the thing it does, the
little twist that makes it not as trivial to solve as it
seems. With some problems, the developer can miss that key
challenge, make bad assumptions, and introduce a defect
they are completely oblivious too.

A good tester, who in this kind of situation might be
the programmer themselves, needs to see this fundamental problem
separate from the algorithm. If we think of loops and if conditions
and else statements and recursion and whatnot, we start to
impose assumptions separate from the problem in the raw, without the code.
If we can think about the problem itself, and ask "what does this imply?"
we start to see critical patterns.

An example of good test cases for this problem might look like the following.
In this case, imagine that the candidate is representing only the
high bit instead of the whole byte, and that the strings of ones and
zeroes each represent the end of a single buffer:

> Buffer... expected result
> ```
>   0 - English
>   1 - Invalid?
>  00 - English
>  01 - Invalid?
>  10 - Japanese
>  11 - Japanese
> ```

Let's pause for a second. A good test candidate does not stop with the above
tests, but there are some important points to talk about in the selection above.
The methodical order of tests and layout make understanding the expected
results better.

Also notice how the invalid case naturally falls out from the analysis. Most
candidates ask me "what is invalid?" and I tell them that is for them to
figure out. The data scheme as it is defined intrinsically introduces an invalid
case when a single byte character has a high bit set to 1. The test case layout
makes that apparent.

It is critical for the candidate to keep going. I will cover this more in
Red Flags below, but let's continue with this example.

> ```
> 000 - English
> 001 - Invalid
> 010 - Japanese
> 011 - Japanese
> 100 - English
> 101 - Invalid
> 110 - English
> 111 - Invalid
> ```

A pattern is emerging in the data that is difficult to spot in this form.
There is also a behavior I typically see as the candidate tries to build the
cases. When they try to decide what the expected result ought to be, they
sometimes deal with this case...

>```
>                  0 0 English
>                  1 0 Japanese
> <don't know yet> 1 0 ??
>```

The candidate typically starts adding 1s and 0s to the left of that last
row. What is it supposed to be? English or Japanese. They don't know how
to assign the expected result. They start to wrestle with a dilemma built
into the data scheme.

Eventually, the candidate creates something like this:

> ```
>     0 English
>    10 Japanese
>   110 English
>  1110 Japanese
> 11110 English
>
>    00 English
>   010 Japanese
>  0110 English
> 01110 Japanese
> 
>     1 Invalid
>    11 Japanese
>   111 Invalid
>  1111 Japanese
> 11111 Invalid
> ```

At this point, the answer to the algorithm is almost screaming at them.
I will show the algorithm later in the article, because the
important part here is the test analysis.

Good candidates get to the example cases above in different order, direction,
style. I really like the tabular approach, but lots of other styles
succeed. The key accomplishment is to analyze the data scheme
and identify the important problem implied by it.

This is not a one-off example of an oddball problem. Data formats
and schemes and logical constructs and relationships between
features in applications have these sorts of relationships all
the time which imply critical meaning for constructing
test conditions that will target high probability bugs.

I had one candidate who, when I asked him this problem said
"Our developers had to solve this where I worked. They were
never able to get it completely right..." The existence of a
real world account of this problem being wrongly implemented
I feel is a demonstration of how powerful and important good test analysis
is on detailed level. With a good set of cases, built for specific
reasons based on in-depth analysis, the failure is not some
shrug your shoulders situation. The fix can be clearly articulated.

Willing to go outside the box and not quite satisfied
---------------------------------
This question feels at first like the opposite of outside the
box thinking. The data inputs are tightly defined, as is
the desired behavior. The analysis I describe above is very methodical,
very step by step.

A lot of good candidates don't trust their initial analysis. In the
prior section I describe an idealized thinker, almost an automaton
in their organized approach. I saw some candidates who were that
precise. Most candidates, including myself when I answered this question,
were not. Most candidates will approach the problem with a shorter
list of cases and assumptions - and then the good ones will
express some degree of concern, or distrust of what they just did.
They satisfy this concern by tossing a more sporadic mix. Consider this example:

> ```
> 0 English
> 
> 10 Japanese
> 
> 11 Japanese
> 
> 000000 English
> 
> 010101 -- what? Invalid?
> 
> 010100 English
> 
> 011111 -- ?? Invalid?
> 
> 0111110 Japanese
> 
> 010110 English
> ```

The distrust and skepticism, the concern they are not doing enough
is a green flag for test candidates. My randomness example above
is contrived to paint a picture of a candidate who picks spurious
values to shake their head out of what they are locked into. They
are "tossing things at the wall" to see if any of their prior
assumptions are violated or if they see new patterns. In the fifth
case, the candidate ponders if the case might be invalid. In the next
one, they try out changing that last bit, and it seems it ought to
be English. Then they get back to another Invalid, and play with
the buffer values to find how to change the Invalid back to Japanese
and then randomly change something upstream that flips it English.

The candidate is on to something, but hasn't solved the algorithm
yet. That is not so much of a concern, because the important
piece of information from testing mindset angle is that they are
still searching. Settling for the test cases they have written
already is not good enough. They have no reliable method yet
to predict the correct response and that is bothering the tester.
This demonstration of an inclination to keep looking, as well
as having ways to shift their perceptions away from
locked assumptions is a predictor of a good tester.

Red Flags
===============================
Some of the red flags on this question present so strongly
I want to shut the interview down the moment I see them. I don't
because if a person does not do well I want to learn from the
experience during the debrief, and I need a full sample of
their behavior to give them that information. Some of the
red flags are corrected by the green flags. I will start with
the less damaging red flags.

Writes the code first
------------------------------
Lots of candidates want to dive immediately into code
without considering test cases. They confidently implement
what they believe is the correct algorithm and
present that as the answer.

Most of them write the following
code (let's pretend we have a function which checks
the high bit given a byte pointer and returns a boolean
true if set):

```
if (ishibitset(pbEnd))
{
  if(ishibitset(pbEnd--))
  {
    return 2;
  }
  return 0;
}
else
{
  return 1;
}
```
If you have been following the article thus far, you should recognize that
the code above is broken for at least 50% of the possible inputs. The above
algorithm is as if the candidate imagined the following possible test
conditions and wrote the code to handle only those conditions:
```
  0 English
  1 Invalid
 10 Japanese
 11 Japanese
```
When I ask the candidate "Does this code work?" That is where I get the
split between a good tester who for the rest of the interview shows
me green flags, and a candidate who continues with more red flags.

"Yup, that works"
------------------------------
This red flag waves strong. I nudge and hint and try to give themselves
a chance to rescue themselves, but I rarely see them take the bait.
Rather than walk through their code, rather than try some
test cases, the candidate stares at the code and declares it good.

Most of the time, after strong hinting and nudging, the
interview turns into a learning opportunity, where I demonstrate
the kind of test analysis I am looking for and how it demonstrates the bugs
in the code.

Writing the same tests they wrote the code to handle
------------------------------
This red flag is a concern if the candidate never proceeds to a green flag.
They go to the whiteboard and write down the test cases:
```
  0 English
  1 Invalid
 10 Japanese
 11 Japanese
```
Rather than separating their mind from their code and thinking about
the core problem in the data scheme, they repeat what the code does,
meaning the test suite is doing nothing more than propagating the
same assumptions that the bug comes from.

At this point, the candidate either switches to a distrust/skeptism
behavior and starts adding cases (green flag), or they revert to "Yup, that works."

Sloppy test case design
------------------------------
A candidate does not need to be clean and neat and orderly to
succeed, but they need to have some degree of rigor to find
the answer. Sometimes the candidate's management and organization
of their test analysis is so disorderly they confuse themselves.
They forget what expected values are for certain cases, they
get the correct results wrong.

The most shocking and surprising and "What are you doing?" thing
I have seen a couple of candidates do is write a case, think about it,
write down the correct answer, ERASE THE CASE, and then write a new one.

ERASE THE TEST CASE!!! THEY ERASED IT!!! WHAT THE HECK?

I have never seen disorderly test analysis lead to
a successful solution to this particular problem.

Getting into the tester mindset for analytical problems
=============================
Not all testing problems are about methodical, detailed and intricate analysis, but many are.
I find when testers avoid this kind of problem they leave the feature they are testing open to
a set of bugs that haunt the product for a long time.

This is also the kind of testing problem developers CAN be very good at. But I have
found from many years of asking developers this question that a
developer who wrote the code first can very easily get the test cases too
short and miss a large set of defects in their core algorithm. But
this kind of problem is SO closely tied to the core algorithms and application
logic that I believe it is an important muscle for developers to build. I believe
this is the kind they can, and should, excel at if they can just avoid a few
traps. Let's use the green flags to consider the techniques that well in the interview:

- write the cases first, or try to imagine the test problem as if you don't know the code
- methodically analyze the test problem
- search for a "heart of the problem" and design cases to exericse it
- amend the cases with ideas outside your methodical analysis

Let's think of the red flags:
- distrust your inclination to say "yup it works", address by forcing yourself to test the code
- distrust your inclination to believe you thought of everything in the code,
  invest some time thinking of cases other than what the code explicitly looks for
  even if they feel redundant or already handled
- address hard to read or understand test case design by forcing some order on your test
  case design by forcing some order and structure to its layout

The red flags are a suggestion, a hint, that there is a bug in your code.
Telling yourself "that works" without having watched it work suggests a
superficial analysis that probably was in force when you were writing the code.
Believing you thought of everything when you wrote the code suggests incomplete
assumptions the code does not address. Disorder in the test case design suggests
some level of confusion when thinking about the what the code needs to do. Pay
attention to the red flags.

The above paragraph describes a mindset that something must be wrong, there
must be a mistake somewhere. This is part of the tester mindset. You want to
get into the practice of adopting that way of looking at things.

The solution - spoiler alert, in case  you like solving problems yourself
=============================
I am going to describe the algorithm in pseudo code rather than write it out
in a specific language. 

In the green flags test cases above, there was a pattern apparent in the test suite.
The expected values were flipping back and forth between either Japanese and (English|Invalid)
based on the number of consecutive hi bits set preceding the last byte in the buffer.
An even block of consecutive hit bits set to 1 would all be Japanese bytes paired up, meaning the
byte was alone and either English (0) or Invalid(1). An odd block of consecutive high bits
mean that the last byte in the buffer is paired with the preceding byte and thus
it has to be Japanese. Whether the position to the left of the consecutive hi bits is beginning
of buffer or a low bit (0) does not matter. This means the algorithm is as follows:

```
 if found error conditions like bad buffer pointers, crossed buffer pointers return 0

 scan next to last byte back to either a 0 in hi bit or beginning of buffer, counting bytes

 if number of bytes is odd - return 2 // paired with last byte
 if number of bytes is even  // last byte stands alone
    if last byte hi bit is 0 return 1
    else return 0
```

I almost do not care if the candidate gets the above algorithm exactly correct.
A good test suite that will break almost every bad implementation is a strong
indicator of a good hire.

The weakness of this particular interview question is in how targeted and precise it
is. It reveals one very specific thing about how well the candidate works with detailed
and analytical problems. It demonstrates nothing about how they do with open ended test problems,
challenging assumptions from others, thinking about heavily user oriented problems. I always
carefully picked this problem only if I knew what the other candidates in the interview
panel were going to ask.

This problem is also very puzzle problem in nature. I have some reservations on questions
that really heavily on intuition and moments of brilliance. The whole leetcode arsenal of
questions feels the same way to me. The methodical analysis part
of the solution redeems it somewhat, but there are times I feel as if we are
punishing candidates for maybe not having enough coffee that morning rather than
assessing their readiness for a job. I share it here only because I do believe the test
case portion of the interview describes tester mindset very well.
