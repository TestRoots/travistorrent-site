---
layout: page
title: Technical Details
subtitle:
comments: true
---

Mining Travis CI and linking it to GitHub data poses several difficulties:

- The official Ruby client is ill-suited for batch-aquiring logs (both due to a
resource leak and because it does not fetch all logs).
- Establishing a connection between GitHub and Travis CI build logs is all but
trivial.

In particular, we have to deal with the issue of build linearization and
mapping, detailed in the following section.

# Build Linearization and Mapping
If we want to answer questions such as ``how much latency does CI
introduce,'' we need to make a connection between the builds performed
on Travis and the repository which contains the commits that triggered
the build. We call this build linearization and commit mapping, as we
need to interpret the builds on Travis as a directed graph and
establish a child-parent relationship based on the Git commits that
triggered their execution.

![My helpful screenshot](../img/git-travis-matching.png)

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
3. occurs, in which one merge commit #H naturally has two predecessors.