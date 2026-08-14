---
title: Latest Updates
date: 2025-06-11 19:16:45
highlight: true
weight: 90
tags:
 - news
categories:
 - topics
 - news
---

# Recent Lumesh Version Updates (0.16.13 → 0.17.5)

Following the previous report (0.14.x–0.16.12: editor rewrite, tokenizer refactoring, History/Slash Commands, AI integration), this issue focuses on the language core layer—**deep refactoring of the string/quoting system**, **introduction of the `Bytes` type**, **establishment of the background task management system**, and a series of performance refinements throughout.

---

### String and Quoting Semantics: Major Change This Issue

This is the most significant set of changes in this release, evolved over three versions (0.16.13 → 0.17.0 → 0.17.5), finally converging into a complete and self-consistent escaping semantics system:

- **0.16.13**: `rewrite unescape for all string`—Rewrote the escaping logic for all string types, paving the way for refined quotation semantics in subsequent versions.
- **0.17.0**: **Introduced `StringSafe` (`s'...'`)**, designed specifically for command concatenation scenarios, eliminating injection risks at the language level:
  ```bash
  let s = s'(rm -rf /)'
  let s2 = into.safe 'mkfs /dev/sda'
  eval_str(`echo $s $s2`)  # never unsafe eval
  ```
  In the same release, **introduced `SymbolRaw`**, extending the `^` escape character's scope from command positions to argument positions.
- **0.17.5**: **Introduced hashed raw quotes (`#`-wrapped syntax)**, formally establishing a multi-level escaping gradient:
  ```rust
  r#'....'#        // No escaping at all
  r##'....'#′      // Supports any number of # (for content containing #)

  r#"...."#        // Escapes quotes, but not ANSI and unicode
  t#"...."#        # Prefixes g/t/s/b are not escaped
  ```
  **Breaking Change**: `r'....'` no longer carries the responsibility as a regex prefix, now narrowed to a pure **raw string** semantics; the original **regex prefix is now renamed to `g'....'`**.

  This design cleverly resolves the natural conflict between "boundary character and content containing quotes" using the `#` separator, while decoupling "whether to escape ANSI/unicode" from "whether to escape quotes" into two independent, combinable dimensions—ultimately presenting a complete semantic gradient from `"..."` (fully escaped) through `r#"..."#` (half-escaped, quotes not escaped, ANSI/unicode still escaped) to `r#'...'#` (fully unescaped), with clear hierarchy and distinct responsibilities.

---

### `Bytes` Type Introduction (0.17.0)

- Literal support: `b'\x41'`
- Supports slicing, comparison, operator overloading
- Supports printing, piping, and use as command arguments
- **0.17.5**: Syntax further enriched with `b"..."` (double-quoted form) and `b#"..."#` (`#`-wrapped form), maintaining consistent writing style with the string system's escaping semantics.

---

### Enhanced Numeric Literals (0.17.0)

- Introduced **Radix numbers**: `0b100_100`, `0o170`, `0xff`
- All numeric types now uniformly support `_` separators: `999_999`, `999_999.999_999`
- **0.17.5**: Radix number parsing performance further optimized.

---

### Background Tasks and Terminal Integration (from 0.17.3)

- **0.17.3**: Added `jobs` command for unified background task management.
- **0.17.5**:
  - PTY (pseudo-terminal) performance and stability optimizations
  - Fixed display residue issues when piping output to `vi`
  - Added `Ctrl+Z` support for non-PTY scenarios

---

### Grammar/Tokenizer Refinement (0.17.3)

- Adjusted categorization for custom unary operators
- Improved tokenization precision for `.`, `^`, `+`
- Removed index operator `@`
- Unified prefix marker tokenization logic for `-`/`!`
- Improved `<<`, added byte stream support
- **`?&` renamed to `&:``, continuing the short-circuit logic writing style introduced in 0.16.2
- **`let` default CFM disabled**: Prevents statements like `let a=1` from being mistakenly parsed as commands by CFM, while preserving idiomatic CFM styles like `dd if=/dev/sda`
- Improved `^` and `:` whitespace detection before/after
- Fixed `symof`: `assert(symof(1+2), 'BinaryOp')`

---

### Standard Library Enhancement (0.17.3)

- `fs.ls` supports directly specifying files (`fs.ls file(s)`)
- `log` supports write to file, print timestamp
- `pprint` supports group print of table/list
- improve `group` of `table` and `list`
- Built-in libraries support wildcard expansion

### Prompt Integration (0.17.3)
- Integrated with starship, improving shell prompt customizability

---

### Version Rhythm Summary

| Phase | Core Direction |
|------|----------|
| 0.16.13 | Complete string escaping logic rewrite, CFM/custom operator fixes |
| 0.17.0 | `Bytes` type, `StringSafe`/`SymbolRaw`, Radix numbers, function renaming (Breaking Change) |
| 0.17.1–0.17.2 | Library functions/prompt optimizations, `cfm auto` mode, `&:` short-circuit syntax |
| 0.17.3 | `jobs` background task management, starship integration, tokenizer refinement (Breaking Change: `?&` → `&:`) |
| 0.17.5 | **hashed raw quote system implementation** (`r#'..'#`/`r#"..."#`/`g#'..'#` etc.), PTY/signal processing optimizations (**Breaking Change**: `r'..'` semantics narrowed to raw string, regex changed to `g'..'`) |

