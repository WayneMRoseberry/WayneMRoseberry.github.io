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

Before we get started
=============================================
I am not an employee of ContextQA. I was not asked or paid
by them to do this testing. I performed the testing to learn
more about the application, and as material for this article.
Deep Barot, the CEO of ContextQA has been coorperative and
helpful every time I ask him questions. I have shown him the
issue that is the target of my investigation and writing
below. I used a free trial account for this testing, and every time
I hit a limit I contacted Deep and he extended it.

This article is not a review or critique of ContextQA. This
article is about testing, and in particular investigating a bug
that turns out to be harder to repro than one initially believes.


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
not to TAB between controls when they record themselves? I consider
that a problem, but I also wanted to get through a complete and
working version of my scenario.

In the previous run I had changed the fields in this order
1. building for start
2. building for destination
3. pickup time
4. rider name
5. TAB

That TAB was in there to get the change to the rider name
to trigger the onChange() event. I changed the order to something
like this:

1. building for start
2. building for destination
3. rider name
4. pickup time

Changing focus inside a web page is all that is normally required
to trigger the onChange() event for a control. By choosing
pickup time from its Select element, I figured that would separate
if the issue is just with capturing TAB keypress, or if it was
something else.

On playback, I saw the problem reproduced. The rider name was
not changing on the summary of pickup text below on the page, even
though it was updated when I recorded the session. Rather than this
being about not capturing TAB, I now had a new hypothesis:
> _Changes to the text property of Input elements in a form recorded by
> ContextQA Chrome Extension test case recorder will not trigger
> the onChange() event during playback._

I sought to test this hypothesis.

Seeking the repro
==================================================
I put the investigation to the side for a bit, but made note of the bug.
This Input field issue came up while I was considering a different investigation,
which was seeing how well the recorder worked with different kind of markup
on HTML elements. I was still trying to understand how it decided what
kind of selector to assign during record, and how it used those selectors
during playback.

I created a test web page that had most of the different kind of HTML elements, each
with an id field on them. My assumption was that the recorder ought to prefer
the id field. While testing this, I found the truth was far more sophisticated, and
ContextQA's behavior far more resilient because of it.

During that testing, I re-visited the Input field onChange() behavior, partly
to investigate some of the other behavior I was trying to understand regarding
selectors on the page. I had an Input element's onChange() event set to update
another element's innerHTML property. To my surprise, during playback, the target
element innerHTML did update as it should have.

What happened to my bug?

Going back to the repro
=============================================
My bus route application page is a little weird. The pattern for something
like booking a pickup time is to input what you want and choose some kind
of "Submit" or "Request pickup" button. All the collection of all the
changes happen on that button click. I also wanted to demonstrate easily
how different the application state could be, and how that could have a
dramatic effect on the final order.

I made a simulate fruit ordering page.

The page had two buttons, one to add guava, another to add apple.
As each item was added, an order list was updated below the buttons.
There was an input field to add a custom item to the order.
There was a submit button that would collect the order list, display a summary span, place the order list in the summary, and put in an "Confirm order" button.

I recorded myself doing the following sequence:
1. click "guava"
2. click "apple" (order at this point reads ",guava, apple" - yes, extra buggy comma, might fault)
3. type "banana" in the custom field
4. click "submit" (the order and the order summary both read ",guava,apple,banana"
5. click "confirm order"

I saved the recorded steps, and did a run of the test case.
The bug did not repro. Both the order list and order summary had ",guava,apple,banana" as they were supposed to.

This triggered an entire early Saturday morning of trying different ways to reproduce
the failure.

The final repro moments
=============================================
I realized that whenever ContextQA was clicking on a button,
it triggered the onChange() event for the Input element. The only difference
I could see was that my bus route web page had Select elements and
I had recorded myself choosing an option from the list.

I added Selection elemtns to my web page for choosing delivery method (onehour, overnight)
and insurance options (uninsured, insured). I recorded the following
steps:
1. enter "banana" in the custom field
2. select "overnight" for delivery (order list now reads ",banana")
3. select "uninsured" for insurance options
4. select Submit (order now reads ",banana")

During playback, in step 3 and 4, the order list was blank until step 4 when Submit was clicked.
This was different from the recorded session, when "order list" changed immediately
after selecting "overnight." There was a difference in application behavior,
but the final end state was correct. This difference should be
enough to demonstrate a problem, but I wanted the evidence stronger, so
I crated a sequences that would produce a different end state.

First, I added a span of text that tracks the commands in order and changed
every element on the page to call that command with a string identifying them.

The new sequence was:
1. enter "banana" in the custom field
2. select "overnight" for delivery option (order list now reads ",banana" and command list reads "addcustom,deliveryoption")
3. add the text "boat" to the end of the custom field
4. select "onehour" for delivery option (order list now reads ",banana,bananaboat" and command list reads "addcustom,deliveryoption,addcustom,deliveryoption")
5. click "submit" - (order summary displays with ",banana,bananaboat" and command list reads "addcustom,deliveryoption,addcustom,deliveryoption,submit")

I ran the test case, and watched the video. The order summary was just ",bananaboat" instead
of the expected ",banana,bananaboat" and the command list read "deliveryoption,deliveryoption,addcustom,submit",
having changed the order of operations and dropping one.

Here is video of me recording the above:

<video width="500" controls>
  <source src="https://waynemroseberry.github.io/assets/contextqa_recorder_fruitorder.webm">
</video>

This is ContextQA's video of the run of that same test case:

<video width="500" controls>
  <source src="https://waynemroseberry.github.io/assets/contextqa_playback_fruitorder.webm">
</video>
