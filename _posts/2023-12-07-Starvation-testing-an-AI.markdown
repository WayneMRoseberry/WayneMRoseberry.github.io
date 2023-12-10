An AI can only use the data it has
==================================
![A cartoon of a robot pointing to a board with lots of diagrams on it while a human word-bubbles nonsense at it](/assets/anaicanonlyusethedataithas.jpg)

A recent <a href="https://www.linkedin.com/posts/michael-bolton-08847_chatgpt-and-quick-intelligence-tests-activity-7138364892926853121-6SX2?utm_source=share&utm_medium=member_desktop">
example from Michael Bolton</a> prompted me
to consider some testing problems surrounding LLMs,
especially how to easily check if the response to their
prompts are introducing errors when the prompt
response is so fuzzy.

I found that starving the AI of data
at critical points created an opportunity
to learn something otherwise difficult to know.

Testing AI grammatic diagramming of test passages
==================================
I had asked ChatGpt to diagram a paragraph
I had extracted from Michael Bolton's article.
The prompt, and response, follow below.

Prompt:
>A paragraph will be presented below. In the response diagram the sentences in the paragraph, breaking each into their grammatical parts. The response should be in json format, with one json object per sentence, and one element per each grammatical component per sentence.
>
>Another form of arrogance is not admitting when you’re stumped. A socially competent human who couldn’t figure out an answer to this question would simply give up, but I can click on the “regenerate” button all day, and ChatGPT will answer. That points to another syndrome, incuriosity: a socially competent human would eventually say “Hey… why do you keep asking me the same danged question?”

Response:
```
[
  {
    "sentence": "Another form of arrogance is not admitting when you’re stumped.",
    "components": [
      {"subject": "Another form of arrogance"},
      {"verb": "is"},
      {"negation": "not"},
      {"gerund_phrase": "admitting when you’re stumped"}
    ]
  },
  {
    "sentence": "A socially competent human who couldn’t figure out an answer to this question would simply give up, but I can click on the “regenerate” button all day, and ChatGPT will answer.",
    "components": [
      {"subject": "A socially competent human"},
      {"relative_clause": "who couldn’t figure out an answer to this question"},
      {"conditional_clause": "would simply give up"},
      {"conjunction": "but"},
      {"subject": "I"},
      {"verb": "can click"},
      {"prepositional_phrase": "on the “regenerate” button all day"},
      {"conjunction": "and"},
      {"subject": "ChatGPT"},
      {"verb": "will answer"}
    ]
  },
  {
    "sentence": "That points to another syndrome, incuriosity: a socially competent human would eventually say “Hey… why do you keep asking me the same danged question?”",
    "components": [
      {"demonstrative": "That"},
      {"verb": "points to"},
      {"subject": "another syndrome, incuriosity"},
      {"colon": ":"},
      {"subject": "a socially competent human"},
      {"conditional_clause": "would eventually say “Hey… why do you keep asking me the same danged question?”"}
    ]
  }
]
```

I thought the above output was kind of cool, and
I wondered about ways to use it. Maybe an application wants
to use the grammatical breakdown as part of a parser. Maybe
some workflow is trying to infer requests from users. Maybe
someone wants to build an application that visually diagrams
sentences. Any of these uses or more could find a great deal
of utility from a json-based diagramming of paragraphs and
sentences.

The challenge is testing it. 
====================================
Consider:
- is the grammatical analysis and output correct?
- Are there mistakes in terms of assignment, ommission, or transformation of the words and terms?
- is the schema consistent with how the tool will read it, would the LLM produce the same schema every time, or change it

Flippy-floppy output format and schema
------------------------------------
We can test schematic conformance by deserializing the json
and looking at the errors. Testing grammatical analysis, meanwhile, is
a challenge. The schema/format problem introduces a wrinkle
because having a test tool evaluate the output is not going to
work unless that tool is able to reliably handle the schema. Tools
are hard to write when their input data format
constantly changes. We want something that adapts well to
unpredictable inputs.

Which is what LLMs do.

Narrow the testing scope and use simple oracles
------------------------------------
I was curious about testing the grammatical
analysis for word ommissions, additions and transformations.
For this, the oracle should be simple.

