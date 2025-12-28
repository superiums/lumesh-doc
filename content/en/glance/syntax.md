---
title: Lumesh Syntax Overview
date: 2025-07-05 19:16:45  
highlight: true  
image: "images/logo.svg"
tags:  
 - glance  
categories:  
 - wiki  
 - why  
 - syntax
---

Lumesh is a modern shell and scripting language that implements complex expression parsing using a Pratt parser.

## Basic Syntax Structure

### Operator Precedence System

Lumesh uses a precisely defined operator precedence, arranged from low to high:

1. **Assignment Operators** (Priority 1): `=`, `:=`, `+=`, `-=`, `*=`, `/=`
2. **Redirection and Pipeline** (Priority 2): `|`, `|_`, `|>`, `|^`, `>>`, `>!`
3. **Error Handling** (Priority 3): `?.`, `?+`, `??`, `?>`, `?!`
4. **Lambda Expressions** (Priority 4): `->`
5. **Conditional Operators** (Priority 5): `?:`
6. **Logical OR** (Priority 6): `||`
7. **Logical AND** (Priority 7): `&&`
8. **Comparison Operators** (Priority 8): `==`, `!=`, `>`, `<`, `>=`, `<=`
9. Arithmetic operations...

## Data Types

### Basic Types

Lumesh supports various basic data types:

```bash
# Integers
let num = 42
let negative = -100

# Floating-point numbers
let pi = 3.14159
let percent = 85%

# Strings
let str1 = "Double-quoted string,\nwith escape support"
let str2 = 'Single-quoted raw string,\nno escape'
let template = `Template string $var ${var + 1}`

# Booleans
let flag = True
let disabled = False

# Null values
let empty = None

# File sizes
let a = 3000K
```

### Collection Types

```bash
# Lists (List)
let arr = [1, 2, 3, "mixed", True]
let nested = [[1, 2], [3, 4]]

# Hash Map (HMap) - Unordered, fast lookup
# let hmap = H{name: "Alice", age: 25}

# Ordered Map (Map) - Ordered, supports range queries
let map = {a: 1, b: 2, c: 3}

# Ranges (Range)
let range1 = 1..10      # 1 to 9 (excluding 10)
let range2 = 1..=10     # 1 to 10 (including 10)
let range3 = 1..10:2    # 1, 3, 5, 7, 9 (step of 2)

let array = 1...10      # Create an array directly from the range
```

## Variables and Assignment

### Basic Assignment

```bash
# Variable declaration and assignment
let x = 10
let name = "Lumesh"

# Multiple variable assignment
let a, b, c = 1, 2, 3
let x, y = getValue()
```

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
let {name: username, age: userAge} = user_data
```

## Operators

### Arithmetic Operators

```bash
let a = 10 + 5      # Addition: 15
let b = 10 - 3      # Subtraction: 7
let c = 4 * 6       # Multiplication: 24
let d = 15 / 3      # Division: 5
let e = 17 % 5      # Modulus: 2
let f = 2 ^ 3       # Exponentiation: 8
```

### Comparison Operators

```bash
# Basic comparisons
a == b      # Equal
a != b      # Not equal
a > b       # Greater than
a < b       # Less than
a >= b      # Greater than or equal
a <= b      # Less than or equal

# Pattern matching
text ~= "pattern"       # Regex match
text ~: "substring"     # Contains match
text !~= "pattern"      # Regex not match
text !~: "substring"    # Does not contain match
```

### Logical Operators

```bash
condition1 && condition2    # Logical AND
condition1 || condition2    # Logical OR
!condition                  # Logical NOT
```

## Pipeline Operations

Lumesh provides various types of pipelines, which is its unique feature:

```bash
# Standard pipeline - Pass standard output or structured data
cmd1 | cmd2

# Positional pipeline - Replace specified parameter positions
data |_ process_func arg1 _ arg3

# Loop pipeline - Dispatch one element at a time, loop execution, can specify parameter positions
list |> transform(param1, param2, _)

# PTY pipeline - For interactive programs
cmd1 |^ interactive_program
```

## Error Handling

Lumesh has a powerful built-in error handling mechanism:

```bash
# Ignore errors and continue execution
risky_command ?.

# Print error to standard output
command ?+

# Print error to standard error output
command ??

# Print error and override result
command ?>

# Terminate the program on error
command ?!

# Custom error handling, can be used to provide default values
command ?: error_handler
```

## Control Flow

### Conditional Statements

```bash
# if-else expression
let result = if condition {
    "true branch"
} else {
    "false branch"
}

# Ternary operator, supports nesting
let value = condition ? true_value : false_value
```

### Loop Structures

```bash
# while loop
while condition {
    # Loop body
    update_condition()
}

