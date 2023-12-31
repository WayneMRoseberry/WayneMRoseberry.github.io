Fuzzy ideas for fuzzy problems
===========================================
![A cartoon of a bunch of fuzzy monsters surrounding a scientist at a whiteboard](/assets/fuzzyideas.jpg)

Asking the LLM for test ideas by narrowing its focus
===========================================
One way to use an LLM to generate test ideas is to
do the following:
1. give it a prompt intoducing a category of test analysis, ask the LLM to respond with a short summary
2. in the next prompt tell it to apply the test analysis category to a passage of text describing a product behavior, responding with test ideas without detailed steps
3. after the prompt, either tell it to continue or choose one of its test ideas and ask it to elaborate with more test ideas narrowed to just that one point

The idea is to narrow the generated response to the
test analysis category selected. The benefit of the
technique is in its simplicity. You can select a
test category description from a number of
sources. You can get the product description from
documentation, specifications, design documents
or even source code.

The output may or may not be useful, but evaluating
it comes at a very low cost. These are high level ideas
that do not demand deep analysis and checking of precision
facts, steps, assumptions to evaluate. Worst case you lose a minute
or two. Best case the LLM generates some test ideas
you like but might not have thought of. Most likely
case the LLM comes up with ideas you would have thought
of on your own, but it does it much faster. As someone who
writes frequently about applying AI to testing, Jason Arbon, 
points out, often times you don't
need better than a person would create. Often it is helpful
to have something good enough that makes the work easier. The
key is that the cost of evaluation, and the penalty
for being wrong, needs to be within reasonable
limits to pay off, an <a href="https://waynemroseberry.github.io/2023/09/23/Generic-Test-Cases-and-the-Great-ESC-Ape.html">
idea I describe in an article
about generic test case generation tools</a>.

Example: Applying claims testing to Microsoft Word marketing literature
===========================================
For test analysis category, I selected "Claims Testing", the
description of which I got from James Bach's publication
of his <a href="https://developsense.com/resource/htsm.pdf">Heuristic Testing Strategy Model</a>.

For product description, I used the "Features in Word 2022" marketing documentation.

Prompt #1: Introduce the testing category
-----------------------------------------
The template part of the prompt gives the main instruction. The goal is to
get this particular type of analysis into the session state. The instruction
to paraphrase is intended to keep ChatGpt's response short so as not to
throw off the remaining generation with too much extra text to process.
>I am going to introduce a type of testing heuristic. Below, after I do, acknowledge your receipt of it with a short, single sentence paraphrase of the technique.
>
>Claims Testing
>Challenge every claim 
>1. Identify reference materials that include claims about the 
>product (tacit or explicit). Consider SLAs, EULAs, 
>advertisements, specifications, help text, manuals, etc.
>2. Analyze individual claims, and clarify vague claims. 
>3. Test each claim about the product. 
>4. If you’re testing from an explicit specification, expect it and the 
>product to be brought into alignment.
>
>ChatGPT
>
>Acknowledged: Claims Testing involves scrutinizing various reference materials for product claims, analyzing and clarifying these claims, and systematically testing each claim to ensure alignment with explicit specifications and the actual product.

Prompt #2: Introduce information about the system under test to analyze
------------------------------------------
This is a long passage of text that I got from a Microsoft website. I
treated this like a fast "grab and go" use case because I am trying to
mimic a situation where gathering the inputs is inexpensive. I am also
mimicking a situation where the input data is fuzzy and ambiguous.

