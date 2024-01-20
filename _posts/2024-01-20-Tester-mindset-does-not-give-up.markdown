I recently tried testing <a href="https://contextqa.com/">ContextQA</a>, a no-code automation
solution. One of the features they offer is a Chrome Extension that allows a user to
record actions on a web page and capture those as steps in a test case for playback
later.

I tried a user scenario test where I used the recorder to create a test case. I observed
something I believed to be a bug during that test. I thought I understood the bug
behavior and left it in my notes. Meanwhile, I moved on to another test designed to
cover how well the recorder did using different types of markup on elements in pages
as selectors. During that test, I thought I had conditions which ought to also hit the
bug I saw during my user scenario test, but they did not.

I hate it when that happens.

Digging, digging, digging...
=============================================
I had the following scenario in mind:
> A person is assigned to test a web page with elements that change based on form
> element changes from the user. The person records a test case of themselves interacting
> with the form, and then checking if the page is updated. They then playback
> the same test case, expecting it will replicate what they recorded.

I had an existing web page, <a href="https://waynemroseberry.github.io/assets/testform.html">a simulated corporate shuttle bus schedule</a>, that
I use to test web testing tools. This page has three Select elements (building pickup, destination, and pickup time),
and an input box (rider name). Changing any of these elements updates a section of
text at the bottom with rider name and pickup location specified.

You know, that recording isn't really you...
--------------------------------------------
While recording myself, after hitting the Rider Name field, I typed
TAB because the form doesn't have an "Update" button, so I just used TAB
as a way to change focus to the next element. This triggers the onChange()
event on the Input control that updates the Span element below with
the rider name.

<video width="500" controls>
  <source src="https://waynemroseberry.github.io/assets/contextqa_recorder_playback_busroute_mismatch.mp4">
</video>

I noticed when looking at the steps that the TAB keypress was
not indicated in the recording. I wasn't sure if this was a problem.
Maybe the recorded step to enter the rider name into the rider name
field was sufficient to trigger the change, or perhaps the next click
on one of the pieces of text below it would cause the onChange() event to
fire.

During playback of the test case, I confirmed my suspicions. In the video
of the sequence, the page was not updated as I expected. The TAB key
press was not getting captured.

Maybe I'm just doing it wrong...
--------------------------------------------
Perhaps all savvy users of ContextQA are just going to have to learn
not to TAB between controls when they record themselves?

The final repro moments
=============================================
This is ContextQA's Chrome extension recording the steps through my web page.

<video width="500" controls>
  <source src="https://waynemroseberry.github.io/assets/contextqa_recorder_fruitorder.webm">
</video>

This is ContextQA playing back those same test steps. The video was captured
by ContextQA.

<video width="500" controls>
  <source src="https://waynemroseberry.github.io/assets/contextqa_playback_fruitorder.webm">
</video>
