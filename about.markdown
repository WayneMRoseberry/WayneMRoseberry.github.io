---
layout: page
title: "About me and what I do"
permalink: /about
---
<table>
  <tr>
{% for link in site.data.navigation %}
  <td><a class="page-link" href="{{ link.url }}">{{ link.title }}</a></td>
{% endfor %}    
  </tr>
</table>

Work Experience
==========
I am a software engineer, tester, test leader. Most of my career has been at Microsoft. I started as a phone support 
engineer, helping customers with Microsoft Word. I worked on the original MSN 1.0 team as a tester, starting in 
April, 1994. After two and a half releases of MSN, I worked on both Microsoft Commercial Internet Services, and Microsoft Site Server.

Toward the end of the 1990s I was one of the original founding members of the Microsoft Sharepoint team, and stayed
with that as a test lead, test manager, tester, software design engineer through multiple iterations until 2012. At that
point I moved to the Office Engineering team, which builds the tools, services, and systems used to ship Microsoft Office.
In 2014 became a software engineer as part of Microsoft's overall movement to align development and testing into a
single job role.

Across my career I have worked on GUI applications, client-server software, enterprise servers, proprietary online services, and
large-scale cloud hosted software. I have spent years working with, design, and utilizing automation systems. I enjoy
pushing boundaries of innovation with software testing, and automation.

Books
==========

<a href="https://www.amazon.com/Writing-Test-Plans-Made-Easy/dp/1478333693">![Writing Test Plans Made Eash](/assets/writingtestplanscover.jpg)</a>

Many of the testers working for me had trouble writing test plans. Most of them were either suffering from writers
block, or were struggling with how to represent their testing ideas. I found a simple, fast, outline based
format helped my testers get ideas on paper quickly that were easy to understand and review. I share that approach in this book.

Conferences and Presentations
==========

Flaky Test Automation
---------------------
This is a presentation I gave describing how the Office team at Microsoft not just manages, but
utilizes flaky test results to their advantage. I describe the important difference between using
automation to gate code submissions versus using automation to find bugs, and how flaky test
automation is an important signal in both those activities.

<a href="https://www.youtube.com/watch?v=S-da7O4d_Jw&t=370s">![Winning with Flaky Test Automation](/assets/flakyautomationconfimage.jpg)</a>

Using Product Signal Information In Testing
---------------------
This is a presentation where I describe how we integrated product telemetry signals
with the automation systems in Office, and different ways of using that information to
infer information about product quality as well as assess the performance and quality
of your automation suite. This talk represents a key cornerstone priniple I used for
later research applying AI to product testing practices.

<a href="https://www.youtube.com/watch?v=tulztPtNSDo&t=1821s">![Is Your Automation Any Good](/assets/isyourautomationanygoodconfimage.jpg)</a>
