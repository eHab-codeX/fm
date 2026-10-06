

# fm

> A keyboard-driven terminal file manager built on `fzf`, `eza`, and Kitty's
> image protocol.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Bash 5+](https://img.shields.io/badge/bash-5%2B-4EAA25?logo=gnubash&logoColor=white)](https://www.gnu.org/software/bash/)
[![Platform: Linux](https://img.shields.io/badge/platform-Linux-FCC624?logo=linux&logoColor=black)](#platform-support)
[![PRs welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](#contributing)
[![lint](https://github.com/eHab-codeX/fm/actions/workflows/lint.yml/badge.svg)](https://github.com/eHab-codeX/fm/actions/workflows/lint.yml)

![fm demo](docs/demo.gif)

## Features

- 🗂️ **Fuzzy navigation** — `fzf`-driven file list with instant filtering
- 🖼️ **Inline previews** — images, videos, PDFs, and Markdown, rendered by Kitty
- 🖼️ **Gallery grid** — full-screen thumbnail browser with vim-style keys
- 🗑️ **Trash with undo** — `Ctrl-Z` walks back through your last 10 trash operations
- 🔍 **Content search** — `Ctrl-F` runs `ripgrep` on selected files
- 📝 **Bulk rename** — edit a list of names in `$EDITOR`, then commit
- 🌳 **Smart sorting** — by name, size, mtime, or extension
- 🧭 **Jump anywhere** — `zoxide` frecency, bookmarks, git mode
- ⚡ **Cached thumbnails** — invalidation via `mtime+size`, parallel workers
- 🎨 **Nerd Font icons** — via `eza`, with syntax highlighting via `bat`

## Install

### Manual

    git clone https://github.com/eHab-codeX/fm.git ~/.local/share/fm
    ln -sf ~/.local/share/fm/fm ~/.local/bin/fm

### Requirements

| Dep                            | Purpose                          | Required    |
| ------------------------------ | -------------------------------- | ----------- |
| `bash` >= 5                    | Uses `EPOCHREALTIME`, `${var,,}` | ✅          |
| `fzf`                          | The picker itself                | ✅          |
| `eza`                          | Icons + colored listing          | ✅          |
| `ripgrep`                      | `Ctrl-F` content search          | ✅          |
| `trash-cli`                    | `Ctrl-D` / undo stack            | ✅          |
| `kitty` + `kitten`             | Inline previews, gallery grid    | recommended |
| `bat`                          | Syntax highlighting              | recommended |
| `imagemagick`                  | Contact sheets, MD → PNG         | recommended |
| `fd`                           | Faster file discovery            | optional    |
| `pdftoppm` (poppler)           | PDF thumbnails                   | optional    |
| `ffmpeg` / `ffmpegthumbnailer` | Video thumbnails                 | optional    |
| `glow` or `mdcat`              | Rendered Markdown previews       | optional    |
| `zoxide`                       | `Alt-Z` frecency jump            | optional    |

Arch one-liner:

    sudo pacman -S fzf eza ripgrep trash-cli bat imagemagick fd poppler ffmpegthumbnailer glow zoxide

### Platform support

Primary target is Linux. `fm` also runs on macOS and the BSDs, but a few
helpers prefer GNU coreutils. Where GNU-only flags are used, `fm` falls
back to a portable implementation — the thumbnail prewarming path, for
example, tries GNU `head`, then `ghead`, then pure Bash.

On macOS:

    brew install bash fzf eza ripgrep trash coreutils fd poppler imagemagick ffmpeg glow zoxide

On FreeBSD:

    pkg install bash fzf eza ripgrep trash-cli coreutils fd-find poppler-utils ImageMagick7 ffmpeg glow zoxide

The Bash fallbacks mean `fm` still works if you skip `coreutils`, at the
cost of slightly slower prewarming.

## Keybindings

Press <kbd>F1</kbd> inside `fm` for the same list. All bindings work in a
standard terminal; some mouse actions require Kitty.

### File list

| Key                                         | Action                                       |
| ------------------------------------------- | -------------------------------------------- |
| <kbd>Enter</kbd>                            | Open file / enter directory (on `..`: go up) |
| <kbd>Tab</kbd>                              | Multi-select                                 |
| <kbd>Esc</kbd>                              | Quit                                         |
| <kbd>F1</kbd> / <kbd>Alt</kbd>+<kbd>H</kbd> | Help                                         |

### Actions

| Key                                                        | Action                                      |
| ---------------------------------------------------------- | ------------------------------------------- |
| <kbd>Ctrl</kbd>+<kbd>D</kbd> / <kbd>Alt</kbd>+<kbd>D</kbd> | Move selection to trash                     |
| <kbd>Ctrl</kbd>+<kbd>Z</kbd>                               | Undo last trash batch (pops the undo stack) |
| <kbd>Alt</kbd>+<kbd>T</kbd>                                | Open trash browser                          |
| <kbd>Ctrl</kbd>+<kbd>Y</kbd>                               | Copy absolute path(s) to clipboard          |
| <kbd>Ctrl</kbd>+<kbd>O</kbd>                               | Force open externally (`xdg-open`)          |
| <kbd>Ctrl</kbd>+<kbd>N</kbd>                               | New file (end with `/` for a directory)     |
| <kbd>Ctrl</kbd>+<kbd>E</kbd>                               | Rename selected item                        |
| <kbd>Alt</kbd>+<kbd>M</kbd> / <kbd>Ctrl</kbd>+<kbd>W</kbd> | Bulk rename via `$EDITOR`                   |
| <kbd>Ctrl</kbd>+<kbd>K</kbd>                               | Move selection to a destination             |
| <kbd>Ctrl</kbd>+<kbd>P</kbd>                               | Copy selection to a destination             |
| <kbd>Ctrl</kbd>+<kbd>L</kbd>                               | Create symlink(s) to selection              |
| <kbd>Alt</kbd>+<kbd>I</kbd>                                | Show file info (`stat`, `file`, `exiftool`) |

### Search & jump

| Key                          | Action                                                            |
| ---------------------------- | ----------------------------------------------------------------- |
| <kbd>Ctrl</kbd>+<kbd>F</kbd> | Search inside selected file(s) with `ripgrep`                     |
| <kbd>Alt</kbd>+<kbd>Z</kbd>  | Zoxide jump (frecency-ranked directories)                         |
| <kbd>Ctrl</kbd>+<kbd>B</kbd> | Bookmarks (Enter: jump/add, <kbd>Ctrl</kbd>+<kbd>X</kbd>: delete) |
| `fm <query>`                 | Shell-side: jump to best zoxide match                             |

### View

| Key                                                        | Action                                 |
| ---------------------------------------------------------- | -------------------------------------- |
| <kbd>Ctrl</kbd>+<kbd>S</kbd>                               | Cycle sort: name → size → mtime → ext  |
| <kbd>Ctrl</kbd>+<kbd>R</kbd>                               | Toggle recursive listing (depth 1 ↔ 5) |
| <kbd>Ctrl</kbd>+<kbd>H</kbd> / <kbd>Alt</kbd>+<kbd>.</kbd> | Toggle hidden files                    |
| <kbd>Ctrl</kbd>+<kbd>G</kbd>                               | Git mode (modified/untracked files)    |
| <kbd>Ctrl</kbd>+<kbd>T</kbd>                               | Toggle contact-sheet preview           |
| <kbd>Alt</kbd>+<kbd>J</kbd> / <kbd>Alt</kbd>+<kbd>K</kbd>  | Next / previous page of contact sheet  |
| <kbd>Alt</kbd>+<kbd>N</kbd> / <kbd>Alt</kbd>+<kbd>P</kbd>  | Same as above                          |
| <kbd>Alt</kbd>+<kbd>G</kbd>                                | Open full-screen gallery grid          |

### Preview pane

| Key                           | Action                            |
| ----------------------------- | --------------------------------- |
| <kbd>Ctrl</kbd>+<kbd>V</kbd>  | Scroll text preview one page down |
| <kbd>Ctrl</kbd>+<kbd>U</kbd>  | Scroll text preview one page up   |
| <kbd>F3</kbd> / <kbd>F2</kbd> | Same as above (backup keys)       |

> Contact sheets are a single image — use <kbd>Alt</kbd>+<kbd>J</kbd> /
> <kbd>Alt</kbd>+<kbd>K</kbd> to page them, not the scroll keys.

### Trash browser (<kbd>Alt</kbd>+<kbd>T</kbd>)

| Key                          | Action                                   |
| ---------------------------- | ---------------------------------------- |
| <kbd>Enter</kbd>             | Restore selected item(s)                 |
| <kbd>Ctrl</kbd>+<kbd>D</kbd> | Permanently delete (asks to confirm)     |
| <kbd>Ctrl</kbd>+<kbd>Y</kbd> | Copy original path(s) to clipboard       |
| <kbd>Ctrl</kbd>+<kbd>F</kbd> | Search inside all trashed files          |
| <kbd>Ctrl</kbd>+<kbd>X</kbd> | Empty the entire trash (asks to confirm) |

### Gallery grid (<kbd>Alt</kbd>+<kbd>G</kbd>)

| Key                                                                                                       | Action                                   |
| --------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| <kbd>←</kbd> <kbd>↓</kbd> <kbd>↑</kbd> <kbd>→</kbd> / <kbd>H</kbd> <kbd>J</kbd> <kbd>K</kbd> <kbd>L</kbd> | Move selection                           |
| <kbd>PgUp</kbd> / <kbd>PgDn</kbd>                                                                         | Move one screen                          |
| <kbd>g</kbd> / <kbd>G</kbd>                                                                               | First / last                             |
| Mouse wheel                                                                                               | Scroll rows                              |
| Left click                                                                                                | Select item                              |
| Double click                                                                                              | Open (folder: enter / file: full screen) |
| <kbd>Enter</kbd>                                                                                          | Folder: enter · File: full-screen view   |
| <kbd>Backspace</kbd> / <kbd>-</kbd>                                                                       | Parent directory                         |
| <kbd>Space</kbd>                                                                                          | Mark / unmark                            |
| <kbd>a</kbd> / <kbd>A</kbd>                                                                               | Mark-all visible / Unmark-all            |
| <kbd>o</kbd>                                                                                              | Open marked (or selected) externally     |
| <kbd>y</kbd>                                                                                              | Copy absolute path(s)                    |
| <kbd>x</kbd> / <kbd>Delete</kbd>                                                                          | Move to trash (asks to confirm)          |
| <kbd>s</kbd>                                                                                              | Cycle sort: name / mtime / size          |
| <kbd>+</kbd> / <kbd>=</kbd> · <kbd>_</kbd>                                                                | Bigger / smaller thumbnails              |
| <kbd>r</kbd>                                                                                              | Reload                                   |
| <kbd>f</kbd>                                                                                              | Leave gallery, focus file in file list   |
| <kbd>?</kbd>                                                                                              | Help                                     |
| <kbd>q</kbd> / <kbd>Esc</kbd>                                                                             | Close gallery                            |

### Mouse

| Action       | Where      | Effect                                   |
| ------------ | ---------- | ---------------------------------------- |
| Double-click | File list  | Open file / enter directory              |
| Right-click  | File list  | Go to parent directory                   |
| Wheel        | File list  | Scroll list (or preview, if hovering it) |
| Double-click | Any picker | Select                                   |
| Right-click  | Any picker | Cancel                                   |

> **Note:** <kbd>Backspace</kbd> can't go up in the file list — `fzf` uses
> it for editing the query. Use the `..` entry, or <kbd>Backspace</kbd> in
> the gallery grid.

## Contributing

Issues and PRs welcome. Before opening a PR:

1. Run `shellcheck fm` — CI does too.
2. Keep changes to the existing style (2-space indent, `_helper_name` for internal functions).
3. Test on Bash 5+.

## License

MIT © eHab-codeX
FM_README_END