The instructions tell ChatGpt to only list ideas, not full steps. This
is designed to keep the LLM output likewise high level, but also
to get more ideas into a single response. More ideas increases the value
of the session by reducing labor to record the ideas. Higher level means
there is less detailed information to painfully analyze and correct. Rejecting
or keeping an idea is meant to be fast and easy to do.
>__User__
>
>I am now going to present a "Features in Word 2022" list that came from the manufacturers of Word. Given this list, come up with ideas for claims testing, based on this list. You do not need to write the steps, just some basic ideas for testing claims that derive from the feature list:
>
>Co-authoring
>
>You and your colleagues can open and work on the same document. This is called co-authoring. When you co-author, you can see each other's changes quickly — in a matter of seconds.
>
>Visual refresh
>
>Work with a modernized Start experience and newly refreshed tabs in the ribbon. Experience a clean, clear style with monoline iconography, neutral color palette, and softer window corners. These updates communicate action and provide features with simple visuals.
>
>Word, Excel, and PowerPoint displayed with visual updates in ribbon and rounded corners to match Windows 11 user interface.
>
>Save your changes as they happen
>
>Upload your files to OneDrive, OneDrive for Business, or SharePoint Online to make sure all your updates are saved automatically.
>
>Reduce eye strain with Dark Mode
>
>Previously, you could use Word with a dark ribbon and toolbars, but your document color stayed bright white. Now, Dark Mode in Word offers a dark canvas as well.
>Switch modes for Dark mode feature.

>See what’s new in stock media
>
>We’re constantly adding more rich media content to the Office Premium Creative Content collection that helps you express yourself, such as a curated library of stock images, icons, and more.
>
>Find what you need with Microsoft Search

>At the top of your Microsoft Office apps on Windows, you'll find the new Microsoft Search box. This powerful tool helps you quickly find what you're looking for, such as text, commands, help, and more.
>
>Improve comprehension with Line Focus
>
>Move through a document in Word, line by line, without distractions. Adjust the focus to put one, three, or five lines in view at a time.
>
>Line Focus drop down list in the ribbon and One Line option selected
>
>Updated Draw tab
>
>Quickly access and change the color of all your inking tools, all in one place.
>
>Simplify how you work with ink using new Draw tab additions: Point Eraser, Ruler, and Lasso.
>
>Microsoft 365 Drawing Tools pens
>
>Increase the reach of your content
>
>Accessibility Checker keeps an eye on your documents and tells you in the status bar when it finds something you should look at. Try it by clicking Review > Check Accessibility.
>
>Status bar showing that the Accessibility Checker is running
>Support for OpenDocument format (ODF) 1.3
>We now include support for the OpenDocument format (ODF) 1.3. The ODF 1.3 specification adds support for many new features.
>
>List of file formats from Word with ODT file format highlighted
>Try the Sketched style outline
>You can give a casual, hand-drawn look to shapes in your presentations with the Sketched style outline.
>
>Try out the Curved, Freehand, or Scribble options under Format Shape > Line > Sketched style.
>
>__ChatGPT__
>
>Co-authoring: Test the real-time collaboration feature by having multiple users simultaneously edit a document to ensure quick and accurate updates.
>
>Visual refresh: Validate the visual refresh claims by assessing the Start experience, ribbon tabs, monoline iconography, color palette, and window corners for the described modernized and clear style.
>
>Save your changes as they happen: Test the automatic saving feature by uploading files to OneDrive, OneDrive for Business, or SharePoint Online and confirming that changes are consistently and promptly saved.
>
>Dark Mode: Evaluate the Dark Mode functionality by ensuring that both the ribbon/toolbars and the document itself adopt a dark canvas to effectively reduce eye strain.
>
>Microsoft Search: Verify the effectiveness of Microsoft Search in helping users quickly find text, commands, help, and other elements within Microsoft Office apps.
>
>Line Focus: Test the Line Focus feature by navigating through a document in Word, adjusting the focus to display one, three, or five lines at a time, and confirming the absence of distractions.
>
>Updated Draw tab: Check the functionality of the Draw tab additions (Point Eraser, Ruler, and Lasso) and assess the ease of access to and modification of inking tools.
>
>Accessibility Checker: Validate the Accessibility Checker's claims by creating documents with potential accessibility issues and confirming that the tool detects and notifies users of such issues.
>
>Support for OpenDocument format (ODF) 1.3: Test Word's support for ODF 1.3 by working with documents in this format and exploring the new features introduced by the specification.
>
>Sketched style outline: Evaluate the Sketched style outline options (Curved, Freehand, and Scribble) under Format Shape > Line and confirm their effectiveness in giving a casual, hand-drawn look to shapes in presentations.

If you look closely at the response at this point it isn't very interesting. Most of it is pablum.
Some of it is almost nothing more than rephrasing the original marketing documentation
and adding words like "check." Our intent is to push it further by narrowing the focus
of response generation.

