---
title: Syntax Comparison between Lumesh and Bash
date: 2025-06-11 19:16:45
weight: 4
highlight: true
tags:
 - glance
 - bash
categories:
 - wiki
 - bash
---

1. Basic Concept Comparison
--------

| Concept      | Bash Syntax        | Lumesh Syntax                 | Description                      |
|--------------|--------------------|-------------------------------|-----------------------------------|
| Interpreter Declaration | `#!/bin/bash` | `#!/usr/bin/env lumesh` | Lumesh recommends calling via env |
| Comments     | `# comment text`   | `# comment text`              | Same as Bash                      |
| Continuation Character | `\` | `\` | Same as Bash, but continuation characters are not needed within strings |

2. Variable Operations Comparison
--------

| Operation         | Bash Syntax             | Lumesh Syntax                   | Notes                       |
|--------------|-----------------------|-------------------------------|----------------------------|
| Variable declaration     | `var=value`           | `let var = value`             | Spaces optional, required in strict mode |
| Variable reference     | `"$var"` or `"${var}"`    | `$var` or directly `var`           | $ optional, required in strict mode |
| Multi-variable declaration | Not supported | `let a, b = 1, 2`             |                  |
| Delete variable     | `unset var`           | `del var`                     |                    |
| Delayed assignment     | Not supported                | `x := 2 + 3`                  |                    |
| Type checking     | Not supported                | `typeof var == "Integer"`  |                    |
| Destructuring assignment     | Not supported                | `let [a,b] = arr`    |                    |

Bash variables typically require double quotes to avoid syntax errors caused by empty values.

3. Data Types Comparison
--------

| Type         | Bash handling                | Lumesh syntax                     | Notes                       |
|--------------|------------------------------|---------------------------------|----------------------------|
| Integer | `declare -i var=10` | `let var = 10` | Automatically assigned type |
| Floating point | Not supported | `let var = 10.0` | |
| String | `str="hello"` | `let str = "hello"` | |
| Array | `arr=(1 2 3)` | `let arr = [1, 2, 3]` or `1...=3` | |
| Dictionary | `declare -A dict=([k]=v)` | `let dict = {k: v}` | |
| Range | `{1..10}` | `1..10` or `1..=10` | Basic data type, can participate in operations |
| File size | String | `200M` | Basic data type, can participate in operations |
| Time | String | `r'2025-10-1'` | Basic data type, can participate in operations |

4. Operators Comparison
-------

| Operator       | Bash Syntax                     | Lumesh Syntax                     | Description                       |
|--------------|---------------------------------|-----------------------------------|-----------------------------------|
| Arithmetic     | `$((a + b))`                 | `a + b`                         | Direct computation                 |
| String concatenation | `"$a$b"`                     | `$a$b` or `a + b`             | String.format can also be used    |
| Equality comparison | `[ "$a" == "$b" ]`           | `a == b` or `a ~= b`            | Cross-type comparison ~=          |
| Contains check | `[[ "str" =~ "pattern" ]]`    | `str ~: 'pattern'`             | Contains operations can be used for arrays, ranges, dictionaries, strings |
| Regular expression matching | `[[ "str" =~ "regex" ]]`      | `str ~: r'regex'`               | Regex library is available         |

Bash comparisons require double quotes to avoid syntax errors from null values.

5. Flow Control Comparison
--------

| Structure         | Bash Syntax                     | Lumesh Syntax                     | Description               |
|--------------|---------------------------------|-----------------------------------|---------------------------|
| if statements  | `if [ cond ]; then ... fi`    | `if cond {...} else {...}`      |  More concise              |
| for loops      | `for i in {1..3}; do ... done` | `for i in 1..3 {...}`          |  More concise             |
| while loops    | `while [ cond ]; do ... done`      | `while cond {...}`              |   More concise            |
| case statements    | `case $var in; a) a_cmd;; ... esac`   | `match var { a => a_cmd...}`     | Match statement is more concise, supports regex |
| *repeat loops*  | No direct equivalent                   | `repeat 10 {...}`               | Repeat loop, alias for list.map |
| *each loops*     | No direct equivalent                   |  `each {} [1..3]               |  Alias for list.map         |

6. Function Comparison
------

