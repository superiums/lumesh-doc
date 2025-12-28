---
title: Lumesh Syntax Features Introduction
type: glance
date: 2025-07-05 19:16:45
highlight: true
tags:
 - glance
categories:
 - wiki
 - why
 - syntax
---

Lumesh is a modern shell and scripting language that implements complex expression parsing using a Pratt parser.

### Unique Syntax Features

#### 1. Multiple Pipeline Operators
Unlike traditional shells, Lumesh provides various types of pipelines:

```bash
cmd1 | cmd2      # Standard pipeline, transferring structured data or text streams
cmd1 |_ cmd2     # Positional pipeline, transferring to specified parameter positions
cmd1 |> cmd2     # Single dispatch pipeline, looping through list data
cmd1 |^ cmd2     # PTY pipeline
```
Structured pipelines:
```bash
ls -l | Into.table() | where(size > 5K)
Fs.ls -l | where(size > 5K) | select(name,size,modified)
ls -1 |> adb push _ /sdcard/Download/
```

#### 2. Error Handling Operators
Lumesh has a rich built-in error handling mechanism:

```bash
command ?.        # Ignore error
command ?: e      # Error capture or default value
command ?+        # Print to standard output
command ??        # Print to error output
command ?>        # Override print (data channel)
command ?!        # Terminate on error (terminate pipeline)
```

#### 3. Delayed Assignment
Use `:=` for delayed assignment; the expression will not execute immediately:

```bash
let cmd := ls -l    # Delayed execution
```

#### 4. Chained Calls
Supports chained method calls similar to object-oriented languages:

```bash
"hello world".split(' ').join(',')
data | .filter(x -> x > 0)
```

#### 5. Destructuring Assignment
Supports destructuring assignment for arrays and maps:

```bash
let [a, b, c] = [1, 2, 3]
let {name, age} = user_data
```

#### 6. Range Operators
Provides various range operators:

```bash
1..10      # Inclusive of end
1..<10     # Exclusive of end
1..10:2    # With step
1...10     # Directly create an array
```

#### 7. Function Decorators
Supports function decorator syntax:

```bash
@decorator_name
@decorator_with_args(param1, param2)
fn my_function() { ... }
```

#### 8. Pattern Matching
Built-in powerful pattern matching capabilities, supporting regular expressions:

```bash
match value {
    pattern1 => result1,
    pattern2 => result2,
    _ => default
}
```

#### 9. Overloaded Operators
Utilizes regular arithmetic operators to handle more common tasks:
```bash
"1" + [2,3]
[1,2] + 3
[1,2] + [3,4,5]
{a:b} + c
{a:b} + {c:d}
```

#### 10. Functional Programming

```bash
0...10 | List.filter(x -> x % 2 == 0)
0..10 | .map(x -> x * 2)
```

Lumesh includes a wealth of practical function libraries to facilitate convenient functional programming, such as:
- **Collection Operations**: `List.reduce, List.map`
- **File System**: `Fs.ls, Fs.read, Fs.write`
- **String Handling**: `String.split, String.join`, regex module, formatting module
- **Time Operations**: `Time.now, Time.format`
- **Data Conversion**: Into module, From module
- **Mathematical Calculations**: Complete mathematical function library
- **Logging**: Log module
- **UI Operations**: `Ui.pick, Ui.confirm`

### Control Flow Structures

```bash
# Conditional statements
if condition { action } else { alternative }
match expr { a => branchA; ... }
# Loop statements
for item in collection { process(item) }
while condition { body }
loop { infinite_loop }
# Simple repetition
repeat 10 {a += 1}
```

### Expression Precedence
Lumesh uses a precisely defined operator precedence system:

- Assignment operators (`=`, `:=`, `+=`, etc.) - Priority 1
- Redirection and pipeline (`|`, `>>`, `>!`) - Priority 2
- Error handling (`?.`, `?+`, `??`) - Priority 3
- Lambda expressions (`->`) - Priority 4
- ...

### Data Types

#### Strings
Supports three types of strings:
- Double-quoted strings: `"hello world"`
- Raw strings: `'raw string'`
- Template strings: `` `template $variable` ``

#### Collection Types

```bash
[1, 2, 3]                    # Array
{key: value, name: "test"}   # Map
1..10                        # Range
1...10                       # Array
```

#### Functions
- Supports parameter collection and default parameters
- Supports lambda functions
- Supports nested functions
```bash
fn add(x) { x + 1 }
(x, y) -> x + y
```

### Module System

Supports module import and usage:

```bash
use module_name

Fs.ls("/path")      # Use built-in module
```

## Notes

The syntax design of Lumesh integrates features of modern programming languages with the practicality of shell scripting. Its unique pipeline operators, error handling mechanisms, and chained call syntax are the biggest differences from traditional shells.

The parser uses the Pratt algorithm to support complex expression nesting and precedence handling.

Learn more:
- [Lume vs Bash, ppt](/rv/en.html)
- [Syntax Overview (superiums/lumesh)](/glance)
- [Feature Overview (superiums/lumesh)](/feature)
- [Application Examples (superiums/lumesh)](/cases)
- [Syntax Demonstration for Writing lf Configuration Files in Lumesh](/cases/case_lf)
- [Syntax Manual (superiums/lumesh)](/doc/syntax)
- [Built-in Functions (superiums/lumesh)](/doc/libs/)
