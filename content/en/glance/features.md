---
title: What is Lume, Is It Worth Trying?
date: 2026-07-31 14:31:00
highlight: true
weight: 2
tags:
 - feature
categories:
 - wiki
 - feature
---

One-sentence summary:
> **Lume (Lumesh) is a modern Shell and scripting language that writes like JS, uses like Bash, and runs as fast as light**, designed to replace Bash and tackle all the historical baggage and counter-intuitive designs.

## Highlights (Compared to Bash)

| Pain Point | Bash Issues | Lume Solution |
|---|---|---|
| **Syntax** | Space-sensitive, `[ ]`/`[[ ]]`/`(( ))` confusion, no spaces in assignments | Optional spaces, `==` for unified type comparison, no bracket magic |
| **Type System** | Everything is string, arithmetic requires `$(( ))`, no floats, no error on overflow | Complete types: integers/floats/arrays/maps/ranges/regex/timestamps/file sizes, direct writing |
| **Strings** | Implicit splitting + glob expansion easily "mines", nested interpolation escape hell | Three string types (single-quoted raw/double-quoted escaped/backticked template interpolation), `StringSafe` type prevents injection |
| **Data Structures** | Array/associative array syntax cumbersome, no nested access | Intuitive array/map literals, supports `select` (like SQL) and `get` dot-path access to nested structures |
| **Flow Control** | `case` pitfalls, only wildcard matching | `match` supports multiple values/ranges/regex; `if`/`for` can return expression values |
| **Functions** | Only positional parameters, return values must be 0-255 exit codes | Named parameters, defaults, variadic, arbitrary return values, Lambda/closures/currying, decorators |
| **Scope** | Variables default to global pollution | Functions default to isolated scope, modify parent explicitly with `set` |
| **Pipes/Subprocesses** | Right side runs in subshell, variable changes lost; only text streams | Four types of pipes (standard/positional/distributed/PTY), structured data (List/Map) flows directly, no subprocesses |
| **Error Handling** | Relies on exit codes, need manual `set -euo pipefail` | Compiler-level error hints + 7 error capture operators (`?.` `?:` `?+` `??` `?>` `?!` `?~`) |
| **Modularization** | `source` global namespace pollution, no module system | `use ... as` module imports + 17 built-in modules loaded on-demand (`list`/`string`/`fs`/`time`/`math`/`regex`/`ui`, etc.) |
| **Performance** | ~2200ms for 1M iterations | ~200ms, 10x faster |

## More Features

- **Modern Interactive Experience**: abbreviation expansion, programmable hotkeys/slash commands, programmable prompt, syntax highlighting themes, auto-completion, and **AI assistant** for command completion and intelligent suggestions.
- **Automatic Background Task Cleanup**: background tasks automatically terminate when main process exits, no `trap` needed.
- **Cross-platform**: consistent experience anywhere.
- **Daily Habit Compatibility**: provides CFM (Command-First Mode) for everyday CLI scenarios, compatible with simple `include` inline syntax.

## Who Should Use Lume?

- People tired of Bash quote hell, space traps, and implicit variable expansion pitfalls.
- Those who need to handle structured data (JSON/tables) in Shell scripts without switching to Python.
- Anyone wanting a fast-start, high-performance loop, with modern programming language features (Lambda/closures/decorators/pattern matching) for daily interactive Shell.

## Getting Started

Provides two forms: `lume` (full REPL Shell) and `lume-se` (lightweight script executor), supporting source compilation, `cargo install`, precompiled packages, or installation scripts.

Overall, Lume's design philosophy is "safe defaults, explicit is better than implicit," contrasting sharply with Bash's "everything is text, everything is command" philosophy — if you believe "correct writing should also be the most natural writing," Lume is worth trying.