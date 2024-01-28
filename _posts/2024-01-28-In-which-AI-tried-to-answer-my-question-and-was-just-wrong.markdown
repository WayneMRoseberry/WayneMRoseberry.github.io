Asking help questions and why sometimes I feel like AI doesn't really change the problem that much

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
> Looking in Package Manager console, the "Default project:" dropdown is empty, which as I understand is intentional with Node.js solution projects.How is a person supposed to setup unit testing in Visual Studio when working with Node.js? Do they have leave the IDE entirely? Is there a way to make it work?NOTE: I tried the AI generated "Answer my question", and it gave me two links to irrelevant articles (neither about Node.js, but instead about TypeScript and ASP.NET Core - and the second link said it was about Node.JS, but it pointed back to the first article). It also told me to install NuGet packages, which as you can see from my description above isn't working because the Node.js package does not support it.

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
