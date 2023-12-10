Asking the LLM for test ideas by narrowing its focus
===========================================
One way to use an LLM to generate test ideas is to
do the following:
1. give it a prompt intoducing a category of test analysis, ask the LLM to respond with a short summary
2. in the next prompt tell it to apply the test analysis category to a passage of text describing a product behavior, responding with test ideas without detailed steps
3. after the prompt, either tell it to continue or choose on of its test ideas and ask it to elaborate with more test ideas narrowed to just that one point

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
of on your own, but it does it much faster. As a colleague
of mine, Jason Arbon, points out, often times you don't
need better than a person would create. Often it is helpful
to have something good enough that makes the work easier.

Example: Applying claims testing to Microsoft Word marketing literature
===========================================
For test analysis category, I selected "Claims Testing", the
description of which I got from James Bach's publication
of his Heuristic Testing Strategy Model.

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
treated this like a fast grab and go use case because I am trying to
mimic a situation where gathering the inputs is inexpensive. I am also
mimicking a situation where the input data is fuzzy and ambiguous.

The instructions tell ChatGpt to only list ideas, not full steps. This
is designed to keep the LLM output likewise high level, but also
to get more ideas into a single response. More ideas increases the value
of the session by reducing labor to record the ideas. Higher level means
there is less detailed information to painfully analyze and correct. Rejecting
or keeping an idea is meant to be fast and easy to do.
>User
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

If you look closely at the response, some of it is interesting. Most of it is pablum.
Some of it is almost nothing more than rephrasing the original marketing documentation
and adding words like "check," but some of it hint that we can get more out of it
that might start to pay off.

Prompt #3: Ask for more details
------------------------------------------
At this point, pick a claim and ask ChatGpt to repeat with more
claims testing ideas. This is the point where the intent is to
see some advantage. Human typing speed is enough of a difference
in performance to see a time advantage. Exhaustion, boredom, distraction
are other possibilities. I like to imagine the tester is
creative, so I prefer to just hold on to the typing
speed disadvantage and use that as my basis for comparison.

The prompt instruction is generic. All it does is specifiy the claim #1.
Then I follow by repeating that request.

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
>User
>Come up with 10 ideas to check claim #2
>ChatGPT
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
>User
>Elaborate on the following idea, relative to co-authoring, by coming up with 10 more test ideas based on it (no steps needed, just the idea).
>
>User Permissions: Validate that user permissions are accurately enforced, ensuring that only authorized users can make edits, and unauthorized changes are prevented.
>ChatGPT
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
>
>User
>Come up with 10 different test ideas (just the ideas, no steps) relative to co-authoring that are permutations of the following idea:
>
>Concurrency with Mixed Permissions: Simulate a scenario where users with different permission levels (read-only, editing, admin) are co-authoring the same document simultaneously to confirm that the system appropriately handles mixed permissions during concurrent edits.
>
>ChatGPT
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

