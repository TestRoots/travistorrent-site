---
layout: page
title: TravisTorrent
subtitle: Free and Open Travis Analytics for Everyone
---

TravisTorrent, a [GHTorrent](http://ghtorrent.org/) partner project, provides free and easy-to-use [Travis CI](http://www.travis-ci.com) build analyses to the masses through its open database.


<div class="main-explain-area jumbotron">
TravisTorrent provides access to a database of hundreds of thousands of analyzed travis builds in <strong>under 10 seconds</strong>.
  <div class="get-started-wrap">
    <a class="btn btn-success btn-lg get-started-btn" href="page_access">Access TravisTorrent now</a>
  </div>
</div>

### What does TravisTorrent do?
We access the [Travis CI](http://www.travis-ci.com) API and for each build, combine vanilla API data (such as build number and build result), with an analysis of the build log (such as how many tests were run, which test failed, ...) and repository and commit data from [GitHub](http://www.github.com) (such as latency between pushing and building), acquired through GHTorrent.

### What is the current status?
TravisTorrent has reached the state of a minimally working prototype. Currently, we use a static process to update our databases. We plan to automatically synchronize TravisTorrent with Travis, at least for a select number of projects (as analyzing log files is a CPU intensive process, and linking GitHub data requires lots of HTTP requests). We also plan to give our users the opportunity to add projects they are interested in.
At this point, our log analyses are focused on general Travis data and provide testing specialisations for Ruby and Java, but we plan to extend them.

### Why did you do it?
We are doing research on software repositories, testing and continuous integration. [Travis CI](http://www.travis-ci.com)  is an exciting new data source for us, one that has several of the problems we are facing as data miners solved. The uniformity of data will allow scaling of research to hundreds or thousands of repositories spanning across multiple languages and application domains. However, we encountered several non-trivial technical issues that might make it all but trivial for end users or researchers to use Travis data before processing, especially when they want to combine it with GitHub data.

### Why the name?
TravisTorrent was chosen to resemble the close proximity to the GHTorrent project. According to GHTorrent's website, "the name signifies a torrent of data coming from GitHub." Similarly, TravisTorrent can be seen as the torrent of logs arriving from Travis CI.

### Can I help?
Absolutely. Both our TravisPoker and TravisHarvester are [publicly available](https://github.com/Inventitech/travis-analysis) and looking forward to receiving your PRs!
