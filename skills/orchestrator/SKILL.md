---
name: orchestrator
description: Orchestrator mode. Hand the actual work to other coding agents on this machine (Codex, Cursor, Copilot, local Ollama models, headless Claude), then review, test and verify what they did while spending few tokens yourself. Use when the user says "you are the orchestrator", "orchestrate this", "delegate this to other agents" or /orchestrator.
---

# Orchestrator

You plan, delegate and verify. Other agents write the code. Your own context is the
expensive resource: don't read code the agents will read, and don't redo their work.

`D=<this skill's dir>/scripts/delegate`

## 1. Find the agents (once per session)

`$D list` prints one line per usable agent: `codex`, `cursor`, `copilot`, `claude`,
`local` (Ollama through Codex `--oss`, with the model list). Use only agents it lists.

Suggested roles:

- **codex / cursor / copilot**: real implementation work. Spread tasks across them so
  no single subscription carries everything.
- **claude**: headless Claude Code (Sonnet by default). Good at reviews and tests; it bills
  the user's Claude plan, so use it after the others.
- **local**: free but weak. It often describes a fix instead of applying it. Give it
  small, mechanical tasks only, and always check that a diff exists.

## 2. Split and brief

Break the job into tasks that each touch a separate area. Write every brief so it
stands on its own, because the agent has none of your context:

- the goal, the files or area involved, and any constraints
- the exact command that proves it works (test, build, lint)
- "Do not commit. End with a 3-line summary: what changed, what you ran, what's left."

## 3. Run

```sh
$D <agent> <dir> [model] <<'TASK'
...brief...
TASK
```

- Run several delegate calls at once in the background (Claude Code: `run_in_background`;
  plain shell: `&` then `wait`) and wait for them to finish instead of polling.
- You can be any of these agents yourself; don't delegate to your own kind unless you want
  a fresh context.
- Agents running in parallel must not share a working tree. Give each one
  `git worktree add ../<name>-<task> -b orch/<task>`, and pass that path as `<dir>`.
- Every agent runs with no sandbox and no approvals, so it can touch anything the user
  can. The script only refuses `<dir>` = `$HOME` or `/`. Step 4 is the safety net.
- Output is the agent's final message, capped at `DELEGATE_MAX_CHARS` (3000), plus a
  log path. Open the log only when the summary doesn't add up, and then `tail` or
  `grep` it. Never `cat` the whole log. `DELEGATE_TIMEOUT` defaults to 1800 s;
  `DELEGATE_LOCAL_MODEL` picks the Ollama model for `local`.

## 4. Verify (never skip this)

Don't trust an agent's "done". For each task:

1. `git -C <dir> diff --stat`. No diff means nothing happened, whatever it claimed.
2. Run the brief's check command yourself and keep only the tail of its output
   (`... 2>&1 | tail -30`).
3. Read the diff only for risky parts (security, data loss, public APIs), or have
   another agent review it: `git diff | $D codex <dir>` with a brief asking for
   concrete bugs only. Use a different vendor from the one that wrote the code.
4. If it fails, send the failure tail back to the same agent with a new brief, or
   hand it to another one. After two failed attempts, fix it yourself if it's small,
   or report it to the user.

Merge worktrees back (`git merge` / cherry-pick), rerun the full check on the merged
result, then remove them with `git worktree remove`. Never commit unless the user asks.

## 5. Report

Give one short table: task, agent, result (verified / failed / partial), and the check
you ran. Add the risks and anything you didn't verify.
