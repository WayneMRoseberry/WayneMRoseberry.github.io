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
