Getting the Vibe on Specification Review
========================================

Some colleagues of mine and I engaged in an exercise which unexpedtedly resulted in
realization of a testing methodology none of us had ever come across before. It involved
AI, vibe coding tools, too many people trying to build something all at once, and
product requirements document that happened to be JUST THE RIGHT level of ambiguous.

Twelve Testers Try to Build an App
==================================
This all started when one of us (Alexander) expressed an interest in learning more about building
apps, but didn't really want to go deep on coding. So of course he proposed vibe
coding as the way to go. And of course he got an earfull from all of us about
how horrifically bad vibe coding is, how it is an irresponsible practice that results
in dreadful code that looks like it works but falls over when you breathe on it,
etc. etc.

You know. Stuff testers say.

At the same time, we couldn't resist digging in. It would give our our aspiring
app-building friend some experience with GIT, development workflows, examining code,
working with requirements, and all the other things that come with application building.
We also relished the chance to have a running application we could exercise some
testing muscle on, as well as reflect how much the AI-driven coding has an impact
on what to test for.

So, You Say You Already Have a Requirements Document?
===================================
He had started the experiment with a trivial example program taken from testing
classes. It is an overly simple application that will tell the user what kind
of a triangle results from inputting the length of three sides. We had fun for a little
bit with that, but got bored very quickly.

One of us, James, said he had a real requirements document he wrote years ago
when a tool company offered to build something for him. The requirements were written
with an actual purpose in mind, and was intended to be used in the development of
an actual project. It was a simple tool, but complex enough to be interesting.

We were using Cursor. First problem with a kind of cool, "Okay, we admit it. This stuff is more useful than we thought"
happens now.

How do you get Cursor to read a .docx file?
-------------------------------------------
This was about five minutes wondering if it knew how to read it natively, if we should try
PDF instead, maybe we should copy and paste it into an editor...

And then one of us (Ben), proceeds to type this into Cursor:

```
Write a tool to convert .docx files to .markdown format
```

A few seconds later, Cursor creates the tool, we run it.. and voila, we have a .markdown version of
the requirements document. Markdown format being very simple, very primitive, Cursor
can read it no problem.

