One time a program manager, James Pierson, told me "I like to think of testing like science
experiments. You make a hypothesis and you test it."

I was inclined to reply with the tester standby comment, "It depends..." but we were
working togther on performance, scalability, reliability, and capacity for SharePoint.
Science experiments work very well for that kind of testing.

Science you say?
===================================================
Let's review the scientific process:
1. we pick something we want to understand, we observe its behaviors
2. we create a hypothesis that would explain its behaviors, part of the hypothesis are conditions that would have to be true for the hypothesis to be correct
3. we create a test procedure to check for if the conditions are true
4. the test procedure will keep all parameters the same across every iteration except one which changes as per the hypothesis
5. we measure results and compare against the outcome predicted by the hypothesis, and if the outcome is not as expected, the hypothesis is false, if as expected the hypothesis is more likely to be correct

If we framed the hypothesis based on a product requirement, then a false
hypothesis indicates a problem, very likely a product bug. If the hypothesis is
confirmed, product team stakeholders typically consider that meaning the product
met requirements. Testers squirm at that sometimes, because they know more testing
may nullify the hypothesis, but we do understand the excitement over positive
results.

Sometimes we frame the hypothesis opposite of a product requirement, such
as when we are seeking bug repro conditions. In this case, a false hypothesis
indicates that the bug is not reproducing for the reasons we were checking, whereas
a true hypothesis indicates we probably have the repro.

Product requirements examples: Scalability testing
===================================================
Let's imagine you are testing a web-based server product that has a document
library feature. The document library supports file upload, download,
read, check-out, check-in, document review, and publish (make visible for
non-authors in the library). You are interested in the various performance
characteristics of the document library functionality. In particular
you want to understand how well the product scales.

The product architecture stores the document library data in
an SQL database. End user requests are handled by a web-front
end application tier which makes requests directly to the SQL
server. Multiple web-front servers may access the same SQL
database.

Scalability hypothesis: Does the product capacity scale with more machines?
--------------------------------------------------
Customers will want to be able to grow their system to meet end
user demand. If they reach maximum system capacity, they will want
to expand that capacity somehow. One of the solutions the product
offers is to add web-front end servers to handle end user requests
to the document library. We want to test how well that works.

We formulate a hypothesis:
> "Throughput capacity of a document library workload against the system will grow linearly as web-front end machines are added to the system, without a change in operational latency."

This hypothesis has a lot to define.

- __Throughput capacity__: For this problem, we mean Requests Per Second (RPS).
- __Document library workload__: This will be a mix of document library operations, executed over HTTPS. We will need to choose the percentage of different types of operations, data sizes, user accounts, user privileges.
- __Grow linearly as web-front end machines are added__: This means that as we go from 1 to 2 to 3 machines the maximum RPS we can achieve will likewise go up by a factor of X.
- __Without a change.. in latency__: This is how the increased capacity is checked. We establish the RPS where request latency begins to slow down and establish some point just before it slows down as maximum RPS capacity of the system.

