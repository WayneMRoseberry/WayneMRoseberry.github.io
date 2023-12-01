Microsoft solved this problem in Office over a decade ago, and so can you
=================================
Office has duplicate failure detection in their automation
runs solved. Not 100% - the problem is a fuzzy one - but
solved to a degree that made working with the automation
suite unlike anything I have seen anybody else talk
about anywhere. When Office solved that problem, everything
changed.

I am going to talk about how, and describe some ways other
people can, with some work, do the same thing.

Duplicate bugs impede going big with automation
=================================
The question is simple.

An automated suite produces a failing result.

What do you do now?

The answer is not simple. Should you send the failure 
immediately to the developer that owns the code under test? 
Should you send the failure to some individual that examines
every failure and decides what to do with it? What if your
automated tests are designed to hit bugs in complex
end-to-end functionality where failures appear with less
certainty than the test can control? Do you keep those
tests, or toss them because cannot afford the investigation?
How many automated checks, each emitting results and
possible failures, can you afford to run? At what rate
will the information overwhelm your capacity to look
at the failures.

There is a key question sitting  at the heart of this problem
which comes up in every single failure investigation.

_Is this a new problem?_

There are other questions that make the problem difficult,
but establishing new problem versus existing problem is
where almost every analysis starts, and how easily and
effectively you can answer that question
