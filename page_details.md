---
layout: page
title: Technical Details
subtitle:
comments: true
---

On this page, we describe the challenges and technicalities of mining Travis CI and linking it to GitHub data poses several difficulties:

- The official Ruby client is ill-suited for batch-acquiring logs (both due to a
resource leak and because it does not fetch all logs).
- Establishing a connection between GitHub and Travis CI build logs is all but
trivial.

In particular, we have to deal with the issues of efficiently collecting data, build linearization and
mapping, detailed in the following sections.

# Data Collection

*TravisPoker.*  To find out which and how many projects on
GitHub use Travis, we implemented TravisPoker. This fast and
lightweight application takes a GitHub project name as input (for
example, `rails/rails`), and finds out if and how many Travis
builds were executed for this project.

*TravisHarvester.*  We implemented TravisHarvester to aggregate
detailed information about a project's Travis build history. It takes as input
a GitHub project name and gathers general statistics on each build in the
project's history in a CSV file. Associated with each build entry in the CSV
are the SHA1 hash of the Git commit, the branch and (if applicable)
pull request on which the build was executed, the overall build status, the
duration and starting time and the sub jobs that Travis executed for the
different specified environments (at least one job, possibly many for each
build). TravisHarvester downloads the build logs for each build for
all jobs and stores them alongside the CSV file.

While both TravisPoker and TravisHarvester utilize
Travis CI's Ruby client for querying the
[API](https://github.com/travis-ci/travis.rb), we cannot use its
job log retrieval function (Job:log) due to a memory
leak ([reported](https://github.com/travis-ci/travis.rb/issues/310)) and
because it does not retrieve all build logs.  We circumvented these problems by
also querying an [Amazon Cloud](http://s3.amazonaws.com/archive.travis-ci.org) server we discovered that archives
build logs.

To speed up the process of retrieving thousands of log files for each project,
we parallelize our starter scripts for TravisHarvester with [GNU Parallel](https://www.gnu.org/software/parallel/).


# Analysis of Build Logs

BuildAnalyzer is a framework that supports the general-purpose analysis of Travis build
logs and provides dedicated Java and Ruby build analyzers that parse build logs in both languages
and search for output traces of common testing frameworks.

The language-agnostic BuildAnalyzer reads-in a build log, splits it into the different build
phases, and analyzes the build status and runtime of each phase. The fold for the `script`
phase contains the actual build and continuous testing results. The BuildAnalyzer dispatches the
automatically determined sub-BuildAnalyzer for further examination of the build phase.

For Java, we support the three popular build tools Maven, Gradle, and Ant. In Java, it is
standard procedure to use JUnit as the test runner, even if the tests
themselves employ other testing frameworks, such as PowerMock or
Mockito. Moreover, we also support TestNG, the second most
popular testing framework for Java. Running the tests of an otherwise unchanged
project through Maven, Gradle and Ant leads to
different, incompatible build logs, with Maven being the most verbose
and Gradle the least.  Hence, we need three different parsers to
support the large ecosystem of popular Java build tools. As a consequence, the
amount of information we can extract from a build log varies per build
technology used. Moreover, some build tools give users the option to modify
their console output, albeit rarely used in practice.

```
-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running nl.tudelft.watchdog.ClientVersionCheckerTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.04 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] All tests passed!
```

The listing above shows an excerpt of one test execution from the
`TestRoots/WatchDog` project. In the output, we can see the executed
test classes (line 4), and how many tests passed, failed, errored and were
skipped. We also get the test execution time (line 5). Moreover, Maven prints
an overall result summary (line 9) that the BuildAnalyzer uses to triage its
prior findings. Line 11 shows the overall test execution result. Our
BuildAnalyzer gathers all this information and creates, for each invoked
project, a CSV table with all build and test results for each job built. We
then aggregate this information with information from the build status analyzer
step by joining their output. TravisTorrent provides easy access to this data.

The listing below shows the equivalent Gradle output. The silent
Gradle becomes more verbose when a test fails, providing us with
similar information to our first listing.

```
:test
```

By contrast, in Ruby, the test framework is responsible for the console output:
it is no different to invoke RSpec through Rake than through
Bundler, the two predominant Ruby build (support)
tools. For Ruby, we support the prevalent
Test::Unit and all its off springs, like MiniTest. Moreover,
we capture behavior driven tests via RSpec and Cucumber
support.


# Build Linearization and Mapping
If we want to answer questions such as ``how much latency does CI
introduce,'' we need to make a connection between the builds performed
on Travis and the repository which contains the commits that triggered
the build. We call this build linearization and commit mapping, as we
need to interpret the builds on Travis as a directed graph and
establish a child-parent relationship based on the Git commits that
triggered their execution.

![Commit Build Mapping](../img/git-travis-matching.png)

The above picture exemplifies a typical GitHub project that uses Travis for its
CI. In the upper part (1), we see the Travis builds (§1-§9), which are either
passed (§1-§6, §9), canceled (§7), or broken (§8). In the lower part, we see
the corresponding GIT repository hosted on GitHub with its individual commits
(#A-#H).  Commits #D1-#D3 live in a pull request, and not on the master branch,
traditionally the main development line in GIT. There are many complex
scenarios that can occur:

1. Build §1 showcases a standard situation, in which the build
passed and the commit id stored with the build leads to the correct
commit #A that triggered build §1. However, there are a number of
more complex situations.

2. If multiple commits are transferred in one git push (3), only the latest of
those commits is built (§2). In order to get a precise representation of the
changes that lead to this build result, we have to aggregate commits #B and #C.

3. It is a central function of Travis CI to support branches or pull requests
(4) such as commit #D1. When resolving builds to commits, we know from the API
that §3 is a pull request build). Its associated commit points us to a virtual
integration commit #V1 that is not part of the normal repository.

4. In the case of build §6 on the same pull request (6), its direct predecessor
is unclear: we traverse from #V3 to both 1) commit #D2 in the pull request,
which is known, and to 2) #E on the master branch, which is unknown and cannot
be reached from any of our previous commits #D2, #D1, or #C. This is because
there was an intermediate commit #E on the master branch in-between, and pull
requests are always to be integrated onto the head commit of the branch they
are filed against. In such a case, one build can have multiple parents, and it
is undecidable whether the changes in #D3, #E or a combination of both lead to
the build result §6.

5. Build §5 shows why a simple linearization of the build graph
by its build number would fail: It would return §4 as its predecessor, when it
really is §2 (7).

6. Finally, when merging branches or pull requests (8), a similar situation to 
step 3. occurs, in which one merge commit #H naturally has two predecessors.


