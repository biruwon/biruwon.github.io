---
layout:     post
title:      "Some common questions about testing"
subtitle:   "test test"
#date:       2014-06-10 12:00:00
author:     "Antonio Jesús"
header-img: "img/post-bg-01.jpg"
---

<p>On this post I'll try to answer some of the questions that I asked myself when writing tests</p>

<h2 class="section-heading">How much coverage?</h2>

<p>For me, the aim is 100% and this is where you should point. If you set a minimum limit, let say, 80%, does this mean
that you should only cover 4 of your 5 methods on your class? Specially if you're doing TDD you should have a high
number</p>

<blockquote>
<p>If you are testing thoughtfully and well, I would expect a coverage percentage in the upper 80s or 90s. I
would be suspicious of anything like 100% - it would smell of someone writing tests to make the coverage numbers happy,
but not thinking about what they are doing.</p>

-- Martin Fowler
</blockquote>

<p>I usually exclude from the coverage:</p>

<p>Entities, DTOs, Queries and Repositories, specific classes from the framework and the glue between the client and the
backend side like Controllers (more about it in the next point)</p>


<h2 class="section-heading">Funny tests</h2>

<p>On this last point I would like to mention an example of an strange case: a test checking the integration between the
Redis library and a wrapper of it. So the code is trying to connect with a host/port and in case that it can't be done
it throws an exception. For the sake of coverage of this exception and as this was an integration test, not unit, the
test was trying to connect to a random port not used by Redis, 9999. "On my machine is working" (TM) but when it's
executed on the Jenkins machine failed. The reason was some other guy was using this port at this time for another job
so the test was saying "Connection success" and breaking.</p>
