The tester mindset and the FormatFont dialog
===========================
This article is part of a series where I explore the tester mindset
via interview questions I found effective for
finding people who demonstrated application of
a tester mindset.

I have found different questions tend to demonstrate
different kinds of testing behaviors. This question
involves recognizing tricky problems inside
deceptively simple feature sets, examining a very
user oriented UI, going beyond rote enumeration of
an applications surface behaviors, and having an
approach to cut down a rapidly unmanageable problem domain.

All that from FormatFont? Yup.

Bring up the app, give them the chair, and ask the question
===========================
If I can, I really want to see the tester working directly
with the product during this question. I want to watch
how they interact, explore, what steps they take to discover
behaviors, elminate variables, introduce new ones, how they
build a model of expectations. My script goes as follows:

> __Setting__: _My computer has every application but Microsoft Word
> closed. Word is open with the Format Font dialog displayed._
>
> _Step out of chair and pull it back._
> 
> "Here, take my chair, sit at my desk."
>
> _Wait for them to take a seat. Step aside to where I can observe
> and be sure to give them lots of room to feel comfortable._
>
> "On the screen is Microsoft Word with the Format Font dialog
> open. Are you familiar with this feature and what it does?"
>
> _If they say yes, I proceed. If they say no, I give a brief
> explanation of what the dialog is for._
>
> "Let's pretend you are a tester assigned to test the Format Font
> dialog in Word. How would you go about testing it?"

At this point I let them do whatever they intend to do. I answer questions
if they offer them, but only clarifying questions. If they ask me something
such as "What would a good test be?" or "Is this a good testing idea?" I
always throw it back at them with something like, "That is what I am asking you."

Green Flags
===========================

Red Flags
===========================
There are some red flags that worry me the candidate may not be
suited for testing. Something about these red flags is they tend
to appear exclusive of green flags. It is not common that a candidate
will offer one of these ideas and also demonstrate a number of the
behaviors that indicate strong testing potential. If that were to
happen, it is my inclination to emphasize the green flags over the
red.

The candidate ever says "That's all."
---------------------------
There is something about strong testers that keeps them from running out
of ideas, and even when they do run out of ideas they tend to get
very bothered about it. The very notion that one might run out of
testing to do seems to agitate and irritate an effective tester. Even
when they have never been exposed to testing concepts, there is a feeling
in their gut that something more remains.

By contrast, some people shut off idea generation quickly and confidently.
They will assert, even after multiple prompts (personal bias, if I have to
prompt you even once with "Are you sure that's all?" you have a very deep
hole to dig yourself out of) that there is no more. This might shock some
that I find a lot of developers fit this bill nicely. I have seen
plenty of individuals do a whiz bang job at writing up all sorts of
complicated algorithms and rip open all sorts of puzzles, only to respond
with a near empty list of possible ways to challenge the behavior of
whatever code they are handed.

Rote feature enumeration
---------------------------
The behavior goes like this

> _Candidate looks at screen and starts reading from top left of the dialog, moving right and down._
>
> "I would test the font name. I would do each of them, Abadi, Abadi Extra Light, Abel..., then the font style, so Regular, Italic, Bold, Bold Italic. Then the font size..."

And from there they keep going. One control at a time, naming every option.
I try to catch this behavior as quick as I can. Enumeration and recognition of the features
is important, so I don't want to shut them down too early. I also want to give
them the chance to decide on their own that they are using up valuable time and
probably ought to figure out ways to shortcut what they are saying.

But most of all, I want them to see past the stuff on the surface. After I give them a
chance to correct themselves, if they do not, I start dropping hints as questions, such as:

> - Of the items in that font size list, which would you choose and why?
> - Do you think covering every font name in that list is important?
> Is there anything else you would try, seeing that feature beyond just covering every name in the list?

I find that rote enumeration predicts very poorly for getting back to green flag
behaviors. Once I see the behavior, I nudge as much as I can because continued demonstration
of the same rote enumeration gives me no more information about the candidate skills. I respect
their time to much to let them lose a chance to demonstrate something positive.

Never using the product
---------------------------
I put them in my chair with the application loaded and ready for a reason.
I don't just want to hear them talk. I want to watch them work.
When they encounter the font size control, do they ever click on it? Do they
scroll the list? There is an edit control at the top of that list. Do
they ever type anything inside of it? If they do, have they considered typing a
value that is not in the list? Have they considered typing a value that seems odd
or ridiculous, like "pi" or "-23" or "1.0000000000000000000000009"?

Testing is about discovering truth through observation. It is an empirical
craft, and observation demands activity. You have to create a condition, observe
it, think about it, and describe it. Your ideas and hypotheses are going to
constantly fall apart and you need to adapt. If a person, when presented with an
application to test, never once even interacts with it, that is a major red flag
to me.

