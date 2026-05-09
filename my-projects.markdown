---
layout: page
title: "My Projects"
permalink: /Projects
---

DupIQ
====================
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

DataMaker
====================
This is an application I made to play with concepts. It creates random
examples of data based on a schema describing the data. It is not very good, has
problems, very simple.

I use it to explore testing tools. I toss the tool at this app to see
how the tool does.

<a href="https://datamakerjs-f3b6b7d13de0.herokuapp.com/">DataMaker</a>

Training Resources
====================

## Test Plans Made Easy Training
The following files are a collection of presentations and classroom training material derived from my book, "Writing Test Plans Made Easy."

<a href="/assets/writing-test-plans-made-easy/DataMaker%20Requirements%20Document.docx">**DataMaker Requirements Doc.docx**</a> - A requirements document describing an imaginary application. Used in workshop exercises.

<a href="/assets/writing-test-plans-made-easy/Modeling%20instead%20of%20test%20cases.pptx">**Modeling instead of test cases.pptx**</a> - An eight minute lightning round presentation that demonstrates how models allow representation of large numbers of test ideas for review in very little visual space.

<a href="/assets/writing-test-plans-made-easy/Writing%20Test%20Plans%20Made%20Easy%20Workshop.pptx">**Writing Test Plans Made Easy Workshop.pptx**</a> - A workshop on how to begin a test plan. Focuses on starting the outline, inserting requirements, adding test ideas and beginning on details.

<a href="/assets/writing-test-plans-made-easy/Writing%20Test%20Plans%20Made%20Easy%20Class%20Exercises%20as%20Performed%20by%20ChatGpt.docx">**Writing Test Plans Made Easy Class Exercises as Performed by ChatGpt.docx**</a> - An experiment where I prompted ChatGpt through the write test plans made easy workshop.

