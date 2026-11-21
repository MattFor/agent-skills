# Agent Skills

[![CI](https://github.com/MattFor/agent-skills/actions/workflows/ci.yml/badge.svg)](https://github.com/MattFor/agent-skills/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

Agent skills for UI verification, releases, dependency updates, disk cleanup, logo design, orchestrating other coding agents and a CLI project tracker.

## Features

| Skill                                                      | What it does                                                                                                                                                                                        |
| ---------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`dependency-updates`](skills/dependency-updates/SKILL.md) | Lists Dependabot and Renovate pull requests across repos, merges the green ones, fixes or reports the failing ones.                                                                                 |
| [`disk-cleanup`](skills/disk-cleanup/SKILL.md)             | Frees disk space safely: measures, separates regenerable build output and caches from user data, deletes only what regenerates or what you approve.                                                 |
| [`logo-design`](skills/logo-design/SKILL.md)               | Brief to production files: concepts, clean geometric SVG, testing at 16 px and in one colour, presentation boards, export variants, and a searchable library of 1,400+ classified real-world logos. |
| [`orchestrator`](skills/orchestrator/SKILL.md)             | Hands the work to other coding agents on the machine (Codex, Cursor, Copilot, local Ollama models, headless Claude Code), then verifies what they did.                                              |
| [`release`](skills/release/SKILL.md)                       | Cuts a release: SemVer bump, a changelog entry written for users, version files, tag, CI publishing, and a check of the release page and registry.                                                  |
| [`tracker`](skills/tracker/SKILL.md)                       | Reads and updates a [simple-project-tracker](https://github.com/MattFor/simple-project-tracker) project list and todo list.                                                                         |
| [`verify-ui`](skills/verify-ui/SKILL.md)                   | Verifies visible changes by looking at them: screenshots of web pages, desktop apps and terminal UIs across sizes and themes, before/after diffs, console errors.                                   |

Each skill is a plain [`SKILL.md`](https://agentskills.io) folder, so any agent that reads skills can use it. The
scripts are dependency-free Python 3 and POSIX shell. `verify-ui`, `disk-cleanup`, `release` and
`dependency-updates` use [agent-tools](https://github.com/MattFor/agent-tools) when it is installed and plain
commands otherwise.

## Requirements

- `logo-design`: Python 3.11+. PNG export uses `rsvg-convert`, Inkscape or CairoSVG when one is installed.
- `orchestrator`: at least one of `codex`, `cursor-agent`, `copilot`, `claude`, or `codex` with Ollama.
- `tracker`: the `tracker` CLI from simple-project-tracker.
- `dependency-updates`: the GitHub CLI (`gh`), logged in.
- `verify-ui`, `disk-cleanup`, `release`: nothing beyond the tools of the project at hand; ImageMagick for image comparison.

## Install

Any agent, with the [`skills`](https://github.com/vercel-labs/skills) CLI:

```sh
npx skills add MattFor/agent-skills
```

Claude Code, as a plugin:

```text
/plugin marketplace add MattFor/agent-skills
/plugin install agent-skills@mattfor
```

By hand: clone the repo and link each folder under `skills/` into your agent's personal skills folder,
`~/.claude/skills` (Claude Code, also read by Cursor), `~/.codex/skills` (Codex) or `~/.copilot/skills`
(Copilot CLI).

## Usage

Skills load on their own when a request matches their description ("design a logo for…", "orchestrate
this", "add a todo"). In Claude Code they are also slash commands, such as `/verify-ui` or `/release`.

<!-- TODO(user): Disclosure section, if you want one. -->

## License

MIT, see [LICENSE](LICENSE). By [MattFor](https://github.com/MattFor). The logo library's SVGs are CC0 from
gilbarbara/logos, see [its notice](skills/logo-design/assets/library/NOTICE.md).
