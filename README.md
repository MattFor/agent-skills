# Agent Skills

[![CI](https://github.com/MattFor/agent-skills/actions/workflows/ci.yml/badge.svg)](https://github.com/MattFor/agent-skills/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

Agent skills for logo design, orchestrating other coding agents and a CLI project tracker.

## Features

| Skill                                         | What it does                                                                                                                                         |
| --------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`logo-design`](skills/logo-design/SKILL.md)   | Brief to production files: concepts, clean geometric SVG, testing at 16 px and in one colour, presentation boards, export variants, and a searchable library of 1,400+ classified real-world logos. |
| [`orchestrator`](skills/orchestrator/SKILL.md) | Hands the work to other coding agents on the machine (Codex, Cursor, Copilot, local Ollama models, headless Claude Code), then verifies what they did. |
| [`tracker`](skills/tracker/SKILL.md)           | Reads and updates a [simple-project-tracker](https://github.com/MattFor/simple-project-tracker) project list and todo list.                            |

Each skill is a plain [`SKILL.md`](https://agentskills.io) folder, so any agent that reads skills can use it. The
scripts are dependency-free Python 3 and POSIX shell.

## Requirements

- `logo-design`: Python 3.11+. PNG export uses `rsvg-convert`, Inkscape or CairoSVG when one is installed.
- `orchestrator`: at least one of `codex`, `cursor-agent`, `copilot`, `claude`, or `codex` with Ollama.
- `tracker`: the `tracker` CLI from simple-project-tracker.

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
this", "add a todo"). In Claude Code they are also slash commands: `/logo-design`, `/orchestrator`, `/tracker`.

<!-- TODO(user): Disclosure section, if you want one. -->

## License

MIT, see [LICENSE](LICENSE). By [MattFor](https://github.com/MattFor). The logo library's SVGs are CC0 from
gilbarbara/logos, see [its notice](skills/logo-design/assets/library/NOTICE.md).
