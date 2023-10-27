Making Things Easier With Names
==============
![An AI generated cartoon of a group of groundhogs at a meeting wearing name tags](/assets/groundhognametags.jpeg)

Test Cases that are Easier to Read and Write
==============
I advise testers, or anybody describing a set of tests they have already identified,
to describe them using the simplest and most efficient means possible. It
saves time writing and maintaining the cases, but perhaps more importantly it makes the
cases easier to review.

One way to make test cases easier to understand, review, and read, is to make them visually compact
by naming the parts they are built from. The test cases are then shown as combinations of different
named pieces.

Invoice System Naming Example
===============
Imagine we have a set of typical editing operations we perform on sales invoices in a list:

>Invoice_Actions
>---------------
>- add new invoice
>- modify existing invoice
>- rename invoice
>- delete invoice
>- mark invoice as inactive
>- mark invoice as active

Now imagine there are different roles in the system

>Invoice_Roles
>---------------
>- salesperson
>- customer
>- inventory clerk
>- system admin

Or imagine there are different invoice types or states

>Invoice_States
>---------------
>- in-house invoice
>- third-party invoice
>- archive invoice (old system)
>- past-due invoice

Making Test Cases From Names
================

Imagine we want to test what happens when users in every role attempt all
possible actions on invoices. The list of invoice actions is six items. The list
of invoice roles is four items. That is a list of 24 items with lots of
repeated text. That kind of thing puts people to sleep.

Imagine, instead, it looks like this:

>User Roles and Invoice Actions
>-----------------
>Check each combination of Invoice_Roles with Invoice_Actions (24 cases).
>The roles customer and inventory clerk should be disallowed any write operations.

In two short statements, anybody reader can infer rapidly the intent of the
test cases, imagine what the combination looks like, and recognize how
some of the combinations are affected by special conditions. It is easy
for somebody helping review the cases to offer changes, exceptions, or
correct misunderstandings. It is easy to appreciate the number of test
cases described with some simple math - and you are worried about that
you can put the number in the text as in the example.

We can re-use named test ideas to make more cases. Imagine we want to
understand how invoice states affect actions on invoices. We can do the same
as we did before:

>Invoice states and Invoice Actions
>-----------------
>Check each combination of Invoice_States with Invoice_Actions (23 cases).
>"Add New" is a nonsense case for "past-due invoice"

In the example above, even though combining both lists would result in 24 cases,
there is an exclusion of one of the combinations ("Add New" and "past-due") which
limits the combinations.

More Ideas for Easier Test Planning
===================
I describe this idea, and more, in my book

<a href="https://www.amazon.com/Writing-Test-Plans-Made-Easy/dp/1478333693">![Writing Test Plans Made Easy](/assets/writingtestplanscover.jpg)</a> 