Prompt #3: Ask for more details
------------------------------------------
At this point, pick a testing idea or feature topic and ask ChatGpt to repeat with more
testing ideas. This is the point where the intent is to
see some advantage. Human typing speed is enough of a difference
in performance to see a time advantage. Exhaustion, boredom, distraction
are other possibilities. I like to imagine the tester is
creative, so I prefer to just hold on to the typing
speed disadvantage and use that as my basis for comparison. I get enough out
of that to satisfy my interestes.

The prompt instruction is generic. All it does is specifiy the claim #1.
Then it follows in subsequent prompts by repeating that request.

>__User__
>
>Come up with 10 more test ideas to check the claims for #1
>
>__ChatGPT__
>
>Certainly! Here are 10 more test ideas to thoroughly check the claims related to the Co-authoring feature:
>
>Concurrency Stress Test: Simulate a high-traffic scenario with multiple users simultaneously editing the same document to assess system responsiveness and the ability to handle concurrent edits.
>
>Real-time Update Latency: Measure the time it takes for changes made by one user to be reflected in real-time for other co-authors; ensure it aligns with the claimed "in a matter of seconds" update speed.
>
>Offline Co-authoring: Test the co-authoring functionality in an offline mode to confirm that changes are appropriately synchronized when users regain online connectivity.
>
>Conflict Resolution: Deliberately create conflicting changes between co-authors and evaluate the system's ability to handle and resolve conflicts without data loss.
>
>Cross-Platform Co-authoring: Assess co-authoring capabilities across different devices and operating systems to ensure seamless collaboration experiences regardless of the platform.
>
>Large Document Handling: Test co-authoring performance with large documents to confirm that the system can efficiently handle and synchronize changes without significant delays.
>
>User Permissions: Validate that user permissions are accurately enforced, ensuring that only authorized users can make edits, and unauthorized changes are prevented.
>
>Commenting and Communication: Evaluate the effectiveness of the communication features within the co-authoring environment, such as commenting and chat, to facilitate collaboration and discussion among users.
>
>Document Recovery: Simulate scenarios like unexpected crashes or network interruptions during co-authoring sessions and verify the system's ability to recover documents to their last consistent state.
>
>Version History Accuracy: Check the accuracy of the version history log, ensuring that it correctly captures and displays a chronological record of changes made by co-authors, facilitating easy tracking and rollbacks if needed.
>

The list is getting more interesting. It is not genius, or brilliant. It
probably strays away from Claims Testing in some of its output. It is not embarassing
human engineers with its superior insights, but it did take just one or two seconds to come up with and deliver a list of testing
ideas that are relevant to the feature and probably deserve further
analysis. Consider these:

- Offline Co-authoring
- Conflict Resolution
- Cross-Platform Co-authoring

I worked with the Office product teams when they were building
an automated test solution for the co-authoring feature
set, and all three of those problems weighed heavily on
their minds. The generated content, by whatever accident comes
of rolling dice against statistical tables of token relationships,
is relevant in this case.

I believe an individual writing that list would probably
spend an hour or so coming up with it. Maybe faster. ChatGpt saved
some typing time.

Some of the ideas might not make sense. I have no idea about "_Version History Accuracy_"
and whether that makes any sense at all with how the features work
together. But if I was a member of the feature team, I imagine
I would realize, quickly, if the idea had any merit or not.

I tried the same against claim #2, which was for the visual refresh on the toolbar.
The test ideas that ChatGpt generated for this were not
as interesting to me, but that is a subjective evalutation. I
am not a tester on the feature, so maybe my
perspective would change. But the main point, the cost
to get this list of ideas was under a minute of my
time, and less to reject it if I do not like it.