1. Give ChatGpt a prompt which tells it to diagram passage A in json format
2. Feed json response to tool that builds passage B from the json diagram
3. Compare A to B, any differences indicate a problem


The twist is the json schema kept changing. For
example, in two different  exercises,
ChatGpt used different methods to indicate
multi-word labels; catenation of words together, such as
"relative_clause" and catentating them with an underscore, e.g.
"relative_clause." These and other variations would make building
a reliable tool challenging.

Could I use ChatGpt to handle that difference?

Starve the AI to force its hand and have it help with the testing
------------------------------------
ChatGpt is not thrown off as easily by inconsistent inputs. One of its
core capabilities is fuzzy matching of similar content
and concepts. Could I get ChatGpt to play the job of
tool, generating a passage based on a json formatted
grammatic diagram, even if that format is unpredictable?

To do this, I have to starve it of data.

The method goes as follows:
1. Start with a new session to establish a clean data state
2. Build a prompt that instructs ChatGpt to derive a passage C from a json formatted grammatic diagram.
3. Include the json B derived from input passage A from the prior diagramming session in the prompt
4. Extract response C and string compare against A

The important part is that the second new session contains
none of the prompt input from the original session. The only
information that the second session has is the json output, B.
This means that the second session has no information about
whether any of the words in B were changed, added, or removed from
A. All the second session has is the content in B itself.
In the second session, ChatGpt has been "starved" of the data
necessary to cheat. This creates a higher probability
that any changes made from A to B will be propagate, or even
get worse, going from B to C. In the end we have a comparison
against A to C which will help us spot problems.

Note my use of the phrase "higher probability."

Note that I am talking about spotting problems, not establishing correctness.

These are fundamental testing points, but people forget them
and get distracted. A testing method almost never catches
everything, sometimes missing things, but so long
as it finds something useful it worked. The advantage of this
approach is that it should be something we could try
at scale. We could apply the same method to a single
passage, or a million.

Okay, but did it work when you tried it?
========================================
Oh, you still here?

The answer is yes. The method found a bug in the sentence diagramming
app I built.

Here is what I got:

>Given the following json that describes a sentence diagrammed into its grammatical components, respond with the sentence it diagrams.
>
>[
>   {
>    "sentence": "Another form of arrogance is not admitting when you’re stumped.",
>    "components": [
>      {"subject": "Another form of arrogance"},
>      {"verb": "is"},
>      {"negation": "not"},
>      {"gerund_phrase": "admitting when you’re stumped"}
>    ]
>  },
>  {
>    "sentence": "A socially competent human who couldn’t figure out an answer to this question would simply give up, but I can click on the “regenerate” button all day, and ChatGPT will answer.",
>    "components": [
>      {"subject": "A socially competent human"},
>      {"relative_clause": "who couldn’t figure out an answer to this question"},
>      {"conditional_clause": "would simply give up"},
>      {"conjunction": "but"},
>      {"subject": "I"},
>      {"verb": "can click"},
>      {"prepositional_phrase": "on the “regenerate” button all day"},
>      {"conjunction": "and"},
>      {"subject": "ChatGPT"},
>      {"verb": "will answer"}
>    ]
>  },
>  {
>    "sentence": "That points to another syndrome, incuriosity: a socially competent human would eventually say “Hey… why do you keep asking me the same danged question?”",
>    "components": [
>      {"demonstrative": "That"},
>      {"verb": "points to"},
>      {"subject": "another syndrome, incuriosity"},
>      {"colon": ":"},
>      {"subject": "a socially competent human"},
>      {"conditional_clause": "would eventually say “Hey… why do you keep asking me the same danged question?”"}
>    ]
>  }
>]
>
>ChatGPT
>The first sentence: "Another form of arrogance is not admitting when you’re stumped."
>
>The second sentence: "A socially competent human who couldn’t figure out an answer to this question would simply give up, but I can click on the “regenerate” button all day, and ChatGPT will answer."
>
>The third sentence: "That points to another syndrome, incuriosity: a socially competent human would eventually say “Hey… why do you keep asking me the same danged question?”"

The response structured the output instead of making it a continguous passage.
This is a bug in the testing tool approach, but may also indicate a bug
in the original grammatic diagramming. Nowhere in the original json
did the schema preserve the paragraph structure. When asked to
construct a passage from the json, it has no way of knowing if the text is
in multiple or a single paragraph. The grammatic diagramming app
loses information when it diagrams.

Where is the bug?
-----------------------------------
It is so tempting to say the bug is in ChatGpt. But this application
consists of prompt, LLM, response. Part of the business logic
lives in the prompt itself. Presenting instructions that guide the LLM
in output generation may be as much culpable in failing to do a
proper job of it as the LLM.

As a tester, it doesn't matter to me. All I know is I figured out
an interesting way to test this application.

In Summary - Exploit the system as a way to test
========================================
In this case, I am borrowing a part of the same system
under test to help me with the test. Actually, it is more
like I am using the same supporing libraries that the
system I am testing uses - like turning to Math.Power to test
an application that also uses the Math librariers.

It is important, though, that we use methods which make
failures stand out. In this case, I am taking
steps that will preserve or amplify data alterations until the
point of comparison. The "data starvation" technique, removing
information the tool might have used to obscure the transformations,
is part of that method.

P.S. If you think about it...
----------------------------------------
I will leave this to the readers, but there are ways the LLM
could still cheat, even through the two-session data starvation
techniques I employ. Have some fun, imagine what those might
be, and consider how one might test for them as well.

This is about creativity and imagination.

ERRATA - The Second Pass Prompt Was Wrong
==========================================
A reader pointed out that in the second prompt above the LLM is
told that the json describes a sentence. It really describes a
paragraph, thus the prompt was misleading.

That is a fair accusation, but it is worth noting that even
though the second prompt was misleading, the json created by
the first session still stripped any inidication of a paragraph
relationship in the passage of text. It represents the passage as
an array of sentences, no indication that the sentences in the
array go together. It is my contention that this means the
whole application (remember, the application is Prompt+LLM, not
the LLM alone) has a bug causing it to lose paragraph
information when it creates the json.

> Given the following json that describes a passage of text diagrammed into its grammatical components, respond with the text it diagrams.
> 
>[ { “sentence”: “Another form of arrogance is not admitting when you’re stumped.”, “components”: [ {“subject”: “Another form of arrogance”}, {“verb”: “is”}, {“negation”: “not”}, {“gerund_phrase”: “admitting when you’re stumped”} ] }, { “sentence”: “A socially competent human who couldn’t figure out an answer to this question would simply give up, but I can click on the “regenerate” button all day, and ChatGPT will answer.”, “components”: [ {“subject”: “A socially competent human”}, {“relative_clause”: “who couldn’t figure out an answer to this question”}, {“conditional_clause”: “would simply give up”}, {“conjunction”: “but”}, {“subject”: “I”}, {“verb”: “can click”}, {“prepositional_phrase”: “on the “regenerate” button all day”}, {“conjunction”: “and”}, {“subject”: “ChatGPT”}, {“verb”: “will answer”} ] }, { “sentence”: “That points to another syndrome, incuriosity: a socially competent human would eventually say “Hey… why do you keep asking me the same danged question?””, “components”: [ {“demonstrative”: “That”}, {“verb”: “points to”}, {“subject”: “another syndrome, incuriosity”}, {“colon”: “:”}, {“subject”: “a socially competent human”}, {“conditional_clause”: “would eventually say “Hey… why do you keep asking me the same danged question?””} ] } ]
>
> __ChatGPT__
> 
>Here is the text that corresponds to the given JSON:
>
>1. Another form of arrogance is not admitting when you’re stumped.
>2. A socially competent human who couldn’t figure out an answer to this question would simply give up, but I can click on the “regenerate” button all day, and ChatGPT will answer.
>3. That points to another syndrome, incuriosity: a socially competent human would eventually say “Hey… why do you keep asking me the same danged question?”

The test methodology still seems to work. Rather than represent the text passage as a paragraph
ChatGpt presented it as a set of distinct, numbered, sentences.
