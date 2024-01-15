 Parenting college students as an analogy of tester mindset and product quality
==================================
![Cartoon image of a student rushing to their laptop while parents look on in frustration](/assets/parentingcollegefreshman.jpg)
_The prompt on this image from Bing Image Creator was "stick figure drawing of freshman student in college frantically trying to catch up with homework while their parents watch calmly but knowingly."
I believe it captured the frantic nature of a self-made student homework crisis that
I wanted it to convey._

Part of a tester mindset is knowing when to sense a
particular area has problems, is at higher risk. We spend a lot of time
thinking of this as a technical problem. We also need to
look at risk as a social and psychological problem.

Sometimes when we test a product we find defects 
we believe might be simple, one-off problems. Other times,
we get the suspicion that the problem is not isolated, that it
represents a pattern. We make note we might need to take another
look. A good deal of telling the difference is about getting
a read on the people you are working with.

This week I was remind that this experience is a lot
like parenting a college freshman.

Bumps in the road during freshman year
==================================
End of first semester of freshman year, we learn that my youngest
kid needs to take three of his classes over again. This is even more frustrating
because he has been living at home the whole time, we had been
asking him about how it was going the whole time, to which he said
"It is fine." Turns out things were not fine.

I asked my son what he was going to do this quarter to improve
the situation. He said he was going to put in more effort.
I asked him what that meant, and he was unable to describe
specific action he was going to take.

>__Hypothesis: Dismissive and incomplete responses to questions about how things are going are a sign of problems.__
>
>__Hypothesis: Without being able to state specific changes he is going to repeat whatever behavior, or omission of behavior, led to the problems last semester.__
>
>__Hypothesis: Because there was more than one class where he had a problem, whatever caused my son to do so poorly in three classes is probably systemic, and probably hasn't really changed.__

Part of this reminds me of my rule of ants.

There is no such thing as two ants.
---------------------------------
<img src="/assets/nosuchthingastwoants.jpg" width="300px"/>
No matter where I am, if I happen to see a single ant walking
about, I am willing to consider the possibility that it
is a single, one-off ant that somehow found its way to wherever.
Particularly if it is somewhere there should not be ants, like
the kitchen.

If I see two ants, I immediatley conclude there are at least
a thousand. No longer a single, unusual, one-off circumstance
which introduced the ants, we are looking at something systemic
that is introducing ants to the environment.
> 1 ant - might be 1 ant
> 
> 2 ants - is at least 1000 ants

Back to college kid stories...
====================================
Today, Sunday, just after noon, my son announces he has a
club meeting at school. My wife reminds him he is supposed to
get homework done before doing anything social, and asks him if
he has any homework.

Physics homework agitations
------------------------------------
>__Evidence: My son gets visibly agitated, starts pacing, he starts having trouble completing sentences. This is related to his autism, and it gets more pronounced when he is stressed.__

He rambles on with something about physics homework that he cannot do because
he doesn't have some kind of access key that he was supposed to get
from the book he emailed my wife about (that she hadn't been able
to find online, to which he had not responded earlier when asked about
it). She tries to get him to clarify what he needs the access key for,
where he is supposed to get it, to which he says he isn't sure how he
is supposed to get it, exactly what he needs it for, etc. His agitation rises.

>__Evidence: He waited until within hours of an event he wants to go to before dropping on us he needs something he doesn't have, isn't sure what it is, to complete something he was supposed to complete before going to this thing.__

I wait several minutes before he is calm, and ask him if he knows what
he needs and why. I have him show me the website that needs the
access key. I ask him if he has any instructions or notes from class
that tell him how he was supposed to get the key. He says he does not know.
I tell him to go to his school portal where the class notes and syllabus
are to see if it is there (all the schools have all of that online now).
He does, and in the first day of notes is a set of instructions for
getting the access key, of which the options are get it from purchasing
the text book, paying with a credit card online, or trying the
free trial account (14 days, but it is a start).

>__Evidence: Everything he needed to know was online, but he didn't go there to find it.__

At this point, let's recall the context. We are on week two of the
second semester, where in the first semester we found out at the
end there were three classes he did not pass, even though the whole
semester he had been telling us everything was fine.

>__Hypothesis: This physics class homework problem, not having what he needs to even execute the homework because he isn't using the resources he has to solve that problem is likely systemic behavior, and will likely repeat.__

I ask my son, is there any other homework you need to do? 
He gets very quiet, starts his unable to talk in full sentences thing again.

Music Theory perturbations
--------------------------------------
He pulls out a couple of sheets of paper with sheet music and
questions on it. He stares at the paper, pacing back and forth in the room.
He says "I'm going to have to look at this when I can think clearly."