>__User__
>
>Come up with 10 ideas to check claim #2
>
>__ChatGPT__
>
>Certainly! Here are 10 test ideas to verify the claims related to the Visual Refresh feature:
>
>Consistency Across Platforms: Test Word 2022 on different operating systems (Windows, macOS) to ensure a consistent visual refresh across platforms as claimed.
>
>Tab and Ribbon Responsiveness: Evaluate the responsiveness of the newly refreshed tabs and ribbon, ensuring that they behave smoothly and quickly in different scenarios, including document loading and navigation.
>
>Style and Iconography Examination: Conduct a detailed examination of the monoline iconography, evaluating the clarity, simplicity, and relevance of the icons used throughout Word.
>
>Color Palette Evaluation: Verify the described neutral color palette, assessing its appropriateness, visual appeal, and potential impact on user experience.
>
>Window Corners Display: Confirm the presence of softer window corners in Word 2022 and assess whether they contribute to an improved aesthetic and user interface experience.
>
>Start Experience Modernization: Explore the modernized Start experience in Word, checking for improvements in layout, accessibility, and overall user engagement.
>
>User Feedback on Visual Changes: Gather user feedback through surveys or usability testing to understand how users perceive and interact with the updated visual elements, such as tabs, icons, and colors.
>
>Theme Compatibility: Test the visual refresh in conjunction with different themes to ensure compatibility and a visually pleasing experience across various user preferences.
>
>Customization Options: Check if users can customize the visual elements, such as choosing different color schemes or icon styles, to accommodate individual preferences.
>
>User Training and Adoption: Assess the ease with which users adapt to the visual refresh by monitoring user training needs and conducting surveys to measure user satisfaction with the new visual design.
>

You cannot help but inject yourself into this...
------------------------------------------
I decided to ask more details on one of the co-authoring test ideas.
We are three levels deep. Initial feature list, detail on
a specific feature, and detail on a specific test idea within
that feature.

At this point, the cost of iterating is still low, but it
is increasing. I am also making this decision selectively. While
the prompt template I am using is generic, and could be
programmatically applied as an expansion of every test idea,
I am not doing that.

This is an important aspect of interacting with AI that
Michael Bolton mentions a lot. Much of the "smarts" of generative
AI comes from something humans interpret when they read the
output. As he points, it is similar to how humans interact
with a mentalist or spiritualist act. We mentally put things there
which really arent. When I select something and ask for more detail, I am
filling in the gaps for an AI that isn't really exercising
intelligence or insights. It is, instead, creating
text from a series of complex dice rolls that cause me to think
of something or make a decision.

>__User__
>
>Elaborate on the following idea, relative to co-authoring, by coming up with 10 more test ideas based on it (no steps needed, just the idea).
>
>User Permissions: Validate that user permissions are accurately enforced, ensuring that only authorized users can make edits, and unauthorized changes are prevented.
>
>__ChatGPT__
>
>Role-Based Access Control (RBAC) Verification: Test the system's adherence to role-based access controls by assigning different permission levels (e.g., read-only, editing, admin) to users and confirming that each user can perform only the actions allowed by their assigned role.
>
>Dynamic Permission Changes: Verify the system's ability to dynamically update user permissions in real-time and assess whether changes to user roles are immediately reflected in their editing capabilities.
>
>External Collaborator Permissions: Test user permissions for external collaborators by inviting individuals who are not part of the organization to co-author a document, ensuring that their access is limited as per the defined permissions.
>
>User Authentication Impact: Assess the impact of user authentication changes (e.g., password resets, account lockouts) on co-authoring permissions, confirming that unauthorized access is promptly restricted.
>
>Collaborator Removal Validation: Validate the removal of a user from a co-authoring session, ensuring that the removed user immediately loses editing privileges and is unable to make further changes.
>
>Permission Inheritance: Confirm that when a document is copied or shared, the permissions are correctly inherited, preventing unintended disclosure or modification by unauthorized individuals.
>
>Simulated Unauthorized Attempts: Intentionally attempt unauthorized edits using different methods (e.g., direct URL access, unauthorized logins) to check the robustness of the system in preventing unauthorized changes.
>
>Guest User Permissions: Test the co-authoring feature with guest users who have limited access to the organization's resources, ensuring that guest users can only edit documents as permitted by their guest status.
>
>Permission Expiry Handling: If the system supports time-limited access permissions, validate that users lose editing rights once their specified time frame expires, preventing unintended access beyond the allocated period.
>
>Access Logging and Auditing: Evaluate the accuracy and completeness of access logs and auditing features to ensure that all user interactions, especially those related to permissions, are appropriately recorded for later review and analysis.
>
>User
>Please provide 10 more different ideas based on the same thing
>ChatGPT
>Certainly! Here are 10 more test ideas based on the theme of validating user permissions in co-authoring:
>
>Concurrency with Mixed Permissions: Simulate a scenario where users with different permission levels (read-only, editing, admin) are co-authoring the same document simultaneously to confirm that the system appropriately handles mixed permissions during concurrent edits.
>
>Offline Permission Verification: Test the enforcement of user permissions in an offline mode by attempting to make edits without an active internet connection and verifying that the system accurately restricts unauthorized changes.
>
>Permission Boundary Testing: Evaluate the system's response to extreme cases, such as assigning the highest and lowest permission levels, to ensure that it correctly handles and enforces permissions without compromising document integrity.
>
>Permission Propagation: Test the propagation of permissions across linked or embedded documents to confirm that changes in permission status in one document are reflected accurately in connected documents.
>
>External Sharing Security: Assess the security measures in place when sharing documents externally, ensuring that external collaborators have the correct permissions and that there are no unintended data exposure risks.
>
>Granular Editing Permissions: Verify the granularity of editing permissions by testing specific actions, such as formatting changes, content deletion, and document structure modifications, to ensure that users have precise control over permitted actions.
>
>Collaborative Permission Changes: Test the ability of collaborators to suggest or request changes to permissions during a co-authoring session, ensuring that collaborative decision-making regarding access levels is supported.
>
>Integration with Identity Management Systems: If applicable, validate the integration with identity management systems to confirm that user permissions are synchronized accurately between the co-authoring platform and the organization's user directory.
>
>Permission Recovery After System Outage: Simulate a system outage and verify that, after recovery, user permissions are restored to their correct state without any lingering effects or unintended changes.
>
>Cross-Application Permission Consistency: If co-authoring involves multiple applications (e.g., Word, Excel, PowerPoint), check for consistency in user permissions across these applications to ensure a seamless and uniform collaborative experience.

