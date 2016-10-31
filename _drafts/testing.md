---
layout:     post
title:      "Some common questions about testing"
subtitle:   "test test"
#date:       2014-06-10 12:00:00
author:     "Antonio Jesús"
header-img: "img/post-bg-01.jpg"
---

<p>On this post I'll try to answer some of the questions that I asked myself when writing tests. There are plenty of
information on the internet about what's an unit test, some katas to start with it, etc but there is a big different
between the typical example and production code</p>

<p>I won't talk about what's an unit or an integration test, or a mock, or what's is TDD as there are enough information
out there</p>

<h2 class="section-heading">To mock or not to mock</h2>

<p>So once you're into testing and discover mocks you use them for every class you use as a dependency as we have been told
unit tests can't fail because some external dependency, that this is an integration. In my humble opinion, this is an
error. Mocking should be only used when it's really necessary, when the effort for using the dependency is huge or will
make your test suite slow. I'd include in there external libraries or wrappers of them, database/repositories,
send of emails, writing to a file, etc.</p>

<h4>What are the disadvantages of mocks?</h4>

<p>The main problem is that your test is coupled to the implementation. When you have that feeling that you're writing
the same lines of codes that the real implementation that's coupling. You'll have some calls to your mock with what's
exactly receiving, with how many arguments, what's the return value. It can generate a monster of mock objects</p>

<h4>What's the solution then?</h4>

<p>Apply TDD and avoid creating mocks for you own classes unless that necessary. I have found myself doing this: creating
some class, then writing the tests for it, refactoring this class to extract the logic in some other class. While my
tests are already covering both but I'm mocking now the extracted class, I need to create again some other test for the
extracted class as otherwise no test will execute it.</p>

<p>Of course, the disadvantage of not mocking is the setup of the objects can be really complex too. You have 2
dependencies which at the same time have more and the it grows exponentially. But this is for me a code smell and
remember you're already mocking database, your redis queue, etc</p>

<p>
Useful articles:
<a href="https://8thlight.com/blog/uncle-bob/2014/05/10/WhenToMock.html" target="_blank">When to mock</a>
<a href="http://martinfowler.com/articles/mocksArentStubs.html" target="_blank">Mocks aren't stubs</a>
</p>

<h2 class="section-heading">Unit vs Integration tests</h2>

<a href="#">
    <img src="{{ site.baseurl }}/img/no_integration_test.gif" alt="No Integration test">
</a>

<p>Of course you can have the case of the image, some useful unit tests but the application breaking. This is were
integration tests are useful. Some cases are an external library usage like a queue system where you can test a job is
inserted on redis. Generation of payments getting/inserting data into the database using some fixtures in the setUp()
and deleting the rows inserted in the tearDown(). External API calls using a fake server is another example. But don't
use them for increasing the coverage of your code.</p>

<h4>Functional tests</h4>

<img src="https://2.bp.blogspot.com/-YTzv_O4TnkA/VTgexlumP1I/AAAAAAAAAJ8/57-rnwyvP6g/s1600/image02.png" alt="Test pyramid">

<p>The image from the article <a href="https://testing.googleblog.com/2015/04/just-say-no-to-more-end-to-end-tests.html"
target="_blank">No more end to end tests</a> describes how I think they should be distributed. It's mentioned a
70/20/10 split: 70% unit tests, 20% integration tests, and 10% end-to-end tests. Functional tests or whatever they are
called nowadays are an important part for testing the UI of the application but the cost of maintaining and writing them
is too high. As a developer I think you shouldn't write them. If you're lucky enough to have a QA member in your team
helping, it should be part of his job. They should cover the main functionality and some bugs caused in the past,
that's all.</p>


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

REMOVE ME!!!!

<p>On this last point I would like to mention an example of an strange case: a test checking the integration between the
Redis library and a wrapper of it. So the code is trying to connect with a host/port and in case that it can't be done
it throws an exception. For the sake of coverage of this exception and as this was an integration test, not unit, the
test was trying to connect to a random port not used by Redis, 9999. "On my machine is working" (TM) but when it's
executed on the Jenkins machine failed. The reason was some other guy was using this port at this time for another job
so the test was saying "Connection success" and breaking.</p>
