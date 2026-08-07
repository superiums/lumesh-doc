---
title: Key Bindings
date: 2026-07-05 09:36:45
highlight: true
weight: 50
tags:
  - hotkey
  - bindings
  - slash cmd
categories:
  - wiki
  - hotkey
---

## Key Bindings Overview
![hot-key](images/key_cn.svg)

### Default Emacs-style Bindings

#### Character Operations

| Shortcut | Function |
|----------|----------|
| Ctrl+A | Move to beginning of line |
| Ctrl+E | Move to end of line |
| Ctrl+B | Move one character left |
| Ctrl+F | Move one character right |
| Ctrl+H | Delete previous character |
| Ctrl+W | Delete previous word |
| Alt+D | Delete next word |
| Ctrl+U | Delete to beginning of line |
| Ctrl+K | Delete to end of line |
| Ctrl+L | Clear screen |
| Ctrl+N | Completion choice: next |
| Ctrl+P | Completion choice: previous |
| Ctrl+T | Swap characters |
| Ctrl+Y | Paste |
| Alt+B | Move one word left |
| Alt+F | Move one word right |

#### Functional Operations

| Shortcut | Function |
|----------|----------|
| Ctrl+C | Interrupt (SIGINT) |
| Ctrl+D | EOF (when empty line) / Delete character |
| Tab | Trigger completion |
| Ctrl+R | History search |
| BackTab | Reverse completion |
| Escape | Cancel completion / Exit multi-line mode |
| Enter | Accept line / New line |

### Lumesh Special Features

- Alt+S | Toggle sudo command (configurable as doas, etc.)
- Space | Trigger abbreviation expansion
- **Alt+O** / **Alt+Enter** | **AI Replace**
- **Alt+I** | **AI Prompt**

#### Hint Operations:
- Ctrl+J | Accept hint's first word (up to special character)
- Alt+J | Accept hint's first word (up to space or slash)

### Completion Mode Bindings

When completion list is displayed, enter completion selection mode:

| Shortcut | Function |
|----------|----------|
| Up / Ctrl+P | Previous completion |
| Down / Ctrl+N / Tab | Next completion |
| BackTab | Previous completion |
| Enter / Space | Accept current completion |
| Escape | Cancel completion |
| Character key | Continue input and filter completion (supports fuzzy search) |
| Backspace | Delete character and filter completion |


### History Search Mode Bindings

| Shortcut | Function |
|----------|----------|
| Ctrl+R | Trigger history search |

Shortcut keys in completion mode are available in history search mode.

#### Multi-line Editing Mode Bindings

When input contains newline, enter multi-line editing mode:

| Shortcut | Function |
|----------|----------|
| Up | Move up one line |
| Down | Move down one line |
| Home | Move to beginning of current line |
| End | Move to end of current line |
| Escape | Exit multi-line mode |
| Enter | Accept line when empty, otherwise new line and maintain indentation |

- Ctrl+A | Move to beginning of line
- Ctrl+E | Move to end of line
- **Ctrl+O** | Clear input



### Custom Editor Bindings

Custom hotkeys

> Hotkeys configured via `LUME_HOT_BINDINGS`:

Default configuration:

| Shortcut | Function |
|----------|----------|
| Alt+q | Exit shell |
| Alt+h | History selector |
| Alt+x | Bookmark selector |
| Alt+m | Mark current command to bookmark |
| Alt+e | Edit command in external editor |

See configuration file for more details...

> In configuration, each key corresponds to a function, which must have **one parameter** to receive the input content in the current line. If return value is **string**, it will be used to replace current input line.

### Abbreviation Expansion

Expand after typing abbreviation and pressing space:

> Abbreviation expansion is customized via `LUME_ABBREVIATIONS`.

For example:

- xi | doas pacman -S

### / Commands

The following built-in / commands can be configured via `LUME_SLASH_BINDINGS`:

No parameters:
- `/` menu, will start function defined in `LUME_SLASH_MENU`
- `/q` exit

With one parameter:
- `/ <some_query>` Quick fuzzy directory jump, similar to zoxide's z
- `/history [prefix]` Print history (in input order)
- `/h [prefix]` Select and execute history command (by weight order)
- `/hh [prefix]` Select and execute history command for current directory only
- `/hm [prefix]` Select and execute history command for multiple directories

> / commands execute after Enter, so they can receive a string parameter, return value will be discarded.

> Each binding corresponds to a function, which must have **one parameter**, return value will be ignored.