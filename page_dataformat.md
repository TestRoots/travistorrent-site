---
layout: page
title: Data Format
subtitle:
comments: true
---

On this page, we describe TravisTorrent's data format.

<div class="alert alert-success" role="alert">We always describe the structure of the latest <a href="/page_access/">TravisTorrent data dump</a> here. Make sure you are using this, as there might be inconsistencies otherwise.</div>

# General Data Structure
In the TravisTorrent data set, each data point (row) represents a build job executed on Travis. Every such data point synthesizes information from three different sources: The project's git repository (prefixed `git_`), data from Travis's API and an analysis of the build log (prefixed `tr_`), and data extracted from GitHub through GHTorrent (prefixed `gh_`).

# Data construction defaults

* All line measurements are excluding comments.
* For Ruby, we support test file identification for RUnit, RSpec and Minitest standard paths (we explicitely ignore Cucumber features);
we also support test case filtering for RUnit, Shoulda and RSpec tests. More details can be found in [this file](https://github.com/TestRoots/travistorrent-tools/blob/master/bin/ruby.rb).
* For Java, we support test file identification for standard Maven and JUnit conventions. More details can be found in [this file](https://github.com/TestRoots/travistorrent-tools/blob/master/bin/java.rb).
* All variables that have a time dimension (e.g. team_size) are calculated within the timeframe from the moment the build was triggered and 3 months back

# Data Description

|Column |Description | 
| ----  | ---------- |
| `tr_build_id` | The analyzed build id, as reported from Travis CI. |
| `tr_job_id` | The job id of the build job under analysis. |
| `tr_build_number` | The serial build number of the build under analysis for this project. |
| `gh_project_name` | Project name on GitHub. |
| `gh_is_pr` | Whether this build was triggered as part of a pull request on GitHub. |
| `gh_pr_created_at` | If the build is a pull request, the creation timestamp for this pull request, in UTC. |
| `gh_pull_req_num` | If the build is a pull request, its ID on GitHub. |
| `gh_lang` | Dominant repository language, according to GitHub. |
| `git_merged_with` | If this commit sits on a pull request (`gh_is_pr` true), how it was closed (merge button, manual merge, ...). |
| `git_branch` | The branch that was built |
| `gh_num_commits_in_push` | Number of commits included in the push that triggered the build. In rare cases, GHTorrent has not recorded a push event for the commit that created the build in which case `num_commits_in_push` is nil. |
| `gh_commits_in_push` | The commits included in the push that triggered the build. In rare cases, GHTorrent has not recorded a push event for the commit that created the build in which case `gh_commits_in_push` is "" (empty string). |
| `git_prev_commit_resolution_status` | When walking backwards the branch to find previously built commits, what is the reason for stopping the traversal? Can be one of: `no_previous_build`: when , `build_found`: when we find a previous build, or `merge_found`: when we had to stop traversal at a merge point (we cannot decide which of the parents to follow). |
| `git_prev_built_commit` | The commit that triggered the previous build on a linearized history. If `git_prev_commit_resolution_status` is `merge_found`, then this is nil. |
| `tr_prev_build` | The build triggered by `git_prev_built_commit`. If `git_prev_commit_resolution_status` is `merge_found`, then this is nil. |
| `gh_first_commit_created_at` | Timestamp of first commit in the push that triggered the build, in UTC. In rare cases, GHTorrent has not recorded a push event for the commit that created the build in which case `first_commit_created_at` is nil. |
| `gh_team_size` | Number of developers that committed directly or merged PRs from the moment the build was triggered and 3 months back. |
| `git_all_built_commits` | A list of all commits that were built for this build, up to but excluding the commit of the previous build, or up to and including a merge commit (in which case we cannot go further backward). The internal calculation starts with the parent for PR builds or the actual built commit for non-PR builds, traverse the parent commits up until a commit that is linked to a previous build is found (excluded, this is under `tr_prev_built_commit`) or until we cannot go any further because a branch point was reached. This is indicated in `git_prev_commit_resolution_status`. This list is what the `git_diff_*` fields are calculated upon. |
| `git_num_all_built_commits` | Number of `git_all_built_commits`. |
| `git_trigger_commit` | The commit that triggered the build. |
| `tr_virtual_merged_into` | The commit of the branch that the commit built by Travis is merged into when testing pull requests. |
| `tr_original_commit` | The SHA of the original commit that was build as linked to from Travis. Might be a virtual commit that is not part of the original repository. |
| `gh_num_issue_comments` | If git_commit is linked to a PR on GitHub, the number of discussion comments on that PR. |
| `gh_num_commit_comments` | The number of comments on `git_all_built_commits` on GitHub. |
| `gh_num_pr_comments` | If gh_is_pr is true, the number of comments (code review) on this pull request on GitHub. |
| `git_diff_src_churn` | Number of lines of production code changed in all `git_all_built_commits`. |
| `git_diff_test_churn` | Number of lines of test code changed in all `git_all_built_commits`. |
| `gh_diff_files_added` | Number of files added by all `git_all_built_commits`. |
| `gh_diff_files_deleted` | Number of files deleted by all `git_all_built_commits`. |
| `gh_diff_files_modified` | Number of files modified by all `git_all_built_commits`. |
| `gh_diff_tests_added` | Lines of testing code added by all `git_all_built_commits`. |
| `gh_diff_tests_deleted` | Lines of testing code deleted by all `git_all_built_commits`. |
| `gh_diff_src_files` | Number of src files changed by all `git_all_built_commits`. |
| `gh_diff_doc_files` | Number of documentation files changed by all `git_all_built_commits`. |
| `gh_diff_other_files` | Number of files which are neither source code nor documentation that changed by the commits that where built. |
| `gh_num_commits_on_files_touched` | Number of unique commits on the files touched in the commits (`git_all_built_commits`) that triggered the build from the moment the build was triggered and 3 months back. It is a metric of how active the part of the project is that these commits touched. |
| `gh_sloc` | Number of executable production source lines of code, in the entire repository. |
| `gh_test_lines_per_kloc` | Test density. Number of lines in test cases per 1000 `gh_sloc`. |
| `gh_test_cases_per_kloc` | Test density. Test density. Number of test cases per 1000 `gh_sloc`. |
| `gh_asserts_cases_per_kloc` | Test density. Assert density. Number of assertions per 1000 `gh_sloc`. |
| `gh_by_core_team_member` | Whether this commit was authored by a core team member. A core team member is someone who has committed code at least once within the 3 months before this commit, either by directly committing it or by merging commits. |
| `gh_description_complexity` | If the build is a pull request, the total number of words in the pull request title and description. |
| `gh_pushed_at` | Timestamp of the push that triggered the build (GitHub provided), in UTC. |
| `gh_build_started_at` | Timestamp of the push that triggered the build (Travis provided), in UTC. |
| `tr_status` | The build status (such as passed, failed, ...) as returned from the Travis CI API. |
| `tr_duration` | The full build duration as returned from the Travis CI API. |
| `tr_jobs` | The unique Travis IDs of the jobs, in a string separated by `#`. |
| `tr_log_lan` | The primary programming language, extracted by build log analysis. |
| `tr_log_status` | The overall return status of the build, extracted by build log analysis. |
| `tr_log_setup_time` | The setup time before the script phase (the actual build) starts, in seconds, extracted by build log analysis. |
| `tr_log_analyzer` | The build log analyzer that was invoked for analysis of this build. |
| `tr_log_frameworks` | The testing frameworks ran extracted by build log analysis. |
| `tr_log_bool_tests_ran` | Whether tests were run, extracted by build log analysis. |
| `tr_log_bool_tests_failed` | Whether tests failed, extracted by build log analysis. |
| `tr_log_num_tests_ok` | Number of tests that succeeded, extracted by build log analysis. |
| `tr_log_num_tests_failed` | Number of tests that failed, extracted by build log analysis. |
| `tr_log_num_tests_run` | Number of tests that ran in total, extracted by build log analysis. |
| `tr_log_num_tests_skipped` | Number of tests that were skipped, extracted by build log analysis. |
| `tr_log_tests_failed` | Names of the tests that failed, extracted by build log analysis. |
| `tr_log_testduration` | Duration of the running the tests, in seconds, extracted by build log analysis. |
| `tr_log_buildduration` | Duration of running the build command like maven or ant (if present, should be longer than `:tr_log_testduration` as it includes this phase), in seconds, extracted by build log analysis. |