# for loop, can be used for strings, string splitting follows IFS settings; can use * wildcard
for item in collection {
    process(item)
}

# Range loop
for i in 0..10 {
    println(i)
}

# Infinite loop
loop {
    if break_condition {
        break
    }
}

# Simple repetition
repeat 10 {a += 1}
```

### Pattern Matching
Supports regex  
```bash
match value {
    1 => "one",
    2, 3 => "two or three",
    xx => "is symbol/string xx",
    '\w' => "is word",
    '\d+' => "is digit",
    _ => "default case"
}
```

## Functions

### Function Definition

```bash
# Basic function
fn greet(name) {
    "Hello, " + name
}

# Function with default parameters
fn add(a, b = 0) {
    a + b
}

# Variable parameter function
fn sum(a, *numbers) {
    numbers | List.fold(0, (acc, x) -> acc + x)
}
```

### Lambda Expressions

```bash
# Single-parameter lambda
let square = x -> x * x

# Multi-parameter lambda
let add = (a, b) -> a + b

# Complex lambda body
let process = data -> {
    let filtered = data | List.filter(x -> x > 0)
    filtered | List.map(x -> x * 2)
}
```

### Function Decorators

Supports function decorator syntax:

```bash
@timing
@cache(300)
fn expensive_calculation(input) {
    # Complex calculation
    heavy_computation(input)
}
```

## Chained Calls

Supports object-oriented style chained method calls:

```bash
# String chained operations
"hello world"
    .split(' ')
    .map(s -> s.to_upper())
    .join('-')

# Data processing chain
data
    .filter(x -> x.active)
    .sort(x -> x.priority)
    .take(10)
```

## Indexing and Slicing

```bash
# Array indexing
let arr = [1, 2, 3, 4, 5]
let first = arr[0]          # 1

# Array slicing
let slice1 = arr[1:4]       # [2, 3, 4]
let slice2 = arr[1:]        # [2, 3, 4, 5]
let slice3 = arr[:3]        # [1, 2, 3]
let slice4 = arr[-3:]       # [3, 4, 5]
let slice5 = arr[::2]       # [1, 3, 5] (step of 2)

# Map indexing
let obj = {name: "Alice", age: 25}
let name = obj[name]      # "Alice"
let ages = obj.age        # 25 (dot access)
let age = obj@age         # 25 (@ access)
```

## Range Operations

```bash
# Basic range
1..10           # 1 to 10
1..<10          # 1 to 9 (explicitly excluding)

# Ranges with step
1..10:2         # 1, 3, 5, 7, 9
0..100:10       # 0, 10, 20, ..., 90
```

## String Handling

```bash
# String interpolation
let name = "World"
let age = 18
let greeting = `Hello, ${age > 18 ? "Mr." : "Dear"} $name!`

# Multi-line string
let multiline = "
This is a
multi-line string
"

# Raw string (no escape)
let raw = 'C:\path\to\file'
```

## Collection Operations

```bash
# List operations
let numbers = [1, 2, 3, 4, 5]
numbers.append(6)             # Add element
numbers + 6                   # Add element
# numbers.pop()               # Remove last element
numbers - 4                   # Remove specified element
numbers.len()                 # Get length

# Map operations
let person = {name: "Bob", age: 30}
# person.city = "NYC"         # Add property
person + {city: "NYC"}         # Add property
# del person.age              # Delete property
person.keys()               # Get all keys
```

## Module System

```bash
# Import modules
use my_mod
use my_mod as a

# Use module functions
Fs.ls("/tmp")
String.split("hello world", " ")
Math.sin(Math.PI / 2)
```

## Comments

```bash
# Single-line comment
let x = 10  # End-of-line comment
```

## Advanced Features

### Custom Operators

```bash
# Define custom operators
let _+ = (a, b) -> a.concat(b)  # Custom addition
let __! = x -> Math.sum(x)     # Custom prefix operator

# Use custom operators
[1, 2] _+ [3, 4]    # [1, 2, 3, 4]
[5, 6, 7]  __!              # 18
```

## Notes

The design philosophy of Lumesh is to combine the elegant syntax of modern programming languages with the practicality of shell scripting, providing powerful error handling, pipeline operations, and a module system. The priority system implemented through the Pratt parser ensures the correct parsing of complex expressions, while the rich built-in modules and configuration options make it suitable for various scenarios, from simple command-line operations to complex system management scripts.

Learn more:  
- [Feature Overview (superiums/lumesh)](features)  
- [Application Examples (superiums/lumesh)](/cases)  
- [Syntax Manual (superiums/lumesh)](/doc/syntax)  
- [Built-in Functions (superiums/lumesh)](/doc/libs)
