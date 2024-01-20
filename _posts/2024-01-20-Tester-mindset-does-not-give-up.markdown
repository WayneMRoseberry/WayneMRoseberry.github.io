The tester keeps looking for the bugs
===========================================
![Image a hand with a pen pointing at a corkboard of beetles, one of which is a bigfoot/beetle hybrid.](/assets/bigfootbug.jpg)
_The prompt for this Bing Image Creator generated image is "tester pointing to a conspiracy style corkboard with picture of giant bugs on it, some of which are very similar to classic pictures of bigfoot"._

It is common that we see things during testing we do not understand
at first. We form hypotheses about what is happening, and then
later learn they were wrong. The most disappointing case is when
nobody can reproduce what we saw before. Or we may see something,
try again and it goes away, and we doubt ourselves. Maybe we
were mistaken and did not see what we thought we did. Maybe we are
just "doing it wrong."

When that happens, the tester tends to dig in and look more closely.
Try again. Change things up. It helps if the tester has some record
of what they were doing to help them remember, check against the
possibility of mistake.

Testing and time... all that time
------------------------------------------
This whole investigation started off with a
a single test case. I spent less than a minute imagining
it. I already had a website ready, so there was not
much preparation time. The test case itself was
about 10 minutes of execution. It only took this long
because launching a run adds about 2 minutes of wait time
for results. I was also narrating for a video as I went,
which probably doubled my execution time.

Investigating the problem I saw during the test took
probably two hours. Perhaps more. During that time, I was
constantly changing the nature and type of testing. I started
off trying to imitate hypothetical user actions, but then I
move into hypothesis checking, exploration, and straying from
regular user actions in order to narrow the case.

This is something that testers who do a lot of
exploratory work understand well. Even if
you decide to be driven by something whose steps
you thought of ahead of time, most of the time that
is a very small amount of the actual testing work. In my case,
I think the ratio was a 30 to 1 ratio of investigation time
versus pre-planned step execution.

Testing testing tools...
==========================================
![the ContextQA logo](/assets/contextqalogo.png)


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

Things I like about ContextQA
----------------------------------------------
<h3>Things I like, that it brings to the table</h3>

- __Your machines await__: a lot of no code solutions are meant to run your tests in a data center they provide, using resources on demand. While common for the category, this is also no small value. I see this as one of the reasons why somebody would invest in a tool like ContextQA. It takes a whole pile of lab costs off your hands.
- __Record is a useful feature__: the "no code" space is a bit of a misnomer, as crafting custom steps and cases requires being comfortable with developer level information about web pages. The ContextQA Chrome Extension is a helpful aid getting a user to working case steps without having to do all the authoring and selector grabbing themselves. The recorded steps also grab multiple selectors at once, and employ them in a self-healing strategy at run time. I found during some of my testing attempts to make cases that would fail because of control ambiguity were thwarted by this strategy.
- __Canned coding patterns__: this, in my opinion, is what the no code solutions really bring to the table. They are widget style encapsulations of UI automation design patterns which tend to be more resilient to web UI behaviors that destabilize a lot of hand-coded script.
- __The Playwright actions are available for viewing__: Once I found this on results I used it a lot to understand why certain things happened during the test case runs. ContextQA would be wise to lean heavier on these kinds of diagnostic, transparency features.
- __Video playback__: I used this feature the most for the test investigation that follows. It is very helpful to have a video that shows what the test was doing, what the page looks like at the time of test.

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

 __This is quite alarming.__
_Not capturing the same sequences of steps that a user executed
means that when run again, the test case is going to do something
different. This is even more concerning if the test case does not
fail, because unless the behavior of the run is inspected closely
people may be led to believe a scenario is executing and passing
that has not been run at all. It leads to false negatives, something
most people consider the most alarming of test automation problems._

> __Bug or not is subjective on this kind of thing.__
> 
> Record and playback is a feature that is never going to achieve
> full fidelity between what was originally recorded and what is played back.
> Recording everything creates extra noise that makes scripts unwieldy
> and brittle. Some actions are outside view of the recorder.
>
> For decades, people working with record and playback are used to editing recordings to
> make them more robust, add capability the recorder cannot capture.
>
> I could have added a step to press the TAB key. As you will see later,
> order of operations affected whether the problem reproduced or not. The
> user in this case has workarounds, so the bug moves to a question of whether
> or not the mismatch is reasonably obvious, or if the workarounds are indeed
> valid for what the user is trying to automate.
>
> As a tester, I see my obligation to provide as complete and accurate a
> description of product behavior as I can so the product team can investigate
> whether it is something they can, or should address.

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

I added Selection elements to my web page for choosing delivery method (onehour, overnight)
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
I crafted a sequences that would produce a different end state.

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

I now have a repeatably confirmed hypothesis:
> _Changes to the text property of Input elements in a form followed by
> selecting options from a Select element recorded by
> ContextQA Chrome Extension test case recorder will not trigger
> the onChange() event until interacting with a non-Input or
> non-Select element during playback, leading to potential
> omission of operations, altered order of operations, and
> changes in application state._

Not the same bug we were hunting, but a successful morning out regardless
================================================
There are many impulses that causes us not to go looking for odd
behavior we noticed. We might be busy doing other things, we might
doubt ourselves, we might feel compelled to assign the behavior
to the "don't do that bucket." In this particular case, I avoided
all of that by doing the following:

1. Keep note of what I found and put it on a "do next" list if I am busy with something else
2. Be ready to deviate from your test cases and start a bunch of impromptu exploration
3. Keep good notes of what I observed, often records of page content, logs, screenshots - and in this case, video
4. Commit to the certainty that you saw something, and what you are trying to do now is understand what it was
5. Form a hypothesis and see if you can either refute or confirm it with evidence
6. Use the refuted hypothesis as the basis to form new hypotheses and modify your test as appropriate

This is how we find the tricky problems. The monsters await.