---

## Recent Lumesh Version Updates (0.14.x → 0.16.x)

### Editor Complete Rewrite (0.15.0)

0.15.0 is the most important milestone recently: **migration from rustyline to crossterm, editor rewritten from scratch**. Subsequent versions continued refinement:

- **0.15.1**: Editor themes, buffer optimization, custom hotkey bindings
- **0.15.2**: Multi-line editing mode, input validation, cursor and prompt position fixes
- **0.16.0**: Fixed ONLCR issues, path completion sorting optimization
- **0.16.2**: Added `ui.editor`, `ui.date_pick`, fixed CapsLock recognition, hotkey modifier mapping bugs
- **0.16.10**: Editor supports scrolling

---

### Tokenizer Refactoring (from 0.15.5)

- **0.15.5**: **Refactored tokenizer, introduced dispatch mechanism**, prioritizing mark parsing for clearer, more maintainable logic
- **0.15.6**: Improved CFM (Command First Mode) symbol handling, unified highlighting logic
- **0.15.7**: Fixed trailing `&` tokenization issues
- **0.16.2**: Fixed module call tokenizer
- **0.16.7**: Enhanced CFM mode, takes whole word (avoiding `1.1.1.1` being misparsed as float)
- **0.16.8**: Switched to static regex for improved tokenization performance

---

### History System Continuous Evolution

- **0.16.5**: Added history hints (history hint), ESC moved to end of multi-line mode and clears hints
- **0.16.8**: **Introduced slash commands** (`/h`, `/hh`, `/hm` and other slash command system)
- **0.16.9**: Smarter history weighting and sorting, added `/q` quick exit command
- **0.16.10**:
  - Optimized `Ctrl+R` display of long history records
  - Multi-line commands automatically ignore `/h...` history records (avoiding screen flooding)
  - `/h`, `/hh`, `/hm`, `/history` support prefix filtering

---

### Completion System Major Enhancement (0.15.3–0.16.3)

- Path completion, external command completion, parameter-aware completion gradually refined
- Supports Lumesh scripts as completion data source
- Completion colors and context-aware
- `ui.pick`/`ui.multi_pick` support `table`/`map` type inputs
- `ui.float` supports custom decimal places

---

### AI Integration Deepening

- **0.15.9**: `ALT+i` triggers AI prompt
- **0.16.0**: `ALT+Enter` / `ALT+o` triggers AI generation
- **0.16.3**: Default AI-TLS enabled, separated AI-HTTPS as optional feature
- **0.16.8**: Updated AI skill configuration
- **0.16.10**: Updated AI documentation

---

### Language Features

- **0.14.0**: Introduced `table` expression and built-in `table` library
- **0.14.3**: Improved quoting semantics (`''` raw string / `""` normal escaped / `` ` `` fully escaped + variable interpolation)
- **0.16.5**: Added `continue` statement; `match` arrow supports line breaks; **Breaking Change: only single values allowed in declarations**
- **0.16.7**: `~` auto-expands in symbols, prefix matching support in normal mode

---

### Standard Library Structure Reorganization (0.16.10)

Module responsibilities reorganized:

| Change | Description |
|------|------|
| `fs.dirs` → `sys` | Directory-related functions moved to system library |
| `sys.print_tty` / `sys.discard` → `console` | Terminal output control moved to console library |
| Deleted `sys.cds` | Cleaned up redundant interfaces |
| Fixed float filesize display | Float `filesize` formatting fix |

---

### Summary by Phase

| Phase | Core Direction |
|------|----------|
| 0.14.x | Language feature refinement (table, quoting, bug fixes) |
| 0.15.x | Editor rewrite + tokenizer refactoring + completion system construction |
| 0.16.x | Slash commands system + History intelligence + AI integration + module reorganization |
