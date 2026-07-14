---
title: Variables
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
  |------------|-----------------------------|
  | Variable   | `x`, `$a`                   |
  | Integer    | `42`, `-3`                  |
  | Float      | `3.14`, `-0.5`, `10%`       |
  | String     | `"Hello\n"`, `'raw'`        |
  | Boolean    | `True`, `False`             |
  | List (Array)| `[1, "a", True]`           |
  | Map (Dictionary)| `{name: "Alice", age: 30}`|
  | Range      | `1..8`, `1..<10`            |
  | Regex      | `r'^\w+'`                   |
  | Time       | `t'2025-5-20'`              |
  | File Size  | `4K`, `5T`                  |
  | Null       | `None`                      |

#### Complex Types
  | Type       | Example                     |
  |------------|-----------------------------|
  | Function   | `fn add(x,y){return x+y}`  |
  | Lambda     | `(x,y) -> x + y`           |
  | Built-in Library | `Math.floor`          |

  **Scope Rules**
   - Lambda and function definitions create a child environment scope.
   - Child environments inherit parent environment variables without modifying the parent scope.

#### Strings
  - Single quotes denote raw strings.
  
  - Double quotes support escaping:
    + Text escape (e.g., `\n`)
    + Unicode escape (e.g., `\u{2614}`)
    + ANSI escape (e.g., `\033[34m`)
    
  - Backtick quotes denote string templates:
    + Support for `$var` variable substitution
    + Support for `${cmd arg}` sub-statement execution capture
    + ANSI escape characters.

     ```bash
     print "Hello\nworld!\u{2614}"
     Hello                           # Outputs two lines, \n is escaped to a newline
     world!☔                         # Unicode escape, \u{2614} is the umbrella character

     let str2 = 'Hello\nworld!'
     Hello\nworld!                   # Outputs one line, including the raw form of \n

     let a = [1,2,5]
     print `a is $a, and a[0] is ${a[0]}`

     print "\033[31mRed msg\033[m"   # Outputs red "Red msg"
     ```

     _Variable substitution within backtick quotes is more efficient than sub-statement capture; it is recommended to use the former unless necessary._

  - **Edge Cases**:
     > Undefined escape sequences will result in an error, such as:

      ```bash
      echo "Hello\_"

      [PARSE FAILED] syntax error: invalid string escape sequence `\_`
            |
          1 | echo "Hello\_"
            |
      ```

#### File Size Type:

  Composed of an integer followed by a unit, supporting the following units:
  `"B" "K" "M" "G" "T" "P"`

  Lumesh will automatically recognize the unit and output in a human-readable format, for example:
  ```bash
  print 3050M 1038B 3000G

  # Output
  2.98G 1K 2.93T
  ```

  File size types can participate in calculations.
  For example:
  ```bash
  1M > 30K      # Returns True
  fs.ls -l | where(size>20K)   # Filters files larger than 20K
  ```

#### Date and Time Type
For example: `t'2025-5-20'`

Date and time types can participate in calculations.
For example:
```bash
t'2025-5-20' > t'2025-1-20'    # Returns True
```
For specific operations, please refer to the built-in `time` module.

#### Percentages

  Written as a percentage, it will automatically be recognized as a float.

  ```bash
  print 37% 2% + 3
  # Output
  0.37 3.02
  ```

  _A percentage sign immediately following a number denotes a percentage, while a space followed by a percentage sign denotes a modulo operation._

### 2. **Variable Declaration**
   - **Declare Variables**: Use the `let` keyword, supporting multiple variable declarations. The type is automatically assigned based on the value.
     ```bash
     let a             # Declaration
     let x = 10        # Declaration and assignment
     ```

### 3. **Variable Assignment**

### Basic Assignment

```bash
# Assignment
a = 3

# Variable declaration and assignment
let x = 10
let name = "Lumesh"

# Multiple variable assignment
let a, b, c = 1, 2, 3
let a, b, c = 1
```

In non-strict mode, variables can be assigned directly without declaration.

*In strict mode, declaration is mandatory and must be unique.*

### Delayed Assignment

Lumesh's unique delayed assignment feature:

```bash
# Use := for delayed assignment; the expression will not execute immediately
let cmd := ls -l /tmp
let calculation := 2 + 3 * 4

# Execute only when needed
eval(cmd)           # Executes ls -l /tmp
eval(calculation)   # Calculates to 14

# If it's a command, it can also be executed directly
cmd
```

### Destructuring Assignment

Supports destructuring assignment for arrays and maps:

```bash
# Array destructuring
let [first, second, *rest] = [1, 2, 3, 4, 5]
# first = 1, second = 2, rest = [3, 4, 5]

# Map destructuring
let {name, age} = {name: "Bob", age: 30, city: "NYC"}
# name = "Bob", age = 30

# Renaming destructuring
let {name: userName, age: userAge} = user_data
```

### 4. **Variable Usage**
Generally, you can use `a` or `$a` directly.

*In strict mode, you must use `$a` only.*
```bash
print $a
```

### 5. **Variable Deletion**
Use `del`.
```bash
del x
```

### 6. **Variable Type Detection**
Use the `typeof` function.
```bash
let a = 10
typeof a                   # Integer
typeof a == "Integer"      # True
```