I look at it. I believe I understand what it is asking him to do,
but I don't want to tell him something different than what he
was taught for the assignment. There are several chords written on treble
and bass staff (in the key of b minor, if I am correct) and the
instructions say "Write roman numerals where indicated, including inversions."

I ask him "Do you have the learning material that these questions are for? Is it
in a text book, is it online, do you have notes?"

He answers "I don't know."

>__Hypothesis: The same problem is happening here as happened in the physics class. The learning material will either be online, or instructions on what manual he needs to purchase/acquire will be online.__

I ask him to check online to see if his music class
says something about it. Sure enough, in a page in the online notebook
marked "Class Materials" is the name of a text book he needs to acquire.

Our plan of action, so far:
- he is going to sign up for the free trial for now on the physics homework
- he will access the physics text book online (classmate let him know it was online)
- he is going to go to the school today and see if the text books are available there

All of this reminds me how I think about software quality, risk, and where to focus testing energy.

Thinking of software quality like a college student... or ants...
==========================================
<img src="/assets/contemplatingant.jpg" width="200px">
I ask myself similar questions when I encounter problems with software
as I do ants, or as a parent of a college freshman. Is this a one-time thing,
that has its own singular cause that is over and done with, or is this
part of a systemic problem? Are we going to see more of this problem,
and should I start digging further to find out?

Here are some rules of thumb I use to suggest something might
be a systemic problem that is going to keep happening:

> There is no such thing as only two ants.

The moment I see more than one of anything, I get suspicious. A one time error
in calculation, or a single UI glitch, or one security bug may have just been
a mistake which, problem now learned, might not come back. Something that happens
twice suggests there is a reason for the repetition. There might be a bad habit.
There might be a design pattern or work pattern which leads to the mistake. The
design itself might be wrong. The bug might be difficult to avoid, the problem
very difficult to get right. The tools people use might be hard to use, might
have bugs in them. The method might be difficult to survey, assess, or evaluate.

> Lock on target when you confirm the hypothesis.

If you believe something might be a problem, you check, and you don't
confirm your suspicions, maybe keep the concern in mind and check again
later. You can probably relax and worry about something else.

If you check something like _"There were two bugs on the New Ticket rejection workflow,
so I bet there are more..."_, and indeed find more, it is probably time
to commit to a deeper investigation. Worst case you were wrong and you
spent time learning about the feature more. Best case, you save the
team from nasty surprises later.

> Agitation and stress are a sign of problem.

When people seem at all stressed, tired, agitated regarding a specific piece of work, either
talking about it, or responding to questions, or meeting deadlines, that raises
a concern about risk and problems in that work. The level of stress might be a direct
reflection of how difficult it is to do the work correctly. It might be a result
of working longer, harder to get the work done. The stress, agitation, exhaustion
may also directly affect the work quality. Watch the behavior and mood of the
person delivering the product and use it as a clue whether or not you should
take a closer look.

> Dismissive behavior and incomplete information is a warning sign.

When a college student answers every question about how their studies are
going with just "it is fine" but is unable or unwilling to provide
specific answers about what work they have done, are doing, and yet to
do, that is usually a sign that something is not going right. The same
goes with software products. When a person does not and cannot give
fully accountable descriptions of their work, that is a good clue to
dig in deeper on that work and see if there is a problem.

> Prior behavior is likely to repeat unless we see a change.

People do not change habits easily. They can, but it usually
takes intentional, and targted effort. The college student who had a bad prior semester is likely
to have trouble the next semester. This is especially true if they
do not demonstrate through behavior or personal account what
they changed to lead to success. Failure is a learning
experience, but the key to learning is about change we make
in what we do or what we understand about the situation. The same
goes for work and software quality. When a prior project
had lots of problems with quality, be suspicious if the
same people cannot list what specific changes they have made to avoid
similar problems now.

On with the semester... and parenting... and software projects
========================================
I am writing this article in the moment, which is an interesting
time to reflect on material like this. Usually someone waits until the end
so they can share the big success story that came of whatever action they
took - or they explain how they forgot the one special thing and
failed because of it. Depends on what you are selling...

In this case, I don't know how the semester is going to turn out.
Is my son going to get things together, figure out what he needs
to do and succeed on his classes this time around, or will he continue
to struggle with the same problems? All we can do is try and see.

It is the same way with work. We never know what is going to happen,
ever. If we bother to watch and check, we can know what did happen,
and we can make a plan to try to make things better, but nothing
is guaranteed.

What I do know is that there are several important points to watch
with the project and the people around me to guide me toward when
to take a closer look, how to sense potential problems. Watch
things that repeat, watch for dismissive incomplete answers,
watch for stress and agitation, and look for specific reasons
why prior behaviors are not going to be a problem again. Anything
short on that problem deserves a second look.
