I wrote a test specification this morning for some testing I want to try
against a product I looked at earlier this week. I want to try to
intentionally deviate away from happy path testing to see what problems
I will find when doing so. The test specification is small, simple, was
easy to write.

The product: DoesQA, a no-code test automation solution
=======================================================
<a href="https://does.qa/">DoesQA</a> is described as a no-code
test automation solution. It offers a drag and drop graphic interface
for building workflows against web-based applications. The workflow
actions are tailored toward tasks which support automated checking,
such as finding web-page elements, manipulating and setting them,
checking their value, and a variety of other capabilities one finds
with similar products in this space.
![A screenshot of a workflow in DoesQA](/assets/doesqa_busrouteworkflow.png)

Selectors, and the happy path
-------------------------------------------------------
I was giving DoesQA a free trial run, and had hit a problem
with it failing to select a link on a page I created. I shared
the problem with Sam Smith, a founder from DoesQA, in a post
he had made on LinkedIn about DoesQA. He responded quickly, offering
a different way to get to the element. In the reply, he indicated
that the way I was trying to access the element was:

> You found an interesting issue, the Visual Text selector
> used was also able to see into the non-body parts of the
> DOM (head for example) - This has now been updated and is
> working as intended.
> 
>However, this is not a recommended use case for good testing.

The fully reply is below.

![A screenshot of a conversation I had online with a member of the DoesQA team](/assets/doesqa_happypathconversation.png)

Directed toward a customer trying to get the product to work, Sam's
comment is fine. An end user want to solve their problem and get on
their way. As a tester that same comment raises questions and concerns.
As the response indicates, my prior attempt to use that selector
had exposed a bug in the product.

What more lies ahead?

The "Stray from the happy path" heuristic
=========================================================
Products that work as toolboxes often face a situation where
there are many ways to use the tools, and some of those ways
are much better than others. Some of the ways may be undesirable,
but are permitted because sometimes the users find themselves
with other choice.

When we make a tool, and we are checking that it works, we have
a tendency to select the preferred ways to use those tools. We
optimize for them, we look at them more often, we understand them
better. They are preferred usually because everything lines up
better with fewer unknowns, fewer problems to handle, fewer possibilities
of exception.

This means that the code paths behind the less preferred use
cases, those that stray from the "happy path" have not
been examined as closely. There are usually mistakes there
that go beyond the reasons why the use case is not preferred. Often
there are serious breaks.

As testers, we want to stray from the happy paths to find
the forgotten parts of the code and observe what happens. The
trick is how we get there. From the beginning, I stumbled on
this case because I was testing against a web page that did not
have good markup (lack of an id attribute on the element in
question), so I took a shortcut and tried selecting based
on element text. I did not intend to stray from the happy
path, I just wound up doing it.

For sake of the testing, I want to stray more intentionally.

Methodically straying from the happy path
=========================================================
I don't need to know every non-happy path action. I need some, enough
to look for interesting failures. I use the following as a guide:
- My earlier Text Selector experience
- Prior knowledge that
  - #id selectors work well, so everything else tends to be problematic
  - ambiguous elements in a page require special handling
  - selectors inside collections are problematic
- The DoesQA documentation tells people how to get selectors... so use that to see where it falls off
- DoesQA offers a set of options for referring to elements - use each one of those

I used the above to create a list of test ideas, created by combining
DoesQA element selection capabilities against different actions DoesQA
supports, against a set of problematic ways of referring to elements inside
a web page.
