I prefer testing documentation be about testing and only about testing.
The purposes of testing documentation are as follows:
- Help us think about testing we might do
- Support critique of the testing approach
- Reference for test execution, preparation, and reporting
- Communicate and guide testing logistics

There are some testing documentation anti-patterns that I
always ask testers to remove or change in their plans. I ask
them to replace the content with something else that fits
one of the purposes above.

> __Stories from the front lines__
>
> One time, a developer told me "_I always thought the
> purpose of writing the test plan was to check if the tester
> understood how the product works._"
>
> I hid my feelings, but I was angry. To me that
> was an insulting and patronizing view of the tester's
> capacity to act as a skilled member of the team. Nobody
> else is subjected to special homework and quizzes to determine
> if they know what the product is about. But it also
> demonstrated a gross misunderstanding on the part of the
> developer of what testing is about, what a test analysis
> should describe. Topping that off, it suggests that maybe
> none of the testers the developer had worked with ever
> offered anything of substance to talk about.
>
> So many problems to fix.

I am not a tester but I do a lot of my own testing, should I keep reading?
=========================================================
I think maybe yes. Chances are whatever documentation you write is
brief and short. You might share your ideas with others, or the
burden of review might fall entirely on you, although I would advise
sharing anyway to get the benefit of collaboration and feedback.

Either way, the techniques I talk about here are about replacing bulk
with lower volume, value-dense information. When the content is about
the testing and only about the testing you save time writing and
reviewing it. It serves its function more properly.

I also believe "do this instead..." material helps us understand
better what might work for us. Keep on reading, and imagine how whatever
material you prepare for yourself might be affected by the techniques
below.

General guidelines about test documentation content
=========================================================
- replace general concepts with your product/system specifics
- try to describe only testing activities, need, input, expectation, or assumption
- remove/delete any guiding material and replace it with your specific material
- only write it down if you are going to use the information
- don't worry about elegant writing style - if people understand, it is good enough

What to do instead of rewriting the product spec
=========================================================
Sometimes a tester will repeat content that is already described in
product documentation, designs, specifications or source code.
Small bits of such content are useful when the re-write
is about testing implications, but when no such testing
information is included it is a waste of tester time and
energy. It also gives the impression to teammates that testing
is a vacuous act, that there is nothing to it other
than what everybody else already created.


Here are some techniques to make the content testing
relevant

__Try to leave it out entirely.__

Maybe the content offers nothing at all. Just cut it and
see if your specification is any worse without it.

__Use an "(EXCERPT) _(and therefore)_..." pattern.__

Include a description of product behaviors, but after each
detail of meaningful cluster of details, append "_and therefore..."
and say something about the testing. For example:

> The user personalization cache checks for personalization refresh
> based on a timer schedule, _and therefore_ testing will focus on
> actions that fetch from the cache along with actions that affect
> personalizations around triggering of that time job.

__Replace product behavior descriptions with "_What happens when...?_"__

A lot of testing activity is about explorations that seek answers to
questions, about things that happen under different conditions. Imagine
the product specification said something like this:
> User preferences are captured in the app across all platforms and
> persisted both in cookies (or local settings files) and in the user's
> `PersonalizedSettings` property bag in their account on the service.

In the testing specification, you might write in response to that

> __PersonalizedSettings__
>- _What happens when the user personalized settings are different on different devices?_
>- _What happens when the user clears their browser cache?_
>- _What happens when the user makes personalized settings while disconnected?_
>- _What happens when the same personalized setting is across device and platform types, are there any which do not work going from one platform to the next?_

__Write concerns and risk statements__

Testing activity is largely about exploring risk inherent to
the product, project, dependencies, or any other factor
which might threaten product value. In the test documentation, we
can start the discussion of a testing approach with a statement of
concern that the testing approach is meant to explore.

Imagine the following from the product specifcation:
> __Generative AI Integration with Diagnostic Report Data__
> 
>  The report calculation feature will present meaningul and easy to
> understand content by taking the raw output of diagnostic
> measurements, combine the data with prompts fed to generative AI
> and presenting the output to the user in PDF format.

The testing document may respond with the following concerns as areas for
investigation:

> __Report Generative AI Integration Concerns and Risks__
> 
> This document covers strategies for addressing the following risks and concerns
> - LLM response peformance, will the round trip and processing time be adequate
> - LLM model versioning and response drift, will our prompting strategy produce consistent responses across model changes
> - LLM hallucination and unintentional escapes, in particular when processing unstructed diagnostic data

Written in this fashion, the statement functions as a segue for more
specific testing details.

Do this instead of keeping the boilerplate
=========================================================
Lots of testers start a plan or specification with a template, which comes
with a lot of boilerplate text. Some of the boilerplate is structural, helping
organize the documentation. A lot of the boilerplate is placeholder text, a
"replace this with your content" device. Many templates offer documentation and help
inside the boilerplate by offering instruction or examples.

I almost always tell the tester to delete the boilerplate. The boilerplate
text is always a generic statement that says nothing about testing your
feature specifically.