| Feature | Bash Syntax | Lumesh Syntax | Description |
|----------|--------------|----------------|----------------|
| Function Definition | `func() { cmds; }` | `fn func() {...}` | |
| Anonymous Function | Not supported | `let f = (x,y) -> x + y` | lambda expression |
| Function Parameters | Not supported | `fn func(a,b) { a,b...}` | Parameter list |
| Default Parameters | Not supported | `fn f(a=1) {...}` | Default parameters |
| Remaining Parameters | Not supported | `fn f(*args) {...}` | Collect remaining arguments |
| Function Call | `func a1` | `func(a1)` or `func! a1` | |
| *Higher-order Function* | Not directly supported | `funcA(funcB)` | Function as argument |
| Scope | Global scope only | Functions have isolated scope | |

Bash does not have function parameters, can only use positional parameters `$1`..., if positional parameters are passed, they will override script-level parameters.

7. Script Arguments Comparison
------

| Feature         | Bash Syntax                     | Lumesh Syntax                     | Description                       |
|------------------|----------------------------------|------------------------------------|------------------------------------|
| Arguments         | `"$1"` `"$2"` ...              | `argv[0]` `argv[1]`                |                                    |
| All arguments     | `"$@"`                         | `argv`                            |                                    |
| Argument count    | `"$#"`                         | `argv.len()`                      |                                    |

8. Command Comparison
--------

| Operation | Bash Syntax | Lumesh Syntax | Description |
|-----------|-------------|---------------|-------------|
| Command execution | `cmd` | `cmd` | |
| Run in background | `cmd &` | `cmd &` | |
| Redirect output | `cmd >/dev/null` | `cmd &-` | |
| Redirect error | `cmd 2>/dev/null` | `cmd &?` | |
| Pipe | `cmd1 \| cmd2` | `cmd1 \| cmd2` | Same as Bash, but supports structured data flow |
| Append redirect | `cmd >> file` | `cmd >> file` | Same as Bash |
| Override redirect | `cmd > file` | `cmd >! file` | |

VIII. Advanced Features Comparison
--------

| Feature         | Bash Support                 | Lumesh Support                 | Notes                       |
|-----------------|------------------------------|--------------------------------|----------------------------|
| Implicit Type Conversion | None | Automatic conversion of numbers and strings | |
| Operator Overloading | None | Supports arithmetic operations (+, -, *, /) for multiple types | |
| Matrix Operations | None | Supports matrix multiplication | |
| Error Messages | Rough | Detailed | Will show line numbers, context, and error type |
| Error Handling | Only `$?` status code detection | Supports exception catching, ignoring, printing, etc. | More convenient exception handling mechanism |
| Interactive Mode | Supported | Syntax highlighting, auto-completion, AI-assisted, key bindings | Enhanced REPL interactive mode |
| *Built-in Libraries* | None | Built-in a large number of utility functions | See [Libs](/zh-cn/doc/libs) |
| *Structured Pipelines* | None | Supports convenient data processing like `into.\| where \| select` | |

## IX. Migration Recommendations
------

1.  **Variable Declaration**: Keep `var=value` as-is; strict mode should change first declaration to `let var = value`
2.  **Condition Check**: Change from `[ "$a" == "$b" ]` to `a == b` or `$a == $b`
3.  **Loop Statement**: Change from `for i in {1..10}` to `for i in 1..10`
4.  **Function Definition**: Change from `func() {...}` to `fn func() {...}`
5.  **Error Handling**: Keep `cmd || fallback` as-is or change to `cmd ?: fallback`
6.  **Condition Execution**: Keep `cmd1 && cmd2 || fallback` as-is or change to `cmd1 &: cmd2 ?: fallback`

# X. Notes
------

1.  Array indices in Lumesh start from 0 (same as Bash)
2.  Range expression `a..b` does not include b, `a..=b` includes b
3.  Function calls need `()` or `!` suffix
4.  In strict mode, variables must be prefixed with `$`
5.  Operator precedence may differ from Bash

XI. Learning Resources
-------

1. Official Documentation: [Lumesh Syntax Manual](../doc)
2. Basic Syntax: [Lumesh Syntax and Whitespace Rules](../doc/symbo)
3. Detailed Comparison: [Bash Life Journey](../topics/bashlife)
<!--2. Interactive Tutorial: Run `lumesh` to enter REPL mode for practice-->
<!-- 3. Example Scripts: View the `/usr/share/lumesh/examples` directory -->
