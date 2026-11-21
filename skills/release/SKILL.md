---
name: release
description: Cut a software release: pick the SemVer bump, write the user-facing changelog entry, bump every version file, tag, let CI publish, verify the release page and registry. Use for "release", "publish a new version", "bump the version", "write the changelog".
---

# Release

## 1. Preconditions

On the default branch, up to date with its remote, clean tree, and CI green on the current commit (`waitfor ci` from [agent-tools](https://github.com/MattFor/agent-tools), or `gh run list -L 3`). A red or running CI stops the release here.

## 2. Collect what changed

`git log --oneline vLAST..HEAD` and the merged pull requests since the last tag. Read the last two changelog entries for tone and grouping.

## 3. Pick the version

SemVer: a breaking change for users bumps major (minor before 1.0), new capability bumps minor, fixes bump patch.

## 4. Write the changelog entry

[Keep a Changelog](https://keepachangelog.com/en/1.1.0/) under `## [Unreleased]`, grouped as Added, Changed, Fixed, Removed. Write for the people who use the project: name the feature, command or option, say what they can now do or what stopped breaking. Internal work (refactors, CI, tooling, the machines it was built on, who or what wrote it) stays out unless it changes behaviour for users. Match the earlier entries' tone and length.

## 5. Bump, commit, tag, push

If the repo has a release command (`release`, `scripts/release`, `make release`, `cargo release`, `npm version`), use it. Otherwise bump the version in every file that holds it (`pyproject.toml`, `Cargo.toml` and `Cargo.lock`, `package.json`, `CMakeLists.txt`, anything else the repo's CI checks), rename `## [Unreleased]` to the new version with the date in the format earlier entries use, commit `Release vX.Y.Z`, create tag `vX.Y.Z`, push the branch and the tag to every push remote.

A tag is never moved or force-pushed; a broken release gets a new patch.

## 6. Publish and verify

With a release workflow, CI publishes: wait for it. Without one, create the release from the changelog section:

```sh
awk -v v="## [X.Y.Z]" 'index($0, v) == 1 {p = 1; next} /^## \[/ {p = 0} p' CHANGELOG.md | gh release create vX.Y.Z --title vX.Y.Z --notes-file -
```

Done when the workflow is green, the release page shows the notes and every expected asset, and the registry lists the version (`pip index versions`, `npm view NAME version`, `cargo search NAME`, …).