Imagine the following section from a test specifcation template (taken from
an IEE 829 test specification template on <a href="https://www.stickyminds.com/sites/default/files/article/file/2013/Software%20Test%20Case%20Specification%20Template.pdf">
StickyMinds.com</a>:

> Environmental needs
> 
> - Hardware
>   - Configurations
>   - Limitations
> - Software
>   - System
>     - Operating systems
>     - Compilers
>     - Tools
> - Other Application
>   - Mix of applications
> - Other
>   - Facilities
>   - Training

I offer the following guidelines:
- Immediately delete the boilerplate. Keep a separate copy of the template if you need a reference.
- Only fill in the parts of the boilerplate you are going to use. In the example above, if "Facilities" is not relevant, don't include it.
- Amend the content with things the boilerplate does not mention if that works for you.
- Only write something specifically about the testing when you replace the boilerplate. Consider the following possible testing material.

> __Environmental needs__
> We will address coverage across devices and platforms with
> a combination of lab support and outsourced vendor for larger
> scale coverage needs. Engineers will get basic iOS and Windows
> platform coverage from their desktop.
> 
> - Device and OS, in-house lab for engineer checkout
>   - lab for engineer checkout
>     - iOS phone version current and N-1,2
>     - iPad
>     - Mac OS: versions prior to current
>     - Android: Google Pixel Pro, Samsung Galaxy, OnePlus Nord,
>   - engineer assigned machines
>     - Windows Desktop
>     - Mac OS: current version
> - Other
>   - Outsourced Device labs: Needed to support automation suite across devices, assessing vendors

Get to specifics, only say as much as you need, and make
sure it is relevant. 

Describe how and why a category applies to your product instead of making the apology for categories of quality
=========================================================
Testing almost always involves covering common testing categories
that apply to aspects of quality requirements. One pattern I 
have seen in a lot of testing specifications is to make the
case for why the category is important, at all. Imagine coming
across this in a test specification for a web-based calendar
application:

> Accessibility testing is important to ensure that digital products are usable by people with disabilities, promoting inclusivity and meeting legal requirements. It contributes to a positive user experience, leading to increased satisfaction and retention. By catering to a broader audience, businesses can expand their market reach and improve their brand reputation. Designing for accessibility from the start saves time and resources, preventing the need for costly retrofits. Additionally, accessible websites often enjoy SEO benefits, aligning with organizational values centered around diversity and inclusion.

_I got the above from ChatGpt, and it is not much different than
the same sort of material I used to read in test specifications._

You do not need to defend a category of testing. Instead, you
should describe how and why that category applies to your
product. Consider the following for our same imagined calendar
application:

> _Calendar Web-App Accessibility Testing_
>
> The following features use conventional web controls and
> elements without much change to similar controls on other
> web applications. For each we will execute the pri I
> user scenario suite against a set of usability tools and
> modes to look for problems that arise:
> - appointment view
> - appointment invite
> - appointment notification
> - appointment reading/editing
>
> The following feature uses controls with a lot of custom
> behavior, and high-interactivity, which suggests a higher
> risk basis for accessibility modes. We will construct
> feature specific investigations targeting the interactions and
> gestures to see how well they work across accessibility
> tools and modes:
>  - appointment creation/management view

You know, you don't HAVE to write a document...
===============================================
A lot of testers get a lot of ground from material other than
prose documents. Jumping right to another format than document
files can force the issue of not re-hashing something already said
elsewhere, or that says very little.

I have seen lots of the following:
- __Mindmaps__: I do not use them myself, but a lot of people do very well with them
- __Checklists__: Airline pilots, surgeons, and astronauts use them, and they work well for testing. A whole bunch of test specifications are really descriptions of how to combine one checklist with another.
- __Spreadsheets__: Everything from checklists, to matrices, to input values, to pre-canned report formats, I have known a lot of people who get just about every testing idea they have from a spreadsheet
- __Drawings__: A bunch of circles, squares, lines, and arrows can describe an entire spread sheet. Failure mode analysis and threat modelling use diagrams as part of finding problems in a system.
- __Algorithms and pseudocode__: One for loop might suggest thousands - millions... pick your uppper limit - of possible test conditions, all in less than 1" of vertical viewing space

The key to all these other alternate formats is that there be
testing content in it. Consider the following algorithmic
expression of a testing intent:

```
// customersampledocumentlibary located at http://testlab/archive/samples/customerdocs
// possibleuserroles defined in UserRolesAndPrilegesSpec.docx
// documentCRUDusecases located in the DocLib project in Jira, tag is #UseCases_CRUD

foreach(document in customersampledocumentlibrary)
  foreach(userrole in possibleuserroles)
    foreach(scenario in documentCRUDusecases)
       execute scenario as userrole using document
```

It is simple and easy to understand. Explanation of where to
find details are provided in the sample. Notice that the other
collateral serve their own purposes for driving design, setting
business priorities, or serving as input to other testing
purposes. We use that information without having to repeat it.

In short, it is all to make the testing easier and more effective
================================================
When you remove unnecessary, unhelpful material
from testing documentation and focus on the purpose of
such documents, you wind up with a tool that helps you
do better testing.

Rember the guidelines:
- product/system specifics in place of general concepts
- test material only
- replace help material with your content
- only write it if you will use it
- it doesn't need to be elegant

Remember the purpose:
- think about testing
- aid testing critique
- support reference for testing
- communicate testing logistics

Where to find more guidance on writing test plans and specifications
================================================
A lot of the ideas in this article come from my
book, <a href="https://www.amazon.com/Writing-Test-Plans-Made-Easy/dp/1478333693">"_Writing Test Plans Made Easy_", available on Amazon</a>.

<a href="https://www.amazon.com/Writing-Test-Plans-Made-Easy/dp/1478333693"><img src="/assets/writingtestplanscover.jpg" width="300" /></a>

The book is short, inexpensive, and easy to understand.
I found a lot of testers working for me had trouble
writing test documentation, so I came up with an approach
that gets you to useful, helpful material as quickly and
easily as possible.

