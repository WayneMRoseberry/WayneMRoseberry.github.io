![King Kong on a motorcyle jumping the border to Switzerland like Steve McQueen](/assets/greatescape.jpeg)

When I imagine test cases, what I am primarily interested in is what happens in the case when a given condition occurs. It is a question, a pondering, a concern there may be something to learn in that particular case which presents risk to the product, system, business, or customer.
I like to do as much up-front imagination and creation of possible cases as I spontaneously realize them as I test. There are several ways of doing that. The one I will talk about here I call the ESC method. For what it’s worth, the age of that label is less than an hour old, I just hadn’t been so pretentious as to put a label on it until just now. The label stands for:
- **E**numeration
- **S**alience
- **C**ustomize

Enumeration
=================
The first part of the great ESC ape is the act of making lists of things. Enumeration. It sounds easy, but it isn’t always. For simplicity, I am going to talk about enumerating two types of things: generic test cases, and product features.
Generic Test Cases and the Great ESC-ape
Generic test cases are those kinds of cases which are imagined independent of any specific system. Consider this sample of actions to try:
-          Leave entry blank and submit
-          Paste more than allowed characters into text field and submit
-          Input the string “><script>Alert(‘hacked’);</script><”
-          Set clock to exactly 12:00:00 midnight
-          Hold down the ENTER for an extended period without letting it up
-          Abort process mid-sequence
-          Attempt to access and use control via TAB sequence on keyboard

Imagine that for each action above there are one or more things to check after the action. There are books and articles which catalog such lists. Domain expertise in performance, security, accessibility, privacy compliance, platform compatibility and a variety of other topics usually have lists of their own the bring forward when asked to guide, consult, or test regarding a problem.
These generic lists are an enumeration, usually ordered by topic.

ESC Aping Product Features
-----------------
The point of our enumeration is to combine lists together, so we need at least one more list. That list is generally why we are involved in the first place, and that is a list of product features.
In some ways, enumerating product features is easy. They are usually written in a business plan or design document somewhere. In other ways, they are not. If your product home page has a text box, and a “Submit” button on it, are those the only features on the page? What if there is static text content and a picture, should those be features too, and are they one feature for the whole thing, or are the text and the picture distinct features with distinct test problems? If there is an entry field on a menu, and that entry field has a drop-down button, and if the contents of that field change based on different product modes, is that one feature for the control, or are there multiple features between just entering data, dropping behavior, and how it changes its content based on integration with other product features? There is quite a bit that can go into deciding where you will split the line on what is a distinct feature or not.
The reason why becomes apparent when we begin combinations. Let’s pretend we have a very small application which we have decided to analyze as ten features. Let’s also pretend that we have a humble list of 100 generic test cases. To generate our test cases, we combine each of the features with each of the generic cases. We now have 1,000 test cases.
We find ourselves torn at this point between two anxieties. The first is contemplation of there being that many cases. A number like 1,000 is a bit daunting, and that is on the small end of the scale. We worry we are never going to get to running them, preparing for them, even thinking about them. There are just too many. On the opposite, we worry we might not be thinking of enough. Maybe we missed a feature. Maybe we are lumping too many features together in a way that will cause us to miss checking one of the generic cases against a feature in an important way. These two anxieties pull at each other in a frustrating way. Trying to fix either one just makes the other worse,
Fortunately for us, blind combination of generic cases against feature lists produces a lot of unnecessary cases. Which lead us to the next part of our acronym.

Salience
=================
We want to look at the combinations and decide which are salient. Which features actually make sense with which generic cases? While that might sound daunting against a list of thousands of combinations, there are some fast shortcuts.

The Great Categorical ESC Ape
-----------------
Rather than look at features one at a time, and generic test cases one at a time, we can start by looking at them by category. There are numerous categories, and for this exercise the chunkier and bigger the category, the better. Does the feature take user input or not? Does the feature only apply to certain configurations? Is the feature only available in certain modes? Is the feature entirely new code, or is it built from existing components? Categories serve as a quick heuristic that allow us to select or eliminate cases.
The cases likewise fall into broad categories. Almost all of them are topical in some manner, relating to user input, localization, accessibility, platform compliance, regulatory compliance, reliability, performance, or any of a number of other testing categories. It is often the case that a category may be selected, or eliminated, based on whatever category we applied for a feature.
It is sometimes useful to write down which features we are mapping to which categories. For example, imagine this mapping for an online project scheduling application:

