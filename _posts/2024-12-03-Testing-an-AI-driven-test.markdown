Testing an AI that is testing an app
==========================
AI testing bots have bugs and you need to test them. That
sounds daunting and strange and "who tests the tests that
tests the tests etc." and turtles all the way down, but
it isn't really as strange as all that. The AI is only
part of a larger system, and the problems are what you
would expect of almost any system with as many pieces
to it.

Some bugs and investigations that came up
==========================



Raw Notes to Remove later
==========================
This is from my Simulated Self-Host testing. I am capturing ideas below that I can write up a better article from later.

Examples include:
- testing the distribution of events and coverage
- reproducibility
- straying away from the target application
- interacting with the oasys client and shutting the machine off - the bug in simself was not seeing certain properties that allowed it to recognize an app on the exclusion list
- timing of actions and events that drift apart and confuse the AI training
- data inputs that are too random or variant or really not repeatable that the AI cannot effectively train from - e.g. files that happened to exist and randomly opened which are not there later on another machine
- not so much testing, but the difficulty we had giving the data scientist the information to build the Reinforcement Learning models they wanted
- testing whether or not simself was getting lost in the app - this relates to reproducibility

Some themes:
- lots of bugs are not in the AI but in the entire system working together
- AI can only make good predictions if the data offers enough information to base a decision on, if the data is insufficient or misleading the predictions will be bad
- sometimes testing means more analysis of data from something happening live than controlling the test conditions
- some things you really want to do require work or data or behavior that is very hard for you to do
- sometimes the conditions the AI driven behavior is working in is not amenable to doing the activity well - if difficult to do for non-AI will likewise be difficult for the AI

