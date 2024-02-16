One of my favorite test techniques is scenario testing. Find a problem
the end user is trying to solve, use the product to solve that
problem, and report any issues or bugs you discover along the
way. This aligns well with most software development approaches,
which tend to align requirements around use cases, user scenarios,
user stories, and a variety of other labels that tie requirements
to something the user wants or needs.

These ideas tend to be captured in small little nuggets. For testing, we often want bigger.
========================================================

For example:

> _An engineer needs to create an automated check for modifications to
> the web application their team is building...._

The above would typically provide more details, but the starter here is
enough to capture the idea that we tend to work with small stories. I chose
the above story, and then extended it...

> _An engineer needs to create an automated check for modifications to
> the web application their team is building. The engineer intends to save
> time so they use a web-session recorder to capture their steps in a script.
> The web application is undergoing changes in its visual layout, and the
> same scripts are used to check changes as they are submitted._

I bumped the story up. The first bump would likely be found in some
form in the list of requirements, and while it violates some rules of
not including the solution in the story, it is critical to capture the idea
that the end user in this case is trying to avoid writing the scripts themselves.
This is part of the value proposition of the product in quesiton.

The next bump is something derived from the product marketing, claims and
promises of robust and reliable scripts. I want to imagine different ways we
exercise that claim. One of the most important factors that affect our relationship
to automation scripts is changes in the product under test.

Oh... I forgot something...

> _The web page is written in a way that is not well designed for automated scripting._

This is another extension of exploring the value claims of the product. One
of the features of this tool is automatic self-healing, built-in intelligence
that intends to make scripts more robust. That is pretty easy when the web
application is well architected for testability, but when it is not, what happens?

How do you grow the story?
========================================================
Let's break down an approach. Truth is I don't really do this by breaking
it down into steps. I intuit the final outcome. But internally, it is essentially
something like the following:

1. Start with a basic user story

These can come from anywhere. You might have user stories as part of your project. They might
be in a Jira ticket. They might be in a list somewhere.
If your team does not explicitly track user stories somewhere, look at other sources of
information. Marketing materials and end user documentation/help content is one source. Recent
product demos are good. Is there a body of training and how-to videos online from
the company or even end-user consultants and enthusiasts?

You just need one story.
For example: In the test session I used for this article, I took the story as implied by the ContextQA website product promotional claims.

3. Think beyond the moment of the story, build a context

The stories you will find are almost always very isolated and sanitized. This is
because the stories are easier to use and fit their purpose better (to sell, to educate,
to illustrate) if they are simpler. The optimum, simple case works better for
these stories the way everybody else uses them.

The truth is our the user's situation is rich and complex. Pointing
our intent to simple versions of that situation may be an effective
way to achieve clarity and focus, but our success relies on complementing that
focus with discovery of the exceptions and conditions that complicate the story.
When you test, you want to capture some of that context in its
more complicated, rich, unpredictable way.

Ask questions, such as:
- what data does the user have available to them?
- what process, work or other activity preceded this story?
- what does their overall workplace, or system look like? What state is it in?

You don't need to imagine every possible change to the situation. You
don't need to imagine a complete and thorough description of the context.
You just need enough interesting variations - sometimes only one or two - to
make the testing reveal something unanticipated and new.

For example: In this testing session, I pointed ContextQA at a website I have
been building indepenent of this testing initiative. It's problems and bugs
are real because it is still rough, still under development, and I actually
have a real purpose for the site.

5. Search for the "Yes, and..." opportunities

Just like the situation in the user story tends to be sanitized and simple,
so to are the actions the user takes. The "what next" is almost always a linear
path to a singular point of anticipated success. There are no deviations, distractions,
interruptions, and somehow at the end of the story, the user is left whole,
satisfied and fulfilled. Everything done, story over.

Real usage is frequently very different. Users abort the sequence to
collect information elsewhere or check state. They want a slightly different
behavior than the simple script implies. They want or need to complete the
task as part of a larger set of tasks that came before, after, and even concurrent
with the task they are executing.
