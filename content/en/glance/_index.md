---
title: Quick Overview
date: 2026-07-11 19:16:45
highlight: true
weight: 1
tags:
 - glance
categories:
 - wiki
 - why
---


### Interaction

![demo](images/demo.gif)


### Syntax Highlighting

![highlight](images/highlight.gif)

- Auto-completion
   * Command completion/hints
   * Parameter completion/hints
   * Path completion
   * History command completion/hints
   * Built-in library completion/hints, parameter hints
   * AI completion hints (alt+i)

| ![complete](images/completion.gif) | ![complete](images/ai.gif) |
|------------------------|------------------------|


- Detailed error messages

|          Syntax Error          |         Runtime Error          |
|------------------------|------------------------|
| ![err](images/err.gif) | ![err runtime](images/err_runtime.gif) |


### Key Bindings

#### Shortcuts, customizable functions to modify input line
```bash
set LUME_HOT_BINDINGS = {
    CTRL_q: 'exit',
    ALT_m: save_cmdmark,
    CTRL_SHIFT_M: select_cmdmark,
    CTRL_SHIFT_D: select_dirmark,
    ALT_e: fix_typos,
    CTRL_SHIFT_t: timestamp,
    'CTRL_/': menu,
}
```

#### Slash Commands
1. Built-in/Command:
- `/` Command menu
- `/ <query>` Quick fuzzy directory jump, similar to zoxide's z command
- `/h [prefix]` History command fuzzy search
![slash cmd](images/bindings.gif)

2. Supports custom /xxx commands
```bash
let open_file = (b) -> {fd -t file | ui.pick('select file:') ?! | xdg-open -- _}

set LUME_SLASH_BINDINGS = {
    sm: save_cmdmark,
    sd: save_dirmark,
    m: select_cmdmark,
    d: select_dirmark,
    g: fuzzy_go,
    o: open_file,
    e: edit_file,
    sc: search_content,
    cm: git_commit,
}
```


### Performance

 * Memory Usage

|          lume          |         fish           |
|------------------------|------------------------|
| ![mem_lume](images/mem_lume.png) | ![mem_fish](images/mem_fish.png) |
|          bash          |         dash           |
| ![mem_bash](images/mem_bash.png) | ![mem_dash](images/mem_dash.png) |


 * Loop Test

|          lume          |         fish           |
|------------------------|------------------------|
| ![time_lume](images/time_lume.png) | ![time_fish](images/time_fish.png) |
|          bash          |         dash           |
| ![time_bash](images/time_bash.png) | ![time_dash](images/time_dash.png) |

_fish failed to complete one million iterations_


 * Software Package Size (after installation)

|         |    lume       |     bash      |     dash      |     fish      |
|---------|---------------|---------------|---------------|---------------|
| Version    |    v0.16.10     |    v5.2.037   |    v0.5.12    |   v4.0.2      |
| Size    |    3.97 MB     |    9.2 MB     |    153.8 KiB  |   21.64 MB    |


* Test Results

| ![highlight](images/mem_chart.svg) | ![highlight](images/time_chart.svg) |
|------------------------|------------------------|

_Because fish failed to complete one million tasks, we use the time it takes to complete half the tasks_

### Syntax

Friendly and easy to use syntax.

- Direct mathematical computation

```bash
 6 / 3
 5 - 1
 1+2^3*2
```

- Variables
```bash
let a = (2+3)*5
print "a=" a
print `a={a}`

let b,c = 1,2     # Supports multi-variable assignment
println b c
println(b,c)      # Also works
```

- Arrays/Lists
```bash
let arr = [10, "a", true]
let arr_b = 0...10

arr[0]       # → 10

# Slicing operations
arr[1..3]     # → ["a", true] (left-closed, right-open)
arr[:2]     # → [10, true] (stride 2)
arr[-1..]      # → true (slicing supports negative indices)

# Complex nesting
[1,24,5,[5,6,8]][3][1]     # displays 6
```

- Maps/Dictionaries
```bash
let dict = {name: "Lume", age: 2}

# Basic access
dict.name
dict[name]
```

- If Condition Statements
```bash
if a>0 && b==0 {
   print OK
}else{
   print BAD
}
```

- Match Statement
```bash
match a {
 10 => print "ten",
 20 => print "twenty"
 _ => print other
}
```

- Other Statements
Supports *for, while, loop* and other loop statements.
Conditional assignment statement *a?b:c*
Lazy assignment statement `let a := b + c`

- Lambda Expressions
```bash
let addone = x -> x+1
let add = (x,y) -> x+y

addone(2)
add(3,4)
```

- Functions

  > Supports higher-order functions
  > Supports function nesting
  > Supports default parameters
  > Supports variadic parameter collection

```bash
fn add(x,y=0,*other){     # = provides default parameters, * collects remaining parameters
 x+y+len(other)
}

add(3)
add(3,4)
add(3,4,5,6,7)
```

- Error Catch

```bash
let e = x -> print x

3 / 0 ?: e # You can use functions to catch and handle errors
3 / 0 ?: 0 # Provide a default value on failure

3 / 0 ?. # You can also choose to ignore it.
3 / 0 ?? # You can choose to display it on error output.
3 / 0 ?+ # You can choose to merge it to standard output.
3 / 0 ?! # You can choose to use the error as a result, useful for error redirection

```

Error catching can be used in expressions and entire function declarations.

- Smart Pipes

**Smart pipes** can automatically adapt input and output formats, intelligently supporting byte pipes and *structured data pipes*.

> Structured pipe example:

```bash

ls -l | into.table() | where( int C4 > 4000 )            # Use system ls command

let thead = [mode,i,user,group,size,mday,mtime,name]
ls -l | into.table(thead)  | where(int size > 400) | select([name,size,mtime])   # Custom conversion header

fs.ls -lh | where(size > 3M) | select(name)              # Use built-in ls command
```

[Get Started](../doc/quickstart)

## Manuals

 - [Syntax Manual](../doc/syntax)
 - [Built-in Library Functions](../doc/libs/)
 - [Key Bindings](../doc/keys)
 - [Bash Syntax Comparison](../glance/bash)

## Test Scripts

 - [syntax-test](https://codeberg.org/santo/lumesh/raw/branch/main/src/tests/op-test.lm)
 - [benchmark-test](https://codeberg.org/santo/lumesh/raw/branch/main/src/tests/benchmark.lm)
 - [benchmark-bash-test](https://codeberg.org/santo/lumesh/raw/branch/main/src/tests/benchmark.sh)