I asked ChatGpt to go into one further level of detail.

>__User__
>
>Come up with 10 different test ideas (just the ideas, no steps) relative to co-authoring that are permutations of the following idea:
>
>Concurrency with Mixed Permissions: Simulate a scenario where users with different permission levels (read-only, editing, admin) are co-authoring the same document simultaneously to confirm that the system appropriately handles mixed permissions during concurrent edits.
>
>__ChatGPT__
>
>Limited Editing with Mixed Permissions: Simulate a scenario where users with mixed permissions collaborate on a document, specifically testing how well the system handles concurrent edits when some users have limited editing rights.
>
>Admin User Overwrite Test: Evaluate the system's response when an admin-level user attempts to overwrite or disregard changes made by users with lower permission levels during co-authoring.
>
>Dynamic Permission Changes During Collaboration: Test the system's ability to dynamically change user permissions in real-time during a co-authoring session and assess the impact on concurrent edits.
>
>Role Reversal Scenario: Create a scenario where users with lower permission levels initially make edits, and then permissions are dynamically elevated during the co-authoring session to assess how the system adapts to role changes on-the-fly.
>
>Permission Level Swap: Simulate a situation where users with different permission levels swap their roles during concurrent edits, checking if the system correctly adjusts and enforces the updated permissions.
>
>Randomized Permission Changes: Introduce random changes to user permissions during a co-authoring session to evaluate the system's robustness in handling unpredictable modifications to access levels.
>
>Multi-Document Permission Collaboration: Extend the test to multiple linked or embedded documents, assessing how the system manages concurrent edits when users with different permissions collaborate across various interconnected documents.
>
>Permission Inheritance Variation: Test scenarios where permission inheritance varies based on document sections or elements, ensuring that the system accurately reflects and enforces permissions at a granular level.
>
>External User Interactions with Mixed Permissions: Include external collaborators in the co-authoring session and evaluate how the system handles concurrent edits when both internal and external users with varying permissions are involved.
>
>User Permission Delegation: Simulate a scenario where users can delegate editing permissions to others during co-authoring, ensuring that the system correctly manages delegated access and enforces permissions accordingly.

