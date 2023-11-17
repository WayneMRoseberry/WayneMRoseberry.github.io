Sometimes what you need is not that rigorous
============================================
![Picture of a dog altering instructions to put it outside with phrase give dog cooky](/assets/givedogcooky.jpg)

Test data requirements can vary quite a lot, depending on the problem we
are working on. Sometimes the data has to conform to very precise, very
exacting requirements in terms of structure, form, length, content, inner
semantic and referential integrity. Sometimes the data only need meet very
loose, imprecise requirements. ChatGpt and other LLMs tend to produce
output that contains a lot of mistakes in the first case, but work very well
in the latter.

I had a recent example where my test data needs were very imprecise, but
making the same data myself would have been tedious and arduous. I used
ChatGpt to make the test data, and it worked very well for me.

Example Problem: Finding duplicate failures
============================================
The problem I was working on is a <a href="https://github.com/WayneMRoseberry/DupIQ">tool that detects duplicate failures
from test automation</a>. The intent is to allow fuzzy matching of issues
even when the failure message is not exactly identical.

I wanted to test how well it matched. I was not as much focused
on measuring accuracy of matching as I was concerned with bugs in my
code which might cause it to do things like not match at all, or
the opposite, call everything a match against everything else. I wrote
a test method which submits a set of failures to the system and then
checks the rate of matching against a threshold. To do this, I needed
test data where the content was meant to be similar, but not exactly the same.

Getting similar data
============================================
I went to a new site and collected ten different news stories. They are as follows:

>* Authorities have halted oil export\flows from the main pipeline in southern Iraq after\intelligence showed a rebel militia could strike\infrastructure, an oil official said on Saturday.
>* The cost of buying both new and second hand cars fell sharply over the past five years, a new survey has found.
>* Trevor Baylis, the veteran inventor famous for creating the Freeplay clockwork radio, is planning to float his company on the stock market.
>* Private investment firm Carlyle Group,\which has a reputation for making well-timed and occasionally\controversial plays in the defense industry, has quietly placed\its bets on another part of the market.
>* Soaring crude prices plus worries\about the economy and the outlook for earnings are expected to\hang over the stock market next week during the depth of the\summer doldrums.
>* "A Republican running for Congress has received heavy donations from industry because of expectations over posts she may receive if she loses and President Bush wins."
>* "Mariel Zagunis won the first fencing  gold for the United States for 100 years when she beat Xue Tan  of China 15-9 in the inaugural Olympic women's sabre final on  Tuesday."
>* "If there's one thing the Atos Origin SA team understands as lead contractor for the Olympic IT infrastructure"
>* " Oil prices rose on Tuesday on cheery  economic data showing that inflationary pressure was held in  check in July and ahead of weekly inventory due out on  Wednesday forecast to show a draw in crude stocks"
>* "Chinese Internet company Sohu.com\(SOHU.O) on Friday said China Mobile (CHL.N) had suspended Sohu\as a supplier of mobile phone picture services for one year for\unsolicited marketing to some customers."

I then wrote a prompt for ChatGpt that went something like this (I don't have the original prompt anymore):
> For the following text, create ten re-writes which say the same thing but using different words, form, sentence structure, and grammar.

An excerpt of some of the output for one of the stories is below:
>* In the inaugural Olympic women's sabre final on Tuesday, Mariel Zagunis secured a historic victory, defeating China's Xue Tan with a score of 15-9 and clinching the first fencing gold for the United States in a century.
>* After a 100-year gap, Mariel Zagunis brought home the first fencing gold medal for the United States by defeating China's Xue Tan with a score of 15-9 in the Olympic women's sabre final held on Tuesday.
>* Mariel Zagunis achieved a momentous feat on Tuesday by winning the inaugural Olympic women's sabre final, beating China's Xue Tan with a score of 15-9, and securing the first fencing gold for the United States in 100 years.

The full set of output can be found <a href="https://github.com/WayneMRoseberry/DupIQ/blob/master/DupIQ.IssueIdentity.Api.Tests/similarheadlines.txt">here</a>.
The rest of the code for the test project is located in the same directory, so you can see how
it works from there.

Why this worked well for my problem
============================================
I was very happy with how this turned out. It gave me the test method I needed, and
actually served its purpose a couple of times when something I had done to the settings
on the service disabled the matching logic entirely.

Why did this work well?
1. The definition of correct output was imprecise and loose
2. The task was not going to be broken by failure to meet an exacting demand
3. ChatGpt produced the content I wanted much faster than I would have produced it myself
4. The cost of checking the data for correctness was low and easy to do
5. The prompt I gave ChatGpt did not ask it to perform any logic or mathematics or conform to tight requirements

To work well for us, a data generation tool ought to meet the following three
requirements:
* The labor on creation is saved
* the labor on checking the output is low
* the output is useful

If we violate those three points the tool is not helping us very much. If all three are satisfied then the tool
served us well.

What about the counter examples and "Better Prompt Engineering"?
============================================
For sake of this story, I don't care. ChatGpt worked very well for me in this
case, and I thought it would be useful for others to describe it, to understand
what I did and why it worked well.

Searching for the prompt gone wrong
-------------------------------------------
For sake of the larger picture, I believe we have a lot to learn from
the "this did not work at all" examples, and I believe they are 
informative in helping us understand where the rough edges are on this
very powerful toolset. It is not alway easy to anticipate what generative
AI tools are going to do, and learning how to use them involves as much
understanding about what goes wrong as it does what will go well.

Usually when a prompt goes wrong, it does so in violation of one or more of the three ways tools
help us. The cost of creation was higher, the cost of checking the output was higher, or
the output itself was not useful.

Searching for the better prompt
-------------------------------------------
I see prompt engineering as a frontier of explorating the new language
for programming generative AI devices. Sometimes it feels like voodoo,
but some of what people learn and either teach or build for others
is impressive and productive.

The better prompts work for us when the three ways tools help us are preserved.
If the cost of creation is lower, the cost of checking remains low, and
the output remains useful, then we can say "indeed, we have found the better prompt."

More Ideas for Easier Test Planning
===================
I wrote the book decades before we even had generative AI to use, so maybe it needs
an update, but you will find more ideas about test planning and test data in it.

<a href="https://www.amazon.com/Writing-Test-Plans-Made-Easy/dp/1478333693">![Writing Test Plans Made Easy](/assets/writingtestplanscover.jpg)</a> 
