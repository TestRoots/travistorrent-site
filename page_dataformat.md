---
layout: page
title: Data Format
subtitle:
comments: true
---

On this page, we describe TravisTorrent's data format.

# General Data Selection
At this point, from the 17,313,330 active OSS repositories on GitHub in August, 2015, our data set contains a static snapshot and deep analysis of the project source code, process and dependency status of 1,300 projects. To be able to do this, we restricted our project space using established filtering criteria to all non-fork, non-toy, somewhat popular (> 10 watchers) projects with a history of Travis CI use (> 50 builds) in Ruby (898) or Java (402). Both languages are very popular on GitHub (2nd and 3rd, respectively). Then, we extracted and analyzed build information from Travis CI build logs and the GHTorrent database for each Travis CI build in its history. Well-known projects in the TravisTorrent data set include all 691,184 builds from Ruby on Rails, Google Guava and Guice, Chef, Rspec, Checkstyle, ASCIIDoctor, Ruby and Travis itself.

# General Data Structure
In the TravisTorrent data set, each data point (row) represents a build job executed on Travis. Every such data point synthesizes information from three different sources: The project's git repository (prefixed `git_`), data from Travis's API and an analysis of the build log (prefixed `tr_`), and data extracted from GitHub through GHTorrent (prefixed `gh_`).

# Data construction defaults

* All line measurements are excluding comments.
* For Ruby, we support test file identification for RUnit, RSpec and Minitest standard paths (we explicitely ignore Cucumber features);
we also support test case filtering for RUnit, Shoulda and RSpec tests. More details can be found in [this file](https://github.com/TestRoots/travistorrent-tools/blob/master/bin/ruby.rb).
* For Java, we support test file identification for standard Maven and JUnit conventions. More details can be found in [this file](https://github.com/TestRoots/travistorrent-tools/blob/master/bin/java.rb).
* All variables that have a time dimension (e.g. team_size) are calculated within the timeframe from the moment the build was triggered and 3 months back

# Data Description

|Column |Description | Unit |
| ----  | ---------- | ---- |
| "row" | Unique identifier for a build job in TravisTorrent | Integer |
| "git_commit" | SHA1 Hash of the commit which triggered this build (should be unique world-wide) | String |
| "gh_project_name" | Project name on GitHub (in format user/repository) | String |
| "gh_is_pr" | Whether this build was triggered as part of a pull request on GitHub | Boolean |
| "git_merged_with" | If this commit sits on a Pull Request (gh_is_pr true), the SHA1 of the commit that merged said pull request | String |
| "gh_lang" | Dominant repository language, according to GitHub | String |
| "git_branch" | Branch git_commit was committed on | String |
| "gh_first_commit_created_at" | Timestamp of first commit in the push that triggered the build | ISO Date |
| "gh_team_size" | Number of developers that committed directly or merged PRs from the moment the build was triggered and 3 months back. | Integer |
| "git_commits" | All commits included in the push that triggered the build, minus the built commit | String. List of commits, separated by Hashtag `#` |
| "git_num_commits" | The number of commits in git_commits, to ease efficient splitting | String.  |
| "gh_num_issue_comments" | If git_commit is linked to a PR on GitHub, the number of discussion comments on that PR | Integer |
| "gh_num_commit_comments" | The number of comments on git_commits on GitHub | Integer |
| "gh_num_pr_comments" | If gh_is_pr is true, the number of comments (code review) on this pull request on GitHub | Integer |
| "gh_src_churn" | How much (lines) production code changed in the commits built by this build | Integer |
| "gh_test_churn" | How much (lines) test code changed in the commits built by this build | Integer |
| "gh_files_added" | Number of files added by the commits built by this build | Integer |
| "gh_files_deleted" | Number of files deleted by the commits built by this build | Integer |
| "gh_files_modified" | Number of files modified by the commits built by this build | Integer |
| "gh_tests_added" | Lines of testing code added by the commits built by this build | Integer  |
| "gh_tests_deleted" | Lines of testing code deleted by the commits built by this build  | Integer  |
| "gh_src_files" | Number of src files changed by the commits that where built | Integer |
| "gh_doc_files" | Number of documentation files changed by the commits that where built | Integer |
| "gh_other_files" | Number of files which are neither production code nor documentation that changed by the commits that where built | Integer |
| "gh_commits_on_files_touched" | Unique commits on the files included in the build from the moment the build was triggered and 3 months back  | Integer |
| "gh_sloc" | Number of executable production source lines of code, in the entire repository | Integer |
| "gh_test_lines_per_kloc" | Test density. Number of lines in test cases per 1000 gh_sloc | Double |
| "gh_test_cases_per_kloc" | Test density. Number of test cases per 1000 gh_sloc | Double |
| "gh_asserts_cases_per_kloc" | Assert density. Number of assertions per 1000 gh_sloc | Double |
| "gh_by_core_team_member" | Whether this commit was authored by a core team member | Boolean |
| "gh_description_complexity" | If gh_is_pr, the total number of words in the pull request title and description | Integer  |
| "tr_build_id" | Unique build ID on Travis | String |
| "gh_pull_req_num" | Pull request number on GitHub | Integer |
| "tr_status" | Build status (pass, fail, errored, cancelled) | String |
| "tr_duration" | Overall duration of the build | Integer (in seconds) |
| "tr_started_at" | Start of the build process | ISO Date |
| "tr_jobs" | Which Travis jobs executed this build (number of integration environments) | List of Strings |
| "tr_build_number" | Build number on Travis (unique per project) | Integer |
| "tr_job_id" | This build job's id, one of tr_jobs | String |
| "tr_lan" | Language of the build, as recognized by BuildLogAnalyzer | String |
| "tr_setup_time" | Setup time for the Travis build to start | Integer (in seconds) |
| "tr_analyzer" | Build log analyzer that took over (ruby, java-ant, java-maven, java-gradle) | String |
| "tr_frameworks" | Test frameworks that tr_analyzer recognize and invoke (junit, rspec, cucumber, ...) | String. List of Strings, separated by `#` |
| "tr_tests_ok" | If available (depends on tr_frameworks and tr_analyzer): Number of tests passed | Integer |
| "tr_tests_fail" | If available (depends on tr_frameworks and tr_analyzer): Number of tests failed | Integer |
| "tr_tests_run" | If available (depends on tr_frameworks and tr_analyzer): Number of tests were run as part of this build | Integer |
| "tr_tests_skipped" | If available (depends on tr_frameworks and tr_analyzer): Number of tests were skipped or ignored in the build |  Integer |
| "tr_failed_tests" | All tests that failed in this build | A list of strings, separated by `#` |
| "tr_testduration" | Time it took to run the tests | Double (in seconds)  |
| "tr_purebuildduration" | Time it took to run the build (without Travis scheduling and provisioning the build) | Double (in seconds)  |
| "tr_tests_ran" | Whether tests ran in this build | Boolean  |
| "tr_tests_failed" | Whether tests failed in this build | Boolean  |
| "git_num_committers" | Number of people who committed to this project | Integer |
| "tr_num_jobs" | How many jobs does this build have (lenght of tr_jobs) | Integer |
| "tr_prev_build" | Serialized link to the previous build, by giving its tr_build_id | String |
| "tr_ci_latency" | Latency induced by Travis (scheduling, build pick-up, ...) | Integer (in seconds) |

