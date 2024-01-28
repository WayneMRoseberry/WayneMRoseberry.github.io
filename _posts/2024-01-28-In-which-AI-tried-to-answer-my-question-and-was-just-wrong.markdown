Asking help questions and why sometimes I feel like AI doesn't really change the problem that much
=====================================================
![A cartoon of a woman at a chalboard full of equations and she is think that asking AI doesn't change the problem that much](/assets/sometimesAIdoesntreallychangetheproblemthatmuch.jpg)
_The prompt to Bing Image Creator for this image was "Asking help questions and why sometimes I feel like AI doesn't really change the problem that much."
My first attempt at the prompt resulted in four images of someone sitting next to C-3PO on a couch, a rather concerning
violation of copyright of a property owned by Disney, and I was in no mood
to setup myself up for battle with the notoriously vicious lawyers
in service of The Mouse._

This article is an account of an amusing buggy experience
using an AI assisted Q&A feature with Microsoft. This is not
so much LLM/AI bashing as it is just a bug, and to some degree
the eventual bug that made a reference to an article break is
the same sort of problem we might see with non-AI software.

This started me wondering about how to approach testing a
system like this.

It all started with me trying to write a Node.js application in Visual Studio 2022
======================================================
I am re-writing one of my tools in Node.js as a way of improving
my JavaScript skills. One thing I have never tried before is
unit testing with JavaScript. I really like the Visual Studio
IDE, and really want to run the tests in the Test Explorer
window. I had a difficult time finding current articles
that said what to do, and the only ones I did find gave me
instructions that failed when I tried them.

I went to Microsoft's Q&A site, and posed a question there. During
edit of the question, there are some options to use AI to
help find similar questions, re-write the answer, or even
answer the question.

I posed this question to Microsoft's Q&A forum:

> __How to do unit testing on node.js web application in visual studio?__
>
> I created a project in Visual Studio 2022. I created a node.js web application using the "Blank node.js web application" template.I want to create a unit test. What is the recommended way to do that? I hit problems following an article that advised installing Chutzpah as the test runner framework and Jasmine as the test library. Specifically, when trying to install the Jasmine packge, Nuget Package Manager gave me the following error:
>
> ```
> PM> Install-Package Jasmine
> Install-Package : Project 'Default' is not found.
> ```
>
> Looking in Package Manager console, the "Default project:" dropdown is empty, which as I understand is intentional with Node.js solution projects.How is a person supposed to setup unit testing in Visual Studio when working with Node.js? Do they have leave the IDE entirely? Is there a way to make it work?

Below the form, and before the "Post your question" button is the text "__Ask with Q&A Assist__ AI can help you find a solution and write your question. Get started by selecting an option below." are the options "Find similar questions" and "Answer my question". 

![options for "Find similar questions" and "Answer my question"](/assets/msqa_aiassist.png)

I decided to try "Answer my question" and I got the following:

> __Here is a possible answer to your question__
>
> To write unit tests for a Node.js project in Visual Studio, you need to install the NuGet package Microsoft.JavaScript.UnitTest and set the property. Additionally, some test frameworks might require additional npm packages for test detection. For example, Jest requires the jest-editor-support npm package. If necessary, check the documentation for the specific framework.
>
> Before adding unit tests to your project, make sure the framework you plan to use is installed locally in your project. This is easy to do using the npm package installation window. The preferred way to add unit tests to your project is by creating a tests folder in your project, and setting that as the test root in project properties. You also need to select the test framework you want to use. You can add simple blank tests to your project, using the Add New Item dialog box. Both JavaScript and TypeScript are supported in the same project.
>
> After opening Test Explorer (choose Test > Windows > Test Explorer), Visual Studio discovers and displays tests. If tests are not showing initially, then rebuild the project to refresh the list. You can run the tests by clicking the Run All link in Test Explorer. Or, you can run tests by selecting one or more tests or groups, right-clicking, and selecting Run from the shortcut menu. Tests run in the background, and Test Explorer automatically updates and shows the results. Furthermore, you can also debug selected tests by right-clicking and selecting Debug.
>
> To run tests from the command line, right-click the project in Solution Explorer, choose Open in Terminal, and run the command specific to the test type.
>
> References:
>
> <a href="https://learn.microsoft.com/en-us/visualstudio/javascript/unit-testing-javascript-with-visual-studio?view=vs-2022">Unit testing JavaScript and TypeScript in Visual Studio</a>
> 
> <a href="https://learn.microsoft.com/en-us/visualstudio/javascript/unit-testing-javascript-with-visual-studio?view=vs-2022#write-unit-tests-in-a-nodejs-project-njsproj">Write unit tests in a Node.js project (.njsproj)</a>
> 
> _AI-generated content may be incorrect_

The above content is indeed incorrect.

The first problem with the content is that in my question, 
I state that NuGet package installation is not working, 
yet the answer instructs me to install a NuGet package. 
I tried the step anyway, and indeed it failed with the same error I reported earlier.

I chalk that problem up to typical issues of the LLM 
inability to capture all the relevant points, consider its
answer, and realize that one of the things it is telling me
to do is not going to work.

_ASIDE: ever had that problem with a human? I have, so I am not sure if I am worse off or not with the AI._

The second failure is more interesting to me because of the mechanics
of it. The AI offers two links, one for general unit testing in JavaScript and
TypeScript, which doesn't work for my problem because the part of the
article referring to JavaScript is for ASP.NET projects, which are entirely
different from how Visual Studio creates Node.js projects.

The second like is supposedly explicitly about Node.js projects. When I click it,
I wind up on the same page as the first link. At first, I thought the
AI was hallunicating and making up links. But when I copied the link to write
this article, I found something interesting:

```
First link : https://learn.microsoft.com/en-us/visualstudio/javascript/unit-testing-javascript-with-visual-studio?view=vs-2022
Second link: https://learn.microsoft.com/en-us/visualstudio/javascript/unit-testing-javascript-with-visual-studio?view=vs-2022#write-unit-tests-in-a-nodejs-project-njsproj
```

It is to the same article, but with a bookmark to `#write-unit-tests-in-a-nodejs-project-njsproj`.
I viewed the source, and that bookmark is there:

```
<div data-moniker="vs-2019">
<h2 id="write-unit-tests-in-a-nodejs-project-njsproj">Write unit tests in a Node.js project (.njsproj)</h2>
```

But when I view the page, none of the text in that section is there.
But then I compare the `data-moniker="vs-2019"` attribute on the 
containing `div` with the `view=vs-2022` argument on the query string.
The section the AI is referring to is for Visual Studio 2019, and the
URL that the AI constructed is for version 2022. I change the URL to the following
`https://learn.microsoft.com/en-us/visualstudio/javascript/unit-testing-javascript-with-visual-studio?view=vs-2019#write-unit-tests-in-a-nodejs-project-njsproj` and voila, the text for setting up Node.js unit tests displays.

Is this mismatched article version behavior even an AI problem?
============================================
There is no way to know without understanding how the feature was written.

There are numerous ways that an AI can generate a help response like the
above, and some amount of the sophistication of taking my written description of
a problem and trying to create a relevant response is likely using generative
AI and fuzzy matching (I suspect vector database and word embeddings are involved
here as well). The part where it seemed to ignore my problems with Nuget package
installs feels very AI to me.

But the version mismatch on the article, even if we got here down a
fuzzy generative-AI path, the problem I come up with - an article link that
isn't working for my "edge condition" - that problem itself is feels
very much same as what we have always dealt with. Whether it is an explicitly coded
algorithm that gets it wrong, or it is a generative AI unable to realize that
two things do not go together, the end user result is they were given a link that
does not have the content they expected. We get that kind of thing without AI.

If we think of the bug, I believe the condition is something like this:
1. content has multiple version material in it (current, current-1, current-2, etc.), but filtered based on version, which defaults to current
2. customer asks question about version N such that ONE answer to the question is for N-1 but not N
3. system responds with pointer to content
EXPECTATION: the response does not include a pointer to the N-1 material

This test idea seems obvious in hindsight, now that I hit the bug. It might
be difficult to imagine as either the developer or a tester working on it,
but it doesn't seem unreasonable to imagine someone creating this test idea either.
Whether the system answering questions is AI or not doesn't really change the
nature of the use case. We might take a different approach to measuring our expectations
of appropriate correct behavior (AI "correctness" is always size of error over sample population),
but it is still something we could craft a test for.

Testing it in production?
------------------------------------------------------
The answer has a thumbs up, thumbs down indicator. That could be a
measure of how well it is working, although you lose a lot on people
who do not bother to rate the answer.

I am wondering about this anchor/bookmark, version of article mismatch.
Is that even detectable? To detect, you have to anticipate the mismatch is
even something that happens, but even then, it suggests you have something
which can easily know that a given bookmark is inside a section for a version
that does not match the filter on the page query string. You COULD build that...
and if you thought that really important, you might.

Otherwise, this feels hard to catch, even from production. Production signals
are enormously and high volume and spread very wide. Problems like this tend
to get very granular. If we try to track this kind of thing, it starts
to feel like tracking every mosquito around the equator. Just one can give you
malaria, so the consequences of prolbem may be high, but an individual mosquito
tracking strategy is not going to work.

The musing continues
=================================================
Sometimes the interesting part of a bug, or class of bugs, is pondering
how to deal with them. How do you find them? How do you prevent them? Are
they worth finding?

For me, this one added to my growing cynicism about the quality of
Visual Studio, especially around scenarios outside the Microsoft
ASP.NET and TypeScript wheelhouse. I am a pretty have Visual Studio
fan, but things like this nudge me closer and closer to using a different
editor and set of tools when working on JavaScript and various web
technologies.

Where does that show on a dashboard? Hmm?
