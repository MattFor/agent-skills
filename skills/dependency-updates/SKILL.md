---
name: dependency-updates
description: Handle dependency-bot pull requests (Dependabot, Renovate): list them across repos, merge the ones with green checks, fix or report the failing ones. Use for "merge the dependabot PRs", "update dependencies", "dependabot is failing".
---

# Dependency updates

Merging is outward-facing: merge only when the user asked for it in this conversation.

## 1. List

`gh-dependabot --owner OWNER` (or named repos) from [agent-tools](https://github.com/MattFor/agent-tools); without it, `gh pr list -R REPO --author app/dependabot --json number,title,mergeable,statusCheckRollup` per repo. Done when every open bot PR is in one table with its check state and mergeability.

## 2. Merge the green ones

`gh-dependabot … --merge`, or `gh pr merge N -R REPO --squash --delete-branch` per PR whose checks all passed and which is mergeable. After a merge, later PRs in the same repo may conflict: the bot rebases them itself, so leave them and list them as pending.

## 3. Diagnose the failing ones

Read the failure (`gh run view RUN -R REPO --log-failed | tail -60`) and sort it:

- **Flaky or infrastructure**: re-run once (`gh run rerun RUN --failed`).
- **Breaking upgrade** (usually a major version): read the dependency's release notes for the breaking change, fix the code on a new branch based on the bot's branch, open a PR that supersedes the bot's, and say so in a comment on it.
- **Incompatible or unwanted**: report it with the reason; closing or ignoring a version is the user's call (`@dependabot ignore this major version`).

## 4. Report

One table: repo, PR, action taken (merged, re-run, fixed in #N, needs a decision). Done when every bot PR from step 1 has an action or a stated reason it has none.
