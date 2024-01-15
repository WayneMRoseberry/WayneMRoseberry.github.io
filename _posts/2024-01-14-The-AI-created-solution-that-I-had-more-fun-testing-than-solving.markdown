I had more fun testing this horseracing problem than solving it
===============================================================
![A drawing depicting a bunch of horses in standing in front of a whiteboard with code on it](/assets/horseswhiteboard.jpg)
_The prompt for this image was "image of 25 horses working on a coding problem on a whiteboard."
The fact that there are only 22 horses in the picture is in no way sufficient to ruin the
fact that the horse in front is standing on a ladder to access the whiteboard. I will take
innacurate any day if it comes with that kind of hilarious. Even funnier, all the computers in the
room are displaying the blue screen of death._ 

I don't mind a coding challenge, but sometimes I take longer than
I would like to solve a problem. I do like testing the
problems, and one that came up today had a nice, tiny little
test method format which I enjoyed more than the problem itself.
This is not, by far, an impressive bit of code that I wrote
for testing something. But sometimes the small things are fun
to share and talk about.

This one started as a post about LLMs solving math and
coding problems. The <a href="https://www.linkedin.com/posts/alessandro-soldati-gold-startup_chatgpt-activity-7149087697108697088-8yZC?utm_source=share&utm_medium=member_desktop">
original post</a> proposed this question:

>- You have 25 horses.
> 
>- 5 can race at a time.
> 
>- There are no stopwatches. 
>
> What is the minimum number of races required to identify the 1st, 2nd, and 3rd fastest horses in the entire group?

I and others engaged in a back and forth 
over how the different LLMs performed on this task.
My observation was that ChatGpt gave a different
answer every time I asked. Someone else noted that Bard
got the answer correct every time.

I will provide the answer at the end, but
I wasn't convinced that the answer Bard
gave was correct. I was pretty sure I was wrong.

My failed quest to prove Bard wrong
===================================
I was pretty sure I could demonstrate what was wrong by
testing the algorithm, but I had to write it first.

Implementing the solution
-----------------------------------
I wrote code that imitated Bard's algorithm.
The algorithm offered by Bard was in plain
English (it was not asked to produce
code).

Turns out the plain English version of the algorithm
was actually incorrect. It was internally
inconsistent with itself (I will include the
full text at the end). I pointed this out, and
the person I was conversing with offered their
own clarification, which I accepted as what Bard
was trying to express (I am making a huge
concession to the AI to say it meant __anything
at all__ - think of it in a figurative sense). The
algorithm I implemented was this fixed version.

I wrote the solution in a non-generalized way.
It was hard coded to 25 horses, 5 horses per race,
7 races. I also wrote the solution so that it
filled in the horses for heats 1-5 in the same order
they were given to the method.

Testing the implementation
-----------------------------------
The interface
```
	public interface IHorseRacing
	{
		int TopThreeHorses(int[] horses, int horsesPerRace, out List<int> finishers);
	}
```
The test method
```
		[TestMethod]
		public void TopThreeHorses_ascendingorder()
		{
			BardAlgorithm bard = new BardAlgorithm();
			int[] horseTimes = new int[] 
			{ 
				1,2,3,4,5,
				6,7,8,9,10,
				11,12,13,14,15,
				16,17,18,19,20,
				21,22,23,24,25 
			};
			int fastest = bard.TopThreeHorses(horseTimes, 5, out List<int> outs);
			Assert.AreEqual(3, outs.Count, "There better be three horses in there");
			Assert.AreEqual(0, outs[0]);
			Assert.AreEqual(1, outs[1]);
			Assert.AreEqual(2, outs[2]);
		}
```

Back to the conversation
===================================
Some readers will want to know the algorithm,
so I present it here as posted by John Elrick
in <a href="https://www.linkedin.com/posts/john-elrick-854782_chatgpt-activity-7152076280459378688-6-oL?utm_source=share&utm_medium=member_desktop">
this post from LinkedIn.</a>

>Identifying the top 3 fastest horses out of 25 with only 5 horses per race and no stopwatches requires a minimum of 7 races. This might seem surprising at first, but it's due to the limitations of not knowing individual times and only having relative placement information.
>
>Here's how we can achieve this in 7 races:
>
>
>Round 1: Divide and Conquer (5 races)
>
>Divide the 25 horses into 5 groups of 5 horses each.
>
>Run 5 races, one for each group.
>
>In each race, identify the top 3 finishers (let's call them A1, B1, C1, D1, and E1 for the respective groups).
>
>
>Round 2: Winner of Winners (1 race)
>Run another race with all the group winners (A1, B1, C1, D1, and E1).
>The winner of this race is guaranteed to be the fastest horse overall.
>
>
>Round 3: Pinpointing the Top 3 (1 race)
>
>Now, we need to identify the 2nd and 3rd fastest horses. Here's where it gets tricky.
>
>Analyze the groups of the horses that finished 2nd and 3rd in the "Winner of Winners" race (let's say B1 and C1, assuming A1 won).
>
>Run one final race with the following horses:
>
>The 2nd and 3rd finishers from the group of the overall winner (A2 and A3 in this case).
>
>The 2nd finisher from the group of the horse that came 2nd in the "Winner of Winners" race (B2 in this example).
>
>The horse that came 3rd in the "Winner of Winners" race (C1).
>
>In this final race, the winner will be the 2nd fastest horse overall.
>
>The horse that finishes 2nd in this race will be the 3rd fastest horse overall.
>
>
>This approach guarantees that you can identify the top 3 fastest horses in 7 races. While it might seem like more races could be involved, this method cleverly leverages the information gained from previous races to efficiently narrow down the contenders for the top positions.
>
>
>Remember, this is the minimum number of races required under these specific conditions. If you had more horses or could race more horses at a time, the number of races needed might change.

I argue, rightly, that Bard's description of the algorithm
if taken literally suggests 2 x 5 races are needed to determine
2nd and 3rd place, but with a small correction can be reduced
to a single heat.

John offers in one of the comments an elegant, and gorgeous diagram of how you
can determine 2nd and 3rd place in a single heat, which I have included below.

![A matrix of five races depicting that 2nd and 3rd place are either going to be in heat 6, or are in same heat as winner of six and second place of six](/assets/horserace2nd3rd.png)
