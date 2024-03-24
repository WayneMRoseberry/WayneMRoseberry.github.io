In this article I am going to describe the basic structure and functionality of the
test automation system that Microsoft Office uses. It is a large system, servicing a
team of about 7000 engineers across multiple platforms and application types. The automation system
is over twenty years old (today is March 24, 2024... for future readers' sake), 
predating many of the familiar frameworks and solutions in market. It is an interesting
system to describe not so much because of its scale and complexity, but because
it covers a lot of automation framework service and capability archetypes. My interest
here is to describe those archetypes as a way to talk about any automation system.

I am not going to use pseudonyms for the names of services and components in the
system. I am going to describe purpose and role rather than exact functional
behavior. This piece is not an expose, not a "behind the curtain" account. It is
instead an illustration of the functionality, parts and pieces of a large scale
test automation system.

My Background with the Office test automation system
====================================================
I worked in the Office team as a SDET, SDET Lead and Software Engineer from 2001 when (when the SharePoint Portal
team moved into the Office organization) until 2023. I used, built, and investigated automated
tests for various SharePoint properties on the Office test automation system for about
twelve years until I joined the Office Engineering team in 2012 and started working on the automation
system itself. From then until 2023 I worked and spearheaded various efforts to use the
automation system in new and different ways.

Office Test Automation System - some numbers and process background
==================================================
All of the numbers are by the time I write this more than a year old. Some of
them are even older, and are accurate within orders of magnitude. When in doubt, assume
all the real numbers are larger than anything I share here.

The Office organization has roughly 7000 employees worldwide. It is hard
to say exactly how many are doing engineering work, but one time I did a count of the
number of distinct email addresses were associated with launching automation jobs per
month and I got something between 2000 and 3000, so my rule of thumb was that we had
2500+ engineers directly using the automation system. The Office organization divides up
into individual product groups that align mostly with the apps (Word, PowerPoint, Excel, Access, Publisher,
OneNote, Outlook, Teams, and more...) and then a set of teams that write shared technology
(Graphics, FileIO, menus and command bars, performance, telemetry and event infrastructure, etc.) There are multiple
dozens of these teams - each having their own product team branches in the source repository, and
each of those were typically associated with a set of scheduled or event drive automation job
launchers running test automation jobs in product team branches.

Office produces an official integrated build every day, and a set of official
runs are associated with that build. Some of the runs gate release of the daily full for dogfood usage,
some of the runs serve as a lockdown gate, where failure of a set of tests means a given
team cannot integrate into the main branch until the failures are resolved. There are also
numerous less frequent larger scale runs of tests outside those used to gate build release,
as well as "reliability runs" that capture intermitten failure data from the gating tests.

As of 2022, Microsoft Office was regularly running 10 million tests per day across a bank of upper
tens of thousands of machines.  That 10 million number needs some explaining, which will come
later when describe what is meant by a test in the Office automation system.

The Office Test Automation system was built when Microsoft still had both testers
and developers as separate disciplines. The system designers were SDETs, and its
original use cases were from a tester first approach. After 2014, when the roles were
unified, there had been some shifting toward a more developer focused set of use cases,
although most of the features and behaviors still feel as if they were meant for a person
whose primary focus is testing.

The parts and pieces of the Office Test Autoamtion System
========================================================
There is probably something missing in my description, but most of the system is covered by
in the list of the following services and components:
- Case management service and database
- Job launch
- Job policy enforcement
- Resource and job scheduling
- Test machine pool(s)
- Machine monitoring and control
- Result collection
- Job reporting storage and workflow
- Test execution infrastructure
- Test sources
- Test collateral support

Case management service and database
-------------------------------------------------------
The Office Test Automation system presents a WEB UI for creating, editing,
managing, and search for automated tests. A test in this system is
called a "scenario", which was meant to convey larger end to end testing
support than is usually imagined in developer tests. A "scenario" consists
of three parts: 1) an entry in the system database, 2) a scenario file (XML format) describing
how to launch the test binaries, 3) the test binaries in the source
repository and build system. The datbase associates those three entities together.
For example, the same test binaries, with the same test methods, could
be used to define multiple tests, scenarios, by using different scenario
files and different entries in the database.

The scenario is the primary unit of execution in the Office Test Automation
system. Above, when I said the system ran 10 million tests per day, that
is a count of how many times a scenario was executed.

Scenarios are also used for machine preparation, something called a "configuration
scenario" in the system. During a run, a given instance of a scenario running is always
paired with a configuration that bundled a set of configuration scenarios that ran
prior to the test itself. These configuration scenarios do everything from pave and
re-image the machine to laying down different security settings, browser configurations,
OS version patches or whatever else is needed.

Job launch and execution policy enforcement
-------------------------------------------------------
Engineers can launch a job from the case management database, but that is infrequent. Most of
the time, engineers launch jobs from a machine with a development environment and repository
enlistment. There is a local application on all developer environments in Office (built entirely
out of Perl... I was traumatized every time I had to touch that code) can launch a job
against the automation system.

The engineer specifies what they want the system to do. Sometimes they indicate an "intent", for example
when they intend to submit code to the repository, from which the tool scans their changes and determines which
scenarios (based on a mapping that associates directory paths with sets of scenarios) to run on
which configurations. The tool packages up a job definition to upload to a service which then schedules the job.
Part of that definition tells the system how to find the binaries to test (Office uses
Azure DevOps, and developer builds are placed on drop points). The engineer is given a job
Id and URL they can monitor to check on the status of their job.

Resource and job scheduling
-------------------------------------------------------



Test machine pool(s)
-------------------------------------------------------

Machine monitoring and control
-------------------------------------------------------

Result collection
-------------------------------------------------------

Job reporting storage and workflow
-------------------------------------------------------

Test execution infrastructure
-------------------------------------------------------

Test sources
-------------------------------------------------------

Test collateral support
-------------------------------------------------------
