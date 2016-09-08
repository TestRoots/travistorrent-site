---
layout: page
title: Data Format
subtitle:
comments: true
---

On this page, we describe TravisTorrent's data format.

# General Data Selection
At this point, from the 17,313,330 active OSS repositories on GitHub in August, 2015, our data set contains a static snapshot and deep analysis of the project source code, process and dependency status of 1,359 projects. To be able to do this, we restricted our project space using established filtering criteria to all non-fork, non-toy, somewhat popular (> 10 watchers) projects with a history of Travis CI use (> 50 builds) in Ruby (936) or Java (423). Both languages are very popular on GitHub (2nd and 3rd, respectively). Then, we extracted and analyzed build information from Travis CI build logs and the GHTorrent database for each Travis CI build in its history. Well-known projects in the TravisTorrent data set include all 691,184 builds from Ruby on Rails, Google Guava and Guice, Chef, Rspec, Checkstyle, ASCIIDoctor, Ruby and Travis itself.

# General Data Structure
In the TravisTorrent data set, each data point (row) represents a build job executed on Travis. Every such data point synthesizes information from three different sources: The project's git repository (prefixed `git_`), data from Travis's API and an analysis of the build log (prefixed `tr_`), and data extracted from GitHub through GHTorrent (prefixed `gh_`).

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
| "gh_first_commit_created_at" | Date of first commit for the entire repository ('project start') | ISO Date |
| "gh_team_size" | Size of the team contributing to this project | Integer |
| "git_commits" | List of directly preceeding commits that do not have build (e.g., transferred in one push, build cancelled, ...) this build comprises | String. List of commits, separated by Hashtag `#` |
| "git_num_commits" | The number of commits in git_commits, to ease efficient splitting | String.  |
| "gh_num_issue_comments" | If git_commit is linked to an issue on GitHub, the number of comments on that issue | Integer |
| "gh_num_commit_comments" | The number of comments on git_commit on GitHub | Integer |
| "gh_num_pr_comments" | If gh_is_pr is true, the number of comments on this pull request on GitHub | Integer |
| "gh_src_churn" | The churn of git_commit, i.e. how much production code changed in the commit, based on lines | Integer |
| "gh_test_churn" | The churn of git_commit, i.e. how much test code changed in the commit, based on lines | Integer |
| "gh_files_added" | Number of files added in git_commit (this is generally correlated with the churn) | Integer |
| "gh_files_deleted" | Number of files deleted in git_commit (this is generally correlated with the churn) | Integer |
| "gh_files_modified" | Number of files modified in git_commit (this is generally correlated with the churn) | Integer |
| "gh_tests_added" | How many test cases were added in git_commit (e.g., for Java, this is the number of @Test annotations) | Integer  |
| "gh_tests_deleted" | How many tests were deleted in git_commit  (e.g., for Java, this is the number of @Test annotations) | Integer  |
| "gh_src_files" | Number of production files in the repository | Integer |
| "gh_doc_files" | Number of documentation files in the repository | Integer |
| "gh_other_files" | Number of remaining files which are neither production code nor documentation | Integer |
| "gh_commits_on_files_touched" | Number of commits that touched (added/deleted/modified) the files in git_commit previously | Integer |
| "gh_sloc" | Number of executable production source lines of code, in the entire repository | Integer |
| "gh_test_lines_per_kloc" | Test density. Number of lines in test cases per 1000 gh_sloc | Double |
| "gh_test_cases_per_kloc" | Test density. Number of test cases per 1000 gh_sloc | Double |
| "gh_asserts_cases_per_kloc" | Assert density. Number of assertions per 1000 gh_sloc | Double |
| "gh_by_core_team_member" | Whether this commit was authored by a core team member | Boolean |
| "gh_description_complexity" | If gh_is_pr, the Pull Request's textual description complexity | Integer  |
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

