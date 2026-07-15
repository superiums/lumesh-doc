---
title: Hotkey Bindings
date: 2026-07-05 09:36:45
highlight: true
weight: 50
tags:
 - hotkey
categories:
 - wiki
 - hotkey
---

## Hotkey Binding Overview
![hot-key](images/key.svg)

### Default Emacs-style Bindings

#### Character Operations
| Hotkey | Function |
|--------|----------|
| Ctrl+A | Move to beginning of line |
| Ctrl+E | Move to end of line |
| Ctrl+B | Move left one character |
| Ctrl+F | Move right one character |
| Ctrl+H | Delete previous character |
| Ctrl+W | Delete previous word |
| Alt+D | Delete next word |
| Ctrl+U | Delete to beginning of line |
| Ctrl+K | Delete to end of line |
| Ctrl+L | Clear screen |
| Ctrl+N | Completion selection: next item |
| Ctrl+P | Completion selection: previous item |
| Ctrl+T | Swap characters |
| Ctrl+Y | Paste |
| Alt+B | Move left one word |
| Alt+F | Move right one word |

#### Function Operations
| Hotkey | Function |
|--------|----------|
| Ctrl+C | Interrupt (SIGINT) |
| Ctrl+D | EOF (when empty line) / Delete character |
| Tab | Trigger completion |
| Ctrl+R | History search |
| BackTab | Reverse completion |
| Escape | Cancel completion / Exit multi-line mode |
| Enter | Accept line / New line |

### Lumesh-specific Feature Bindings
- Alt+S | Toggle sudo command (configurable as doas, etc.)
- Space | Trigger abbreviation expansion
- **Alt+O** / **Alt+Enter** | **AI Replace**
- **Alt+I** | **AI Hint**

#### Hint Operations:
- Ctrl+J | Accept first word of hint (to special character)
- Alt+J | Accept first word of hint (to space or slash)

### Completion Mode Bindings
When completion list is displayed, enter completion selection mode:

| Hotkey | Function |
|--------|----------|
| Up / Ctrl+P | Previous completion item |
| Down / Ctrl+N / Tab | Next completion item |
| BackTab | Previous completion item |
| Enter / Space | Accept current completion |
| Escape | Cancel completion |
| Character keys | Continue input and filter completion (supports fuzzy search) |
| Backspace | Delete character and filter completion |

### History Search Mode Bindings
| Hotkey | Function |
|--------|----------|
| Ctrl+R | Trigger history search |

Completion mode hotkeys are also available in history search mode.

#### Multi-line Editing Mode Bindings
When input contains newline characters, enter multi-line editing mode:

| Hotkey | Function |
|--------|----------|
| Up | Move up one line |
| Down | Move down one line |
| Home | Move to beginning of current line |
| End | Move to end of current line |
| Escape | Exit multi-line mode |
| Enter | Accept line when empty, otherwise newline with preserved indentation |
| Ctrl+A | Move to beginning of paragraph |
| Ctrl+E | Move to end of paragraph |
| **Ctrl+O** | **Clear input** |

### Custom Editor Bindings

Custom Hotkeys
> Hotkeys are configured via LUME_HOT_BINDINGS:

| Hotkey | Function |
|--------|----------|
| Alt+q | Exit shell |
| Alt+h | History selector |
| Alt+x | Bookmark selector |
| Alt+m | Mark current command to bookmark |
| Alt+e | Edit command in external editor |

See configuration files for more details...

### Abbreviation Expansion
Input abbreviation and press Space to trigger expansion:

> Abbreviation expansion is customized via LUME_ABBREVIATIONS.

for example:

- xi	doas pacman -S
- xup	doas pacman -Syu
- xq	pacman -Q
- xs	pacman -Ss
- xr	doas pacman -Rs

