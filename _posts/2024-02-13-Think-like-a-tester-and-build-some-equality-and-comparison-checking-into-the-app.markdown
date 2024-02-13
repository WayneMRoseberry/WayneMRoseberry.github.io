Implementing testability via data type method overrides
=======================================================
![A cartoon image of a stick figure tester imagining checking two bunnies for equality and wondering if the bunnies could check themselves](/assets/bunnycomparison.png)

_I have settled on this stick figure character motif since opting to draw my own illustrations
rather than use AI. I produce the image almost as quickly as it takes me to choose from
what AI produces. I get more control this way. The AI images look better, but I wind up with something
uniquely mine._

This is the kind of testing approach where the person coming up with
the test procedure has a big advantage if they are also able to change
the product code. The key part is realizing the advantage even exists.

The deal with data types...
=======================================================
It is common during testing that we work with complex data types.
Maybe it is a user entry in an identity database. Maybe it is
a transaction in a purchase/ordering system. Maybe it is a location
in a game or map.

There is also almost always need for processing, generation, alteration,
persistence, or retrieval of those data objects. A user might need to
change their address. A purchase record might need to be replicated. We will
want to test data handling in a variety of ways.

When we test very often we need to make a comparison. Consider the following example:

```
1. create a new book object
2. add the book to the repository, close the repository
3. open a new instance of the repository
4. search for book object
5. check if it is the same as what was created in step 1
```

The above procedure could be helpful identifying problems
with lost or altered properties on persistence and retrieval.
Helper code in test libraries tend to do things like the following (psuedocode...
I am only pretending we have a real, if humble, `Book` implementation):

```
bool areEqual(Book book1, Book book2)
{
  return book1.Author.Equals(book2.Author) &&
    book1.Title.Equals(book2.Title) &&
    book1.Publisher.Equals(book2.Publisher);
}
```

The challenge of such a method is the idea of what two `Book`s being
equal means lives inside the test code. If we add something like
publishing date, edition, or ISBN number to our notion of a `Book`
we have this disconnected areEqual method that we have to remember
and update.

It is better if `Book` can check itself.
============================================
Imagine instead that methods that provide comparison utility exist on the
Book class. We can implement such methods on the class, check and enforce
their behavior with test methods, and then use those methods for our
other tests.

Some languages and data type libraries already provide convenient default methods
on their data types that allow for override. In .NET, some of my favorites are:
```
// return value indicates relationship between compared items, less then, equal, greater than
public static int Compare (...)

/// returns true if the items compared are the same, false if not
public bool Equals(...)

/// returns a string representation of the object
public string ToString(...)
```
You can override any of the above with your own implementation for a Class. In
addition to reasons this might be useful to meet product requirements, it
can also be very helpful for testing purposes. For example, we could override
`Equals` for our `Book` class as follows:
```
class Book {

  public override bool Equals(object obj)
  {
    Book book = obj as Book;
    if (book == null)
    {
      return false;
    }
    return this.Title.Equals(book.Title) &&
      this.Author.Equals(book.Author) &&
      this.Publisher.Equals(book.Publisher) &&
      this.PublishingDate.Equals(book.PublishingDate)
      this.ISBN.Equals(book.ISBN);
  }
}
```
Now we can craft checks where comparing two books for equivalence is done consistent
with how the product sees that equivalence, as well as using built in checking
conventions such as Assert.AreEqual that use built-in operations like `Equals()`. For example:
```
[TestMethod]
public void RoundTripBook()
{
  Book book1 = new Book("title1","author1","pub1","1234");
  Repository repo1 = new Repository("roundtriprepositorytestfile.db");
  repo.AddBook(book1);
  Repository repo2 = new Repository("roundtriprepositorytestfile.db");
  Book book2 = repo2.RetrieveBookByTitleAndAuthor("title1","author1");
  Assert.AreEqual(book1, book2, "Fail if the round tripped book is not equivalent to the book persisted.");
}
```
If we hadn't overridden `Equals`, then we may have had to use a more awkward check like the following:
```
  Assert.IsTrue(areEqual(book1,book2), "Fail if the round tripped book is not equivalent to the book persisted.");

```
The problem with the above pattern is it relies on an external, extra utility
that the reader has to find and understand. Further, "IsTrue" style checking hides a lot of
information about the nature of the check.

I included `ToString()` in my list of favorite overrides. I use it a lot in
product code, but also find it helpful when writing tests. The ToString() method
is typically called by logging methods, as well as during interpolated string
resolution, making an easy way to dump object contents to a test log for later
investigation. The output of `ToString()` can also be a useful signature or identifier
for an object which can be used for keeping track of an object for later purposes.
For example, imagine a random user generator, and you want to keep track of all the
different users in your test code. Consider the following:
```
List<string> generatedUsers = new List<string>();
for(int i = 0; i < 100; i++)
{
  generatedUsers.Add(UserMaker.MakeNewRandomUser().ToString());
}
```
Some piece of test code might have any number of uses for such a list.

Be sure to check the checkers and helpers
====================================================
This whole approach falls apart if the helper methods on the object
remain unchecked. What if `Equal()` has a bug? What if `ToString()` is
dropping properties? The bug will ripple through the remaining test suite and
may obscure other failures.

 The checking patterns on `Equals()` are usually pretty simple:
 1. create two objects using same properties, fail if `Equals()` returns false
 2. create an object, fetch a copy (and check that `Equals()==true`), and then for each property on the object alter the copy and fail if `Equals()` returns true

It can sometimes be more complex than that, depending on the purpose and
requirements for a given class. Other overridden methods, like `ToString()` can
be more complicated to check, again, depending on the nature of the class.

Now that we have gone over the override...
====================================================
This article has really been about testability, which is a good bridging
topic between the world and activity of testing and that of development.
Everything made should be tested, and it is better if we make them
easier to test. Sometimes simple changes, even down at the lowest
level design decisions, can produce a large advantage later.