There is a mix of new, good ideas in this set, combined with some "circle
the drain" ideas that feel like repeat of prior. I take that
as a sign that the technique is probably going to be ineffective
with further iteration.

My assessment of the above exercise is that with just a few minutes
of interaction with ChatGpt I got a lot of test ideas which
would have probably taken me multiple hours to write down on my own.
I am not in the "better than most testers" camp on this one. I have
seen too many good testers to be so cynical. But I don't know any
testers who would record ideas of this caliber this quickly.

It doesn't always work...
===================================
This article is long enough, but I tried the same exercise using the
readme.md output for a <a href="https://github.com/WayneMRoseberry/DupIQ">REST API</a> that I wrote. I combined a description of
"Flow Testing" against the readme content using the same approach
as above.

The output from ChatGpt was uninteresting and shallow. I selected Flow Testing
because the methods in the REST API are meant to combine with each other.
Instead, ChatGpt treated them as atomic operations with atomic problems, not
trying interesting combinations as the definition for Flow Testing suggests. See
my starting prompt:

>User
>
>I am going to provide a description of a test analysis technique. Acknowledge receipt of the technique by providing a one sentence summary of it.
>
>Flow Testing
>
>Do one thing after another 
>1. Perform multiple activities connected end-to-end; for 
>instance, conduct tours through a state model.
>2. Don’t reset the system between actions.
>3. Vary timing and sequencing, and try parallel threads

This kind of thing is frequently a "write a better prompt" problem, and
that may be fair. Consider this an evaluation of my method, the prompts I
used, and how well it worked for this particular feature set.

But then again, finding out it wasn't useful for me was fast and cheap.

Thoughts on the method - interactive, or automatic?
===================================
There are two ways we utilize chat based generative AI.

One is as an interactive conversation between a person and
a chat-based LLM. The person crafts the prompts ad hoc, adjusting
them as they try to get what they want or need from the interaction.

Another is with an application crafting the prompts and extracting
the responses based on input gathered on behalf of the user
and then using the output in some way for benefit to the user.

I wrote the article based on an interactive conversation, but we
could imagine it as an application generating the prompts. In this session,
there were several prompt boilerplates:
1. I am going to introduce a type of testing heuristic. Below, after I do, acknowledge your receipt of it with a short, single sentence paraphrase of the technique.
2. I am now going to present a (NAME) list that came from the manufacturers of Word. Given this list, come up with ideas for claims testing, based on this list. You do not need to write the steps, just some basic ideas for testing claims that derive from the feature list:
3. Come up with 10 more test ideas to check the claims for (selected claim)
4. Elaborate on the following idea, relative to (selected topic), by coming up with 10 more test ideas based on it (no steps needed, just the idea).

The boilerplates do not need to change based on the testing ideas
or the production description under analysis. If we can generalize
a prompt strategy, we can automate the prompting to produce
content even faster and in larger volume. The value to us
derives from how well that given prompt generalizes, how often
it produces content we find useful.

The challenge with automated solutions of this class is
evaluating the volume. In the experiment I did here, where
everything is interactive, I got several dozen testing ideas by
controlling the focus as I went. I asked it to focus on
one feature area, co-authoring. The original document had 10
feature areas. After co-authoring, I asked it to focus on 1 of 10
or so test ideas, then twice more. Were this process allowed to play
out on its own automatically, that would have been 10x10x10x10 test
ideas generated - 10,000 to review, select and reject from. Something
that large is difficult to evaluate. Sometimes the cure is more 
of a problem than the disease.

I leave this problem to cleverness and invention to ponder.
There are ways to present concepts, ideas, output, and outcomes that are
more compact, easier to process.

Bottom Line: Playing to useful fuzziness as a generative AI strength
===================================
I said recently to someone that I am sometimes surprised how
broad our toolkit is when we test. We use all sorts of tools and
methods and techniques to find issues. Some of those methods
demand exact, precise information that serve as a basis for
comparisons and inputs and oracles for checking behavior.
Some of those methods are about something less concrete, such
as coming up with or recording ideas for things to try.

When we assess something like generative AI, or any technology,
for use in testing, we should always keep in mind what we need, and
how that technology does or does not fill those needs.