- Date-Time Entry Fields: project due date, project start date, resource start date
- Accessibility, Data Validation, Localization, International Sufficiency, Platform Compatibility
- Load based operations: Online project CRUD, Project navigation and viewing, Schedule update
- Performance, Scale, Reliability, Stress, Security

This kind of list says a lot without having to infer a lot of details. It is the kind of thing we can put together very quickly and use to get critique from co-workers. Perhaps a fellow tester or developer will point out you are overlooking data validation on the load-based operations – we wouldn’t want a process to go down because something about the data caused it to crash or start processing an infinite loop. In addition to being a convenient way to review our early thoughts, this kind of salience analysis at the large, categorical level is our first big reduction in the size of our problem.

ESC Aping the Details
-----------------
Our categories of test cases are usually hierarchical. For example, International Sufficiency covers such issues as character sets, dates and times, edit control behaviors, geo-politically sensitive content and a host of other issues. Within each of those may be another level of hierarchy where you get to very specific tests. This affords opportunities such as selecting only the currency cases from the international sufficiency category if a given control only accepts currency. Another large cut of cases happens. We can express these choices in ways similar to above – list the labels and the groups they apply to.
Note too that at some point the formality of listing these cases can be implied. Convenient phrases like “the relevant cases from this set...” drop a big heavy hint like “I am only picking the date and time-based cases for the calendar control from this group – not the hundreds of others...”
Eventually we get to leaf nodes. Hopefully by this point, the big sweeping eliminations have culled the set to something more manageable. But even so, it is easy to get oneself into the task of enumerating hundreds, sometimes more, cases even after all the reduction.
At this point, I usually start to look for ways to convey what the final combination is going to be without having to list them all. Maybe the list of cases I am NOT going to do is very short and I can just state them. Maybe there is some rule I can use for elimination that I can describe in a short statement such as “Eliminating all the free form data entry cases because the control does not permit it...”

Custom ESC Ape
=================
Once we have enumerated our cases, established which combinations are salient, we almost always have to customize our generic cases to match the features.
One of my favorite examples is security testing via data inputs. There is a popular security testing joke where various things like license plates, pets, children, or anything else that might it’s way into a database query are named “; drop table ”. It’s funny, and everybody gets a chuckle, but everybody who has really done security testing knows that that particular generic security test archetype almost always needs to be changed to work. The bug may really be there, but as a tester (and a hacker in this case) you wind up changing it based on what you discover about the actual feature under test. I remember one time doing penetration testing on a set of web pages where the page would only be processed in certain fields had a very specific set of values in them. Security tools that were not page inspection based, that performed a direct HTTP Post missed that requirement, and all their hack attempts were entirely nullified by one switch statement at the top of the page. Modify the payload to contain one of the required values in that one field, and now you were finally testing.
Almost every generic case, when combined with a product feature, is going to have to adapt in some way regarding steps, pre-conditions, data, validation mechanisms, expected outcome.

ESC Ape Sounds Like a Big Thing
=================
It is when we talk about it formally like I did here. If one follows the procedure in a rigid way like I describe above, they wind up creating a lot of collateral to describe what they intend to or have already done. That may be the right thing to do in some cases, but it is a lot of work.
Lots of people have far more success most of the time adopting very shorthand ways of capturing their idea. Sometimes people describe as far as the categories and leave the rest implied.
We also do pretty well holding mappings in our heads. I took a roundabout way to describe this process, but in some ways, I am just applying a deconstructive model to something people do mentally without necessarily taking it apart in this way. Very often, when confronting a feature, we do the enumeration, salience and customizing on the spot, almost as if in one mental leap.

The Final ESC Ape
=================
Someway through the middle of this article I had the thought that I was describing something too obvious, too simple and easy to merit writing. But I also remember different times as a tester where I was overwhelmed by the size of a problem, and I realized that the struggles were all about modelling the enumerations, managing the salience decisions. Sometimes it is useful to talk about our mental activities as parts and pieces of a process, even if the end result is something that sounds very straightforward.
I also wrote this article while thinking about another article I had read describing (very early, very experimental) attempts at AI test case generation. I had already shared my feedback with the author of that article what I thought was interesting, and what I thought was not working well. It was while thinking about that I decided I wanted to deconstruct what I believe I do when coming up with my own test cases, particularly for the similar kind of generic test case combinatorics described in the AI article. That deconstruction helps me frame my own thoughts on the AI case generation and organize the critique. If I share those thoughts, I will do so in another venue.

