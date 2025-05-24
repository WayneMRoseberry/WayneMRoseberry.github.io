Getting the Vibe on Specification Review
========================================
![Two people looking at a toilet built by a robot missing a handle and drainage connect because the requirements didn't mention them.](/assets/requirementsvibration.jpg)


Some colleagues of mine and I engaged in an exercise which unexpectedly resulted in
realization of a testing methodology none of us had come across before. It involved
AI, vibe coding tools, too many people trying to build something all at once, and a
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

In Which Vibe Coding Gets the App Wrong and It Turns Out to be a Good Thing
=============================================
We fed the requirements document to Cursor, and in short order it had the application up.
Visually it was pretty clean and easy to understand. Rapidly, though, we had problems. A lot
of these noticed by James, who had written the specification.

"It doesn't let you associate sessions with existing charters. Instead, the charter field
forces you to type in a completely new charter. This is pointless if it doesn't point to existing
charters."

Then people noticed another problem after refreshing the page.

"It isn't saving any of our changes. This thing is supposed to save the changes!"

This digressed into a conversation about the iterative nature of Vibe coding, how
it is a conversation, etc. etc.

But I grew curious. I went back to the requirements document.

"Hey, James, the requirements document didn't say anywhere in the edit sessions section
about selecting existing charters. It only says you can edit the sessions, and edit the charters.

And when I look in the item repository specifications, it says the items go in a repository, but
it doesn't say that the repository contents are persisted. These are missing from the
requirements document. The AI doesn't magically know our intent."

A testing methodology is born... although I hesitate to name it after "vibe"
-------------------------------------------------
James is always looking for new ways to test things. We were ready to roll on with
just updating the requirements document, or prompting Cursor to add the missing features,
but James stops us.

"So, this is pretty cool. The fact the AI couldn't build the application is an indication there
is something wrong with the requirements. This could be a new way to review requirements. Feed
them to the AI, have it build the app, and then we as a group could talk about it, find what
seems off, and then go back to improve the requirements."

We had a talk about this. It was a new idea for all of us. Some of us reflected how the
conventional way of getting this kind of ambiguity sorted out sometimes meant waiting a half to
whole sprint to wait for a developer or designer build a mock-up of the requirements, only to find out after
very expensive work there was something the requirements didn't say explicitly enough.

Alright - Let's Name it Requirements Vibration
===============================================
Somehow "vibration" feels better to me than "vibe." It implies you are doing something almost
agitating to the specification. Brings to mind building earthquake testing. It has a kind of
stress test feel to it. Here is the method, in a nutshell:

1. Prepare a version of the requirements that is machine readable (e.g. markdown)
2. Give the requirements to a AI driven coding tool - preferrably one that will build a fully functioning application - and direct it to build the app described
3. Test the resulting application, looking for deviations from what you want the application to be - this could be missing features, extra unwanted behaviors, flow that is not correct. Make note of anything different.
4. Review the requirements document and see if anything in the requirements might have suggested the problems discovered, or did not describe sufficiently to guide the AI in the desired direction
5. Change the requirements document and repeat the process - so long as the number of issues is decreasing the document is likely converging toward a less ambiguous and more useful version of the requirements

So, we can vibe it all the way home, right?
-----------------------------------------------
The methodology described here is not meant to cover how to build the completed
product. This author's personal preference is for vibe coding to stay in the
domain of prototyping and tiny one-off tool writing. That topic isn't the focus of
this article, and the methodology described here is completely independent from
how a person or team intends to finish building what is described in their
requirements document.

"Requirements Vibration" (the term is growing on me) is meant to describe a technique which might help discover ambiguities and
confusing material inside a requirements document. It will miss things, it will
probably not always work well, it will probably not work with certain
types of requirements. But it is a relatively inexpensive (and fun, if I must
be honest) means to find problems in requirements material.

