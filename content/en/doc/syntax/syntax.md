---
title: "Syntax: Variables"
subtitle: (Basic Syntax)
date: 2025-06-11 19:16:45
highlight: true
weight: 20
tags:
 - syntax
categories:
 - wiki
 - syntax
---

> Basic Syntax: Variable Types and Assignment

## I. Variables and Assignment

### 1. **Data Types**
#### Basic Types
  | Type       | Example                     |
  |-----------|--------------------------|
  | Variable     | `x`, `$a`                    |
  | Integer       | `42`, `-3`               |
  | Float       | `3.14`, `-0.5`, `10%`     |
  | String     | `"Hello\n"`, `'raw'`      |
  | Boolean     | `true`, `false`           |
  | List (Array)   | `[1, "a", true]`          |
  | Set   | `S{1, "a", true}`          |
  | Map (BtreeMap) | `{name: "Alice", age: 30}`|
  | Map (HashMap)  | `H{name: "Alice", age: 30}`|
  | Range       | `1..8`, `1..=10`          |
  | Regex       | `r'^\w+` |
  | Time       | `t'2025-5-20'` |
  | File Size | `4K`,`5T`  |
  | Null       | `none`                    |

#### Complex Types
  | Type       | Example                     |
  |-----------|--------------------------|
  | Function     | `fn add(x,y){return x+y}`    |
  | Lambda     | `(x,y) -> x + y`  |
  | Built-in Library  | `math.floor`  |

  **Scope Rules**
   - lambda, function functions create child environment scopes.
   - Child environment inherits parent environment variables, doesn't modify parent scope.

#### Strings
  - Single quotes are raw strings.

  - Double quotes support escaping:
    + Text escapes (like `\n`)
    + ANSI escapes (like `\033[34m`)
    + Unicode escapes (like `\u{2614}`)

  - Dot quotes are string templates:
    + Supports `$var` variable substitution
    + Supports `${expr}`/`{expr}` sub-expression execution capture
    + All escape characters.

  - `#wrap` string:
    r#' ... '#  no escaping
    r#" ... "#  only escapes quotes

  
     ```bash
     print "Hello\nworld!\u{2614}"
     Hello                           # outputs two lines, \n escaped to newline
     world!☔                         # unicode escape, \u{2614} is umbrella character

     let str2 = 'Hello\nworld!'
     Hello\nworld!                   # outputs one line, including raw \n form

     let a = [1,2,5]
     print `a is $a, and a[0] is ${a[0]}`

     print "\033[31mRed msg\033[m"   # outputs red Red msg
     ```


#### File Size Type:

  Constructed with integer followed by unit, supports these units:
  `"B" "K" "M" "G" "T" "P" `


  lumesh automatically recognizes units and outputs in human-readable format, for example:
  ```bash
  print 3050M 1038B 3000G

  # Output
  2.98G 1K 2.93T
  ```

  File size type can participate in operations.
  Example:
  ```bash
  1M > 30K      # returns true
  fs.ls -l | where(size>20K)   # filter files larger than 20K
  ```

#### Date/Time Type
Example: `t'2025-5-20'`


Date/time type can participate in operations.
Example:
```bash
t'2025-5-20' > t'2025-1-20'    # returns true
```
Specific operations please refer to built-in function's `time` module

#### Percentage

  Written as percentage, automatically recognized as float

  ```bash
  print 37% 2% + 3
  # Output
  0.37 3.02
  ```

  _percent sign tightly after number is percentage, space after number + % is modulo operation_

### 2. **Variable Declaration**
   - **Declare Variable**: Use `let` keyword, supports multiple variable declarations. Type automatically allocated based on assignment.
     ```bash
     let a             # declare
     let x = 10        # declare and assign
     ```

### 3. **Variable Assignment**

### Basic Assignment

```bash
# assignment
a = 3

# variable declaration and assignment
let x = 10
let name = "Lumesh"

# multi-variable assignment
let a, b, c = 1, 2, 3
let a, b, c = 1
```

Non-strict mode doesn't need declaration, direct assignment

*In strict mode, declaration is required and unique*

### Lazy Assignment

Lumesh's unique lazy assignment feature:

```bash
# use := for lazy assignment, expression doesn't execute immediately
let cmd := ls -l /tmp
let calculation := 2 + 3 * 4

# execute when needed
eval(cmd)           # execute ls -l /tmp
eval(calculation)   # calculate to 14

# if it's a command, can also execute directly
cmd
```

### Destructuring Assignment

Supports destructuring for arrays and maps:

```bash
# array destructuring
let [first, second, *rest] = [1, 2, 3, 4, 5]
# first = 1, second = 2, rest = [3, 4, 5]

# map destructuring
let {name, age} = {name: "Bob", age: 30, city: "NYC"}
# name = "Bob", age = 30

# rename destructuring
let {name: userName, age: userAge} = user_data
```

### 4. **Variable Usage**
Generally can directly use `a` or `$a`

*In strict mode only can use `$a`*
```bash
print $a
```

### 5. **Variable Deletion**
Use `del`.
```bash
del x
```

### 6. **Variable Type Detection**
Use `typeof`/`symof` function
```bash
let a = 10
typeof a                   # Integer
typeof a == "Integer"      # true
# type detection of expression before execution:
symof a+1                  # BinaryOp
```
