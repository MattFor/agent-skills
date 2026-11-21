---
name: tracker
description: Read and update the user's project tracker and todo list through the `tracker` CLI (simple-project-tracker, alias `t`). Use when they mention their projects, todos, tasks, what they are working on, statuses or notes, e.g. "add a todo", "mark 3 done", "what's on my list", "set myapp to blocked".
---

# tracker

`tracker` ([simple-project-tracker](https://github.com/MattFor/simple-project-tracker)) is the user's project tracker: tracked projects (each with a status and a note) plus a separate todo list. Run it through the shell; its output is plain text when piped. If `command -v tracker` finds nothing, tell the user it isn't installed rather than guessing.

## Look

| Want                  | Command                                                                                                                                              |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| All projects          | `tracker list all` (bare `tracker` and every `list` apply `display.filter`, which hides some statuses; `tracker settings get filter` shows which)    |
| Search projects       | `tracker list <search>` · by status: `tracker <status>` (e.g. `tracker stable`), since `tracker list s:<status>` finds nothing for a filtered status |
| One project in detail | `tracker <project> check`                                                                                                                            |
| Statuses in use       | `tracker status`                                                                                                                                     |
| The whole todo list   | `tracker todo`                                                                                                                                       |
| Open todos only       | `tracker todo list s:todo`                                                                                                                           |
| Todos about something | `tracker todo <word>` (matches names), or read the full list                                                                                         |
| Totals                | `tracker stats`                                                                                                                                      |
| How a command works   | `tracker help <command>` (topics: `selection`, `statuses`, `todo list`)                                                                              |

## Change

| Want                       | Command                                                                                                                  |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Add a todo                 | `tracker todo add <what to do>` · with fields: `tracker todo add <name> s <status> n <note>`                             |
| Finish / reopen            | `tracker todo <n> done` · `tracker todo <n> reopen`                                                                      |
| Note on a todo             | `tracker todo <n> note <text>` (`@project` links a project)                                                              |
| Rename / status            | `tracker todo <n> rename <text>` · `tracker todo <n> status <value>`                                                     |
| Remove / clear finished    | `tracker todo remove <n>` · `tracker todo clear`                                                                         |
| Track / stop tracking      | `tracker add <path> [status] [note]` · `tracker remove <project>` (files untouched)                                      |
| Project note / status      | `tracker <project> note <text>` · `tracker <project> status <value>` · all of one status: `tracker status s:<old> <new>` |
| Duplicate or moved entries | `tracker doctor` reports, `tracker fix` repairs (one undoable change)                                                    |
| Take back the last change  | `tracker todo undo` for todos · `tracker undo` for projects (separate histories)                                         |

Projects and todos are picked by number (the row number from the last listing), name or part of it, a range (`3-5`, `2+3`), or `s:<status>`. When a name matches several, the error lists them: pick with `:<row>` or the permanent `i:<id>`.

## Rules

- List first, so the row numbers you use are current, and show the list again after a change so the user sees the result.
- Each undo reverts exactly one change: `tracker todo undo` for the todo list, `tracker undo` for projects. They are separate, so `tracker undo` after a todo change touches a project instead. Use the right one at once if a change hit the wrong row.
- Removing projects or clearing todos loses data: confirm with the user first unless they asked for exactly that.
- Commands that ask first (`tracker fix`, `tracker todo clear`, removing several rows) refuse without a terminal ("pass --yes"). Add `--yes` once the user has agreed.
- Go through the CLI only, never the data files. `[ERROR] ... was not migrated (... refused ...)` means an old `.pkl` held something other than plain data, possibly planted: tell the user and never load it yourself.
