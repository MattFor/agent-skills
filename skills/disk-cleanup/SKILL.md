---
name: disk-cleanup
description: Free disk space safely: measure what is big, sort it into regenerable (build output, caches, virtual environments, trash) and user data, delete what regenerates, ask before touching anything else, report what was reclaimed. Use for "clean up", "free space", "disk is full", "remove old builds/backups".
---

# Disk cleanup

Deleting is the one step that cannot be undone, so every deletion is either regenerable or approved.

## 1. Measure

`df -h` for the filesystems involved, then the biggest items: `reclaim [PATH]` from [agent-tools](https://github.com/MattFor/agent-tools) when installed, else `du -xh --max-depth=2 PATH | sort -h | tail -40`. Add `docker system df`, `podman system df`, `journalctl --disk-usage`, `flatpak uninstall --unused` (dry answers only) when those exist. Done when you have a ranked list with sizes and ages.

## 2. Classify

- **Regenerable**: build output (`target/`, `node_modules/`, `dist/`, `build/`), virtual environments, language caches (`__pycache__`, `.ruff_cache`, `~/.cache/*`, package-manager caches), trash, dangling container images. Regenerates with one command.
- **Redundant**: old backups, duplicate downloads, rotated logs, superseded installers. Usually safe, but someone made them on purpose.
- **User data**: everything else, including anything you cannot place.

## 3. Check before deleting

- A build dir of a project with work in progress still regenerates; a venv with hand-installed packages may not (`pip freeze` it first).
- Nothing in use: no running process has its working directory, files or containers there.
- Redundant and user-data items go to the user as a table (path, size, age, why it looks removable) and wait for a yes on each line or group.

## 4. Delete

Use the owning tool when there is one (`cargo clean`, `docker image prune`, `flatpak uninstall --unused`), literal absolute paths otherwise (`rm -rf /exact/path`), and `gio trash` for approved user data when available. Never delete `.git` directories or anything outside the approved list.

## 5. Report

`df -h` again. Done when the report shows space before and after, every deleted item with its size, and what was kept and why.
