---
name: verify-ui
description: Verify a visible change by looking at it: screenshots of web pages, desktop GUI apps and terminal UIs at the sizes and themes that matter, before/after comparison, console errors. Use after changing anything a user sees, for a reported visual bug, or "check how it looks", "take a screenshot".
---

# Verify UI

Code that compiles can still look broken. A change counts as verified only once you have looked at it.

## 1. Pick the surface

| Surface           | With [agent-tools](https://github.com/MattFor/agent-tools)                                              | Without                                                                           |
| ----------------- | ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| Web page          | `webshot URL out.png [--size WxH] [--mobile] [--dark] [--full] [--click CSS] [--wait-for CSS]`          | Playwright, or `chrome-headless-shell --screenshot=out.png --window-size=W,H URL` |
| Desktop GUI (X11) | `eval "$(xlab up 1280x800)"`, start the app, `xlab win PAT`, `xlab input …`, `xlab shot -w PAT out.png` | `Xvfb :99` + a window manager, `xdotool`, `import -window ID out.png`             |
| Terminal UI       | `tui start NAME -- CMD`, `tui keys NAME …`, `tui screen NAME`                                           | `tmux -L test new -d -x 120 -y 40 CMD`, `send-keys`, `capture-pane -p`            |

Run apps on a private display or tmux server, never the user's own, and send their sound to a null sink.

## 2. Capture the before state

When fixing or restyling something, capture the same views from the unchanged code first (a `git worktree` of the base commit, or a stash). Without a before shot there is nothing to compare.

## 3. Capture the matrix

Every view the change touches, at each size and theme the project supports:

- Web: desktop 1440x900, phone (`--mobile`), dark mode if the site has one, `--full` for long pages, plus the state after the interaction you changed (`--click`, hover via `--eval`).
- GUI: the main window and every dialog or panel you changed, at the default size and at a small one.
- TUI: 80x24 and a wide terminal.

Done when every view in the matrix has an image file.

## 4. Look at every image

Open each one (read the PNG as an image) and check: clipping and overflow, alignment and spacing against neighbouring elements, truncated text, contrast in dark mode, focus and hover states, empty and error states. Zoom into the changed region when it is small: `magick in.png -crop WxH+X+Y +repage -scale 300% zoom.png`.

For web pages, console errors and uncaught exceptions must be zero, or each one explained.

## 5. Compare before and after

`magick compare -metric AE before.png after.png diff.png` counts changed pixels and paints them in `diff.png`. Changes outside the area you meant to touch are regressions until shown otherwise. Side by side for the report: `montage before.png after.png -tile 2x -geometry +4+4 pair.png`.

## 6. Report

List each view with its screenshot path and what you checked. Done when every view in the matrix was captured and looked at, the comparison shows only intended changes, and every started server, display and tmux session is stopped.
