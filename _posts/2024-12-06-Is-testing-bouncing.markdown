Get rid of testers, then start hiring testers
=============================================
I have been interviewing for work since I was laid off
in March of 2023. I was surprised to see evidence of a
testing bounce, a return to open testing jobs from
workplaces that had previously decided they didn't
need testers. It is not a loud, large signal, but it
has been there.

Example interviews I had with employers this year
=============================================
These are all real interviews I had with employers this
year. I have obscured details about company names,
and product specifics. Three of these are companies
with worldwide, every day brand recognition. Three of
them did not have an open head approved, but were exploring
the idea because of problems that came up. Four of the
five companies do not have testers.

- A director of engineering at a very well known
company (almost everybody uses their product at
least once in their lives) who was looking to fill two
positions. This company had gone all in on removing
their QA teams and pushing all testing to their
developers several years prior. One open position
 was a director of engineeering reporting
to her that was to drive improved testing capability services,
tooling and support because their development teams were
having trouble covering critical tests on their own without
better capabilities. The other was an engineering manager
that was to run a team of SDETs whose charter was to
run through enterprise customer platform extensibility and
custom application scenarios, because they had been having trouble
with those end to end scenarios breaking after new product
drops.
- A VP of data quality at a data analysis services company
whose customers use that data for their own research as
part of regulatory approval for bringing product to market. The
company was built on a "No QA teams needed, developers do all
their own testing" paradigm, and any overt indication of
hiring QA positions was met with sharp disaproval from the
development side of the house. This VP was concerned with
recent instances of data integrity problems coming out their
pipeline. For their customers, data integrity problems result
in legal trouble, the kind where they are brought forward to
testify before congress with stiff penalties. The VP was hoping
to open a head to covert test engineering leader that wasn't
called QA, but whose primary goal was to build the strategy
to cover the gaps in testing for data integrity to prevent these kinds
of problems.
- An engineering manager for a games product who related to
me a conversation where his manager had said "Remember when we
got rid of all the testers several years ago? I think that was
a mistake?" and has since been building a team of engineers whose
primary goal is to build and enable testing capabilities that
cut across the scenarios where the individual development teams
miss problems. One of the examples he described was a really awesome
skunkworks mechanisms that measures online streamed gameplay latencies
end to end, catching bugs in performance that at every single
individual end point was measured as within tolerances but
when covered end to end was clearly causing customer problems.
- A director of engineering at a large, centuries old
non-software company who is looking to modernize their software
integrated aspects of product  (burned onto silicon right now)
and shift to modern software modes where product can be
updated, improved and fixed without having to update chips. The
nature of the product is such that software failures could
range from trivial risk to physically harmful or fatal. The
teams are working on old-style, slow, hardware based schedules
with long pole tasks for everything, including testing, and
they hope to bring a faster, more effective, more efficient
testing process to the team. The goal here is to get
ahead of the game, get the testing covered well without a
hasty shift to 100% fast feedback biased iterations.
- An engineer manager at a company who produces monitoring
software for power systems. Their business has no QA teams. They
are not exactly CI/CD with fast, trunk based Agile engineering, but
their market pressures are such that this doesn't matter to them, and they
run pretty close to that state anyway. They had not had any huge problems
come up, but the manager was noticing inefficiences in the way some
kinds of testing and quality assessment was happening, and felt a
need for somebody to drive testing practices that spanned across
systems or filled gaps the individual development teams were
not hitting. The position needed VP and CEO approval, which he
was unable to get after our conversation.
