If you find this test plan acceptable, you need expert help,
===========================================================
![An image of a test plan commonly posted as an example on LinkedIn - a link to pdf will follow later](/assets/dreadfultestplan.jpg)

- a thorough review and critique of that test plan template for sign-in/sign-up that
apears on LinkedIn all the time

In this article, I am going to behave as if I were in a leadership position
in a company where somebody handed me the test plan displayed
at the top of this article. I am going to review the document
as if the company I work for was delivering the product
referred to in this plan.

An version of the same <a href="/assets/dreadfultestplan.pdf">test plan in  PDF format
can be found in this link</a>.

Test Plan Review, step by step
============================================================
__Nit: Title block should have the name of the feature.__

This is not a big deal, but when someone is reading lots of material,
it helps when the title of the document says what it is about.

__Features to be Tested__, and __Features not to be Tested__

I am very worried about this short list of features.
- __Why not create multiple accounts__? Isn't that what sign-up is all about? Doesn't
signing up new accounts drive the primary business use case? Isn't signing in and
out as different users what distinguishes things like access to data, information storage,
privilege levels? Creating multiple accounts strikes me as a primary use case
for sign-up, as is the case for sign-in of multiple accounts.
- __Why not edit account information__? This at least deserves explantion,
because sign-up is on part of the account information path, it is usually the way
the first, default information gets to the account. Account information is also
where some systems store sign-in information, such as user name, and usually password.
There are very likely important sign-in and sign-up user flows around the edit account experience that
should be checked.
