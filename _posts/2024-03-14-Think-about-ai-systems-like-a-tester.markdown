
No matter what a system is, I like to diagram my understanding of it, breaking it into
the parts and pieces and how they relate to each other. I always find that doing this
makes it easier to understand where and how I want to focus my testing. AI systems
are no different. Before we break them apart, we are often bewildered by what seems
highly unpredictable behavior from the AI models. After we break them apart in a way
that focuses how all the pieces fit together to solve the problem, the behaviors of
the individual parts, including the AI models, start to show relationships that hint
at how we want to focus on the testing.

I believe that most AI and ML based systems are modifications to a common pattern.
I describe that pattern in this article, and offer an example of it. The drawing below
illustrates both. I will describe it in more detail in the text that follows.
![A diagram that models a system using AI to solve a problem, with a generic model at top and an email chatbot below](/assets/AIsystemmodel_emailchatbotexample.png)

Before proceeding further, let's talk about AI and ML models
==============================================
It is important in this analysis to understand some fundamental
differences between AI and ML models compared to code written explicitly.
These differences are part of what makes test analysis of AI systems so daunting,
but are make understanding how to approach the testing easier when we model
the system as separate parts.

AI Models are statistical, most traditional code is not
----------------------------------------------
We are used to thinking of code as solving problems deterministically. There is that joke which goes
that a programmer's spouse said to them "Go to the grocery store and buy a cartoon of milk. If they
have eggs, get 12" and when the programmer returned home with 12 cartons of milk in response to the
spouse's inquiry why they bought that much milk, the programmer's response was "They had eggs."

This joke demonstrates something about conventional coding. It is deterministic, and blindly
logical by formal rules. One can even imagine the code for the joke:
```
milk.cartons.buy(store.hasEggs() ? 12 : 1);
```

Because of the deterministic nature of the sort of code we are used to, we can even imagine
a simple fix:
```
if (store.hasEggs()) {
    eggs.buy(12); }
if (store.hasMilk()) {
    milk.carton.buy(1); }
```

We find the broken case, change the logic, and the problem is addressed. We can accurately
predict the outcome of the above code based on a truth table for both `hasEggs()` and `hasMilk()`
(_Actually, not really... what if the store has eggs, but less than 12? There is not enough information
in the code above for us to know what happens. But whatever it is, it is very likely deterministic and
the same for the same set of input values, we just didn't account for a value._)

AI and ML models don't usually work that way. Rather than having a deterministic relationship between
a known set of inputs and the "correct" output, AI and ML models create outputs that on aggregate, over a large
set, are correct a certain percentage of the time, and the models adjusted during training until that percentage
hits an amount we find acceptable.

Imagine, for example, an AI model that is trained to purchase groceries based on the last purchases,
the expiration date of what was purchased, and the current pantry/freezer/refrigerator contents. Sample data
of human purchases correlating with those same inputs are fed to the model, and it is trained to 95% of the time
buy the same items and number of items as the human 95% of the time. It will sometimes be wrong, perhaps not accounting
for grocery shopping preceeding vacations (don't buy perishables before leaving) or one off events (high school football
team party at the house for all day barbecue, and there are heavy milk drinkers on the team), but it is usually correct.

The statistical nature of ML and AI models are a critical part of their behavior. We use them for problems that
are fuzzy in nature because our deterministic, rule oriented approaches to solving problems do not adapt well
to large numbers of inputs mixed with hidden variables. We can decide for ourselves where to draw the best
fit predictions, but we have found that the training algorithms for AI and ML do the job faster, and better, than
humans tend to for certain problems.

