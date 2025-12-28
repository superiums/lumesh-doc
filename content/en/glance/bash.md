---
title: Lumesh vs Bash Syntax Comparison
date: 2025-06-11 19:16:45
type: compare
highlight: true
tags:
 - glance
 - bash
categories:
 - wiki
 - bash
---

## [watch ppt demo](/rv/en.html)

1. Basic Concept Comparison
--------

| Concept       | Bash Syntax           | Lumesh Syntax                | Description                       |
|---------------|-----------------------|------------------------------|-----------------------------------|
| Interpreter Declaration | `#!/bin/bash`       | `#!/usr/bin/env lumesh`    | Lumesh recommends using the env method to call |
| Comment       | `# comment content`   | `# comment content`          | Same as Bash                     |
| Line Continuation | `\`                 | `\`                          | Same as Bash, but no continuation character needed inside strings |

2. Variable Operation Comparison
--------

| Operation     | Bash Syntax           | Lumesh Syntax                | Description                       |
|---------------|-----------------------|------------------------------|-----------------------------------|
| Variable Declaration | `var=value`           | `let var = value`             | Spaces are optional; must declare in strict mode |
| Variable Reference | `$var` or `${var}`    | `$var` or directly `var`      | `$` is optional; must use `$` in strict mode |
| Multiple Variable Declaration | Not supported        | `let a, b = 1, 2`             |                                   |
| Delete Variable | `unset var`           | `del var`                     |                                   |
| Lazy Assignment | Not supported          | `x := 2 + 3`                  |                                   |
| Type Check    | Not supported          | `type var == "Integer"`       |                                   |

3. Data Type Comparison
--------

| Type          | Bash Handling          | Lumesh Syntax                | Description                       |
|---------------|------------------------|------------------------------|-----------------------------------|
| Integer       | `declare -i var=10`    | `let var = 10`               | Automatically assigns type        |
| Float         | Not supported          | `let var = 10.0`             |                                   |
| String        | `str="hello"`          | `let str = "hello"`          |                                   |
| Array         | `arr=(1 2 3)`          | `let arr = [1, 2, 3]` or `1...<3` |                                   |
| Dictionary    | `declare -A dict=([k]=v)` | `let dict = {k: v}`          |                                   |
| Range         | `{1..10}`              | `1..10` or `1..<10`          | Basic data type, can participate in calculations |
| File Size     | String                 | `200M`                        | Basic data type, can participate in calculations |
| Time          | String                 | `Fs.parse('2025-10-1')`    | Basic data type, can participate in calculations |

4. Operator Comparison
-------

| Operator      | Bash Syntax            | Lumesh Syntax                | Description                       |
|---------------|------------------------|------------------------------|-----------------------------------|
| Arithmetic    | `$((a + b))`          | `a + b`                      | Direct calculation                |
| String Concatenation | `"$a$b"`          | `a + b` or `format("a={}",a)` | `format` is an alias for `String.format` |
| Equality Check | `[ "$a" == "$b" ]`    | `a == b` or `a ~= b`         | Cross-type comparison with `~=`  |
| Inclusion Check | `[[ "str" =~ "pattern" ]]` | `str ~: 'pattern'`          | Inclusion operation can be used for arrays, ranges, dictionaries, and strings |
| Regex Match   | `[[ "str" =~ "regex" ]]` | `str ~~ 'regex'`            | Has regex library available       |

5. Control Flow Comparison
--------

| Structure      | Bash Syntax            | Lumesh Syntax                | Description                       |
|----------------|------------------------|------------------------------|-----------------------------------|
| if Statement    | `if [ cond ]; then ... fi` | `if cond {...} else {...}`  |                                   |
| for Loop       | `for i in {1..3}; do ... done` | `for i in 1..3 {...}`      |                                   |
| while Loop     | `while [ cond ]; do ...` | `while cond {...}`          |                                   |
| case Statement  | `case $var in ... esac` | `match var {...}`           | Match statement                  |
| *repeat Loop*   | No direct equivalent   | `repeat 10 {...}`           | Repeat loop, an alias for `List.map` |
| *each Loop*     | No direct equivalent   | `each {} [1..3]`            | An alias for `List.map`          |

6. Function Comparison
------

| Feature        | Bash Syntax            | Lumesh Syntax                | Description                       |
|----------------|------------------------|------------------------------|-----------------------------------|
| Function Definition | `func() { cmds; }`   | `fn func() {...}`            |                                   |
| Anonymous Function | None                 | `let f = (x,y) -> x + y`    | Lambda expression                 |
| Function Parameters | `func() { $1,$2... }` | `fn func(a,b) { a,b...}`    | Parameter list                    |
| Default Parameters | Not supported        | `fn f(a=1) {...}`           | Default parameters                 |
| Rest Parameters | `"$@"`                | `fn f(*args) {...}`         | Collects rest parameters           |
| Function Call   | `func a1`             | `func(a1)` or `func! a1`    |                                   |
| *Higher-order Function* | Not directly supported | `funcA(funcB)`             | Function as a parameter           |
| Scope          | Only supports global scope | Functions have isolated scope |                                   |

7. System Command Comparison
--------

| Operation      | Bash Syntax            | Lumesh Syntax                | Description                       |
|----------------|------------------------|------------------------------|-----------------------------------|
| Command Execution | `cmd`                | `cmd`                        |                                   |
| Background Run  | `cmd &`                | `cmd &`                      |                                   |
| Close Output    | `cmd >/dev/null`      | `cmd &-`                     |                                   |
| Close Error     | `cmd 2>/dev/null`     | `cmd &?`                     |                                   |
| Pipe            | `cmd1 | cmd2`         | `cmd1 | cmd2`                | Same as Bash, but supports structured data streams |
| Redirect Append  | `cmd >> file`         | `cmd >> file`               | Same as Bash                      |
| Redirect Overwrite | `cmd > file`       | `cmd >! file`              |                                   |

8. Advanced Feature Comparison
--------

| Feature        | Bash Support           | Lumesh Support               | Description                       |
|----------------|------------------------|------------------------------|-----------------------------------|
| Implicit Type Conversion | No              | Automatically converts numbers and strings |                                   |
| Operator Overloading | No                | Supports various types of addition, subtraction, multiplication, and division |                                   |
| Matrix Operations | No                   | Supports matrix multiplication |                                   |
| Error Messages  | Rough                  | Detailed                      | Will provide line numbers, context, and error types |
| Error Handling   | Only `$?` status code detection | Supports exception capture, ignore, print, etc. | More convenient exception handling mechanism |
| Interactive Mode | Supported             | Syntax highlighting, auto-completion, AI assistance, key bindings | Enhanced REPL interactive mode |
| *Built-in Libs*  | No                   | Built-in many useful functions | See [Libs](libs_cn.md)           |
| *Structured Pipeline* | No               | Supports `parse | where | select` and other convenient data processing | |

9. Migration Suggestions
------

1.  **Variable Declaration**: Keep `var=value` as is; in strict mode, change the first declaration to `let var = value`.
2.  **Conditional Judgment**: Change from `[ "$a" == "$b" ]` to `a == b` or `$a == $b`.
3.  **Loop Statements**: Change from `for i in {1..10}` to `for i in 1..10`.
4.  **Function Definition**: Change from `func() {...}` to `fn func() {...}`.
5.  **Error Handling**: Keep `cmd || fallback` as is or change to `cmd ?: fallback`.

10. Notes
------

1.  Lumesh's array indexing starts from 0 (same as Bash).
2.  The range expression `a..<b` does not include `b`, while `a..b` includes `b`.
3.  Function calls require `()` or `!` suffix.
4.  In strict mode, the `$` prefix must be used to reference variables.
5.  Operator precedence differs from Bash; it is recommended to use parentheses to clarify precedence.

11. Learning Resources
-------

1.  Official Documentation: [Lumesh Syntax Manual](/doc/syntax)
2.  Interactive Tutorial: Run `lumesh` to enter REPL mode for practice.
<!-- 3.  Example Scripts: Check the `/usr/share/lumesh/examples` directory -->
