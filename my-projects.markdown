---
layout: page
title: "Projects and Resources"
permalink: /Projects
---

Training Resources
====================

## Test Plans Made Easy Training
The following files are a collection of presentations and classroom training material derived from my book, "Writing Test Plans Made Easy."

<a href="/assets/writing-test-plans-made-easy/DataMaker%20Requirements%20Document.docx">**DataMaker Requirements Doc.docx**</a> - A requirements document describing an imaginary application. Used in workshop exercises.

<a href="/assets/writing-test-plans-made-easy/Modeling%20instead%20of%20test%20cases.pptx">**Modeling instead of test cases.pptx**</a> - An eight minute lightning round presentation that demonstrates how models allow representation of large numbers of test ideas for review in very little visual space.

<a href="/assets/writing-test-plans-made-easy/Writing%20Test%20Plans%20Made%20Easy%20Workshop.pptx">**Writing Test Plans Made Easy Workshop.pptx**</a> - A workshop on how to begin a test plan. Focuses on starting the outline, inserting requirements, adding test ideas and beginning on details.

<a href="/assets/writing-test-plans-made-easy/Writing%20Test%20Plans%20Made%20Easy%20Class%20Exercises%20as%20Performed%20by%20ChatGpt.docx">**Writing Test Plans Made Easy Class Exercises as Performed by ChatGpt.docx**</a> - An experiment where I prompted ChatGpt through the write test plans made easy workshop.

Coding Projects and Tools
====================

## DupIQ

I built DupIQ as an idea for a startup, but changed plans and decided to release
it open source. During my long tenure working on massive scale test automation
systems in Microsoft Office, I had a chance to observe first hand that the
ability to determine automatically, at scale, in real-time whether a test
failure was a new issue or something we had seen already was game changing.

I have talked about this capability in some of my articles and in some of
my conference presentations, but it is a bit of a let-down when I realized
almost nobody else has in place the tools to do the same thing. I came up with
a way of implementing the same thing on relatively easy to use technology. This
became the first capability of DupIQ, a way of identifying duplicate test
automation failures in real time.

<a href="https://github.com/WayneMRoseberry/DupIQ">The DupIQ Github repository is located here.</a>
It is a work in progress. I had a version running on Azure, but have trimmed it
back (removed the WEB UI, user sign-in, security, etc.) to a SQL server backed
storage which runs on premise rather than in-cloud. It is extensible, so other
implementations on other platforms are possible.

## DataMaker

This is an application I made to play with concepts. It creates random
examples of data based on a schema describing the data. It is not very good, has
problems, very simple.

I use it to explore testing tools. I toss the tool at this app to see
how the tool does.

<a href="https://datamakerjs-f3b6b7d13de0.herokuapp.com/">DataMaker</a>

## Statemaker

Statemaker is a C#/.NET library that automatically generates finite state machines from an initial state and transformation rules. StateMaker systematically explores the state space, discovering all reachable states and transitions, making it ideal for test coverage generation, workflow modeling, and system analysis.<br><br>
It is located on Github at <a href="https://github.com/WayneMRoseberry/statemaker">https://github.com/WayneMRoseberry/statemaker</a>.

## Amiiboviewer

I created Amiibowviewer as an exercise in testing APIs and data. The entire application client-based HTML which calls a REST API that resides at https://www.amiiboapi.com/api/. The REST API is not owned by me, so I guess I run the risk that someday the owner takes it down. The purpose of the application is to give people a chance to observe behavior and try to figure out how something works. Upon doing that, they point is to see how well they can come up with interesting testing ideas given what they observe. It is an application that appears very simple, but the underlying behaviors and their relationship to the data returned by the API make it useful training material.<br><br>

It is located on Github at <a href="https://github.com/WayneMRoseberry/amiiboviewer">https://github.com/WayneMRoseberry/amiiboviewer</a>.

## Forklift

Forklift is a video game I started and never completed. It came from lunch conversations at work. One of my coworkers shared funny stories from his days at Walmart, driving a forklift. I have a fascination with video games based on mundane activities, so I decided a forklift simulator might be a fun game. The game is built on the Unity 3d engine. I built a working 3d forklift with the physics to move around, as well as lift and carry objects on pallets. I got bored when I had to figure out how to create the score display and all the in-between scenes.<br><br>

It is located on Github at <a href="https://github.com/WayneMRoseberry/Forklift">https://github.com/WayneMRoseberry/Forklift</a>.