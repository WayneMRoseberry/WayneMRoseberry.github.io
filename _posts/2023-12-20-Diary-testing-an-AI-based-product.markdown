I joined the Checkie.ai beta. Checkie.ai is a tool meant to
assist testers coming up with ideas for testing web pages.
It is a Chrome extension you can invoke on a given
page that will target different bots at the page and
product testing ideas, some suggestions for things
to try on the page, some suggestions for possible bugs on the page.

I was curious about the product idea. I know Jason, the
author of the tool. I wanted to see how the tool
was coming along, but I also needed something to 
keep my testing muscle in shape, and I really wanted
some of that muscle exercised testing something
built around AI.

Promt-based applications
===========================================
One of the ways to use generative AI is as follows
- some tool accepts input on the user's behalf
- the tool processes the input, breaking it into
shorter passages, removing unnecessary material
- the tool selects or constructs canned, templatized prompts that are combined with the processed input
- the LLM produces a response
- the tool processes the response and takes some action, which might mean presenting it in an appropriate form to the user

This type of application intends to have a more controlled,
more curated experience for the user by taking
over the prompt and handling the input going in and
out. This is very different from open-ended chat
experiences where the user is entirely responsible for
the prompt fed to the LLM.

While our typical notion of prompt engineering is a large
part of the application functionality, so too, and maybe
even more important are the parts that process the
input and output to and from the LLM.

Early, early application approach
===========================================
Checkie is barely up and walking. When I first tried to test it,
I wasn't even able to install it. It was written on the Apple
platform, and I use Windows. While installing custom extensions
on Apple is pretty easy, Windows Chrome doesn't allow extension
installations that did not come from the Android store (unless
you are using developer tools in Chromium). I had to wait a week
or two until a version was pushed through the Android store.

When something is so early, hitting it with a full battery
of tests is almost pointless. The development team (of 1, in this case)
might not even know what they want the application to do. You
might spend days exploring something only to find that the
first bug you reported convinced them they should cut the entire
direction of you exploration.

In this case, it is often best to do a quick survey. Look at the
surface menus, controls, behaviors. Make note of anything you
see going wrong, and move to the next item. If some behavior seems
very broken, don't invest much time investigating details. Instead, tell
the development team what you tried, and give them some pointers on
where to look next.

Here are a few things I noticed in my survey:
- custom bot settings were not persisting and did not seem to get used
- reports from the built in bots were not persisted anywhere
- clicking outside of Checkie made the screen disappear, along with reports... lost data!
- asking Checkie to run again on the same page produced different reports - I could not get those reports back
- attempts to run Checkie against HTML page I wrote locally spun forever without producing a report
- attempts to use Checkie against online sample testing pages produced similar behavior as against my local pages

I sent that list as feedback, as well as the online test sample page I used.

While I did look at the canned bots, I didn't focus as much on
how good the testing feedback was from them. I was hitting public
pages (Wikipedia, MSN, Facebook, LinkedIn) and all of those are really
busy with a lot going on. Further, all are shipped, in production
systems, and if Checkie is supposed to help a tester I would think one
would want to do that in a page still in production. This is what
led me to my local page, as well as the sample testing page - but both
of those caused Checkie to give me nothing. I figured a deeper
exploration should wait until I got a new build.

Digging in a little deeper
===========================================
A day or two later I was offered a newer version. This one fixed
the custom prompting.

What follows is an example of how end to end testing causes us
to find problems we didn't anticipate, weren't looking for, but
which prove important to the development team.

It is also an example of a tester being told "You're doing it wrong,"
who persists anyway... and then is thanked later for doing so.

The custom prompt feature allows the user create their own
bot with its own instructions. This made me think of two things:
1. As a customer, I might have special business rules important to me that I want all my testers to be alerted to on every page
2. I wanted to understand how Checkie uses its prompts, and the custom bot allowed me a way to learn more that the canned bots did not

I wrote a bot called "The Cheese Incident" with the following prompt
>If there is no mention of cheese on a page say "No cheese here"
>If there is mention of cheese on a page say "Checkie found cheese"
I then gave it a web page with the word "cheese" in it, and it said "No cheese here"

I experimented with different instructions. The home page to
the website for my blog has all the article titles with all words
capitalized. I opened that page, and changed the custom bot to prompt to
"If there are any phrases on the page that do not use an initial capital followed by lower case, report them."
I tried " It reported only the web page title, none of the links inside the page. I tried
changing the prompt to "Report all phrases on the page that capitalize every word in the phrase."
Still nothing.

I mentioned this to Jason. He response (Jason told me to
be open about this stuff) was 
>Yeah, I think that’s a weird thing to ask ;)...
>I would expect normal usage to be more like “ are all of the links on this page capitalized”. Or something like that.

I changed it to "Are any of the links on this page capitalizing every word in the link?", and Checkie's response was "No."
That was incorrect, because every link on the page was capitalizing every word.

Several hours later when he was able to get to a computer, Jason notifies me. The part of Checkie that was
processing the page was not seeing the whole DOM. The custom prompt was
only processing the very top of the page.

My tester persistence paid off. It may still be true that the type of
prompt I was writing is not really the kind that ought to work well, but there
was still a real bug having nothing to do with the prompt causing the
problem.
