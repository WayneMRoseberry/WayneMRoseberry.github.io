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
- Straying away from the target application

We wanted the test agents to stay inside Word, PowerPoint,
Excel, InfoPath, and Publisher. There were configuration
settings that told the agent which process names to keep their
focus on.The agents needed to take some actions outside of the
target application to manage the environment space.

Sometimes the agents would do far more outside the target
application, opening other applications and windows and
tools that did not relate at all to the target application
nor to controlling the environment and configuration.

This behavior was not caused by the AI, as it came
entirely from the randomized monkey test behaviors of the
agent, but it did affect the AI training. All actions, and
the resulting UI state change were fed back to the service
and used during the backend training to predict
sequences that achieved various reward states. Imagine, then
the following sequence:

1. Word, click on 'Insert' tab
2. Switch to Explorer
3. In Explorer, open 'Documents' link
4. Switch back to WOrd
5. Select 'Image'
> Office.Graphics.Image.InsertImage event captured as reward from telemetry

The backend is going to see that sequence and weigh it with others
that preceded occurence of the Office.Graphics.Image.InsertImage reward
in order to predict sequences which lead to that event. Chances
are the specific switching out of app, into Explorer and the Documents directory,
and then back Word followed by 'Insert Image' will not happen
enough for the service to predict that doing it again will
cause the Office.Graphics.Image.InsertImage event to occur, but
it is still an event we don't want in the session at all.

Testing for this behavior is mostly in the analysis. We didn't
do anything more to control the run state than launch more runs. We were
able to query with Kusto (Azure Data Explorer) to see all the
actions that agents took during a run and which applications/processes
the actions were acting against. From that, we would track the
percentage of actions against the target application versus those
not. There were also a short list of applications the agents used
for controlling settings, and we considered switching in and out
of them acceptable. If we saw any application outside that list, we
were concerned, but only raised an issue if it got above a certain
threshold.

- interacting with the test framework and shutting it off

The test machines had a piece of I am going to refer to as "Test Framework Client", or TFClient
for short. TFClient was responsible for launching test modules, monitoring their
execution, collecting results when done, but most importantly notifying the larger
test system if the machine was available, busy, whether the test was executing
or completed. When a test machine is done and results have been collected,
TFClient notifies tells the Test Framework controller that the machine is
ready to be returned to the pool of available machines. If the TFClient
ever aborts, this machine gets "stuck", with the Test Framework controller
unable to send commands, check its status, and return it to the
pool. A on-call member of the automation system engineer team is notified
of stuck machines in the queue and has to manually release it and return
it to the pool.

TFClient presents a user interface that engineers use when
they connect to test machines so they can step through test
execution.

Sometimes the agents, when monitoring the list of running processes
would see TFClient in the list, switch focus to it, and start interacting
with its UI. Sometimes the test agent would kill the TFClient process.

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

