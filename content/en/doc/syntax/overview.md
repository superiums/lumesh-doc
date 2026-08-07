---
title: "Syntax: Overview"
date: 2025-07-05 19:16:45
weight: 1
highlight: true
tags:
 - glance
categories:
 - wiki
 - why
 - syntax
---

Lumesh is a modern shell and scripting language using a Pratt parser for complex expression parsing.

## Data Types

### Basic Types

Lumesh supports multiple basic data types:

```bash
# integers
let num = 42
let negative = -100

# floating point
let pi = 3.14159
let percent = 85%


# strings
let str1 = "double quotes string, escape ansi/unicode"
let str2 = 'single quotes raw string, only escapes quotes'
let str2 = r#'raw string, no escaping'#
let template = `template string $var ${var + 1} {var+1}`

# booleans
let flag = true
let disabled = false

# null
let empty = none

# file size
let a = 3000K
```

### Collection Types

```bash
# list (List)
let arr = [1, 2, 3, "mixed", true]
let nested = [[1, 2], [3, 4]]

# hash map (HMap) - unordered, fast lookup
# let hmap = H{name: "Alice", age: 25}

# ordered map (Map) - ordered, supports range queries
let map = {a: 1, b: 2, c: 3}

# range (Range)
let range1 = 1..10      # 1 to 9 (doesn't include 10)
let range2 = 1..=10     # 1 to 10 (includes 10)
let range3 = 1..10:2    # 1, 3, 5, 7, 9 (step size 2)

let array = 1...10      # create array directly from range
```

## Variables and Assignment

### Basic Assignment

```bash
# variable declaration and assignment
let x = 10
let name = "Lumesh"

# multi-variable assignment
let a, b, c = 1
let x, y = getValue()
```

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
let {name: username, age: userAge} = user_data
```

## Operators

### Arithmetic Operators

```bash
let a = 10 + 5      # addition: 15
let b = 10 - 3      # subtraction: 7
let c = 4 * 6       # multiplication: 24
let d = 15 / 3      # division: 5
let e = 17 % 5      # modulo: 2
let f = 2 ^ 3       # exponentiation: 8
```

### Comparison Operators

```bash
# basic comparison
a === b      # strict equality, requires same type
a !== b      # strict inequality, requires same type
a == b      # equality
a != b      # inequality
a > b       # greater than
a < b       # less than
a >= b      # greater than or equal
a <= b      # less than or equal

# pattern matching
text ~: "substring"     # contains match
text ~: r'regex'        # regex match
text !~: "substring"    # not contains match
text !~: r'regex'       # not regex match
```

### Logical Operators

```bash
condition1 && condition2    # logical AND
condition1 || condition2    # logical OR
!condition                  # logical NOT
```

## Pipeline Operations

Lumesh provides multiple pipeline types, its unique feature:

```bash
# standard pipe - passes standard output OR structured data
cmd1 | cmd2

# positional pipe - replace specified argument positions
data | process_func arg1 _ arg3

# loop pipe - dispatches one element at a time, loops, can specify argument positions
list |> transform(param1, param2, _)

# PTY pipe - for interactive programs
cmd1 |^ interactive_program
```

## Error Handling

Lumesh has built-in powerful error handling:

```bash
# ignore error, continue execution
risky_command ?.

# print error to standard output
command ?+

# print error to standard error output
command ??

# print error and override result
command ?>

# terminate program on error
command ?!

# return only whether command succeeded
command ?~

# custom error handling, can provide default values
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

# ternary operator, supports nesting
let value = condition ? true_value : false_value
```

### Loop Structures

```bash
# while loop
while condition {
    # loop body
    update_condition()
}

# for loop, can use on strings, IFS setting controls string splitting; can use * wildcard
for item in collection {
    process(item)
}

# range loop
for i in 0..10 {
    println(i)
}

# infinite loop
loop {
    if break_condition {
        break
    }
}

# simple repeat
repeat 10 {a += 1}
```

### Pattern Matching
Supports regex
```bash
match value {
    1 => "one",
    2, 3 => "two or three",
    xx => "is symbol/string xx",
    g'\w' => "is word",
    g'\d+' => "is digit",
    _ => "default case"
}
```

## Functions

### Function Definition

```bash
# basic function
fn greet(name) {
    "Hello, " + name
}

# function with default parameters
fn add(a, b = 0) {
    a + b
}

# variadic function
fn sum(a, *numbers) {
    numbers | list.fold((x,acc) -> acc + x, 0)
}
```

### Lambda Expressions

```bash
# single parameter lambda
let square = x -> x * x

# multiple parameter lambda
let add = (a, b) -> a + b

# complex lambda body
let process = data -> {
    let filtered = data | list.filter(x -> x > 0)
    filtered | list.map(x -> x * 2)
}
```

### Function Decorators

Supports function decorator syntax:

```bash
@timing
@cache(300)
fn expensive_calculation(input) {
    # complex calculation
    heavy_computation(input)
}
```

## Chaining

Supports object-oriented style chained method calls:

```bash
# string chaining
"hello world"
    .split(' ')
    .map(s -> s.upper()())
    .join('-')

# data processing chain
data
    .filter(x -> x.active)
    .sort(x -> x.priority)
    .take(10)
```

## Indexing and Slicing

```bash
# array indexing
let arr = [1, 2, 3, 4, 5]
let first = arr[0]          # 1


# array slicing
let slice1 = arr[1..4]       # [2, 3, 4]
let slice2 = arr[1.._]        # [2, 3, 4, 5]
let slice3 = arr[_..3]        # [1, 2, 3]
let slice3 = arr[-3.._]       # [3, 4, 5]
# above `_` can be omitted
let slice4 = arr[_.._:2]       # [1, 3, 5] (step size 2)
let slice4 = arr[:2]           # [1, 3, 5] (step size 2)

# map indexing
let obj = {name: "Alice", age: 25}
let name = obj[name]      # "Alice"
let ages = obj.age           # 25 (dot notation access)
```

## Range Operations

```bash
# basic range
1..=10           # 1 to 10
1..10          # 1 to 9 (explicitly doesn't include)

# range with step
1..10:2         # 1, 3, 5, 7, 9
0..100:10       # 0, 10, 20, ..., 90

```

## String Processing

```bash
# string interpolation
let name = "World"
let age =18
let greeting = `Hello, ${age>18 ? "Mr.":"Dear"} $name !`

# multi-line string
let multiline = "
This is a
multi-line string
"

# raw string (no escaping)
let raw = 'C:\path\to\file'
```

## Collection Operations

```bash
# list operations
let numbers = [1, 2, 3, 4, 5]
numbers.append(6)             # add element
numbers + 6                   # add element
# numbers.pop()               # remove last element
numbers - 4                   # remove specified element
numbers.len()                 # get length

# map operations
let person = {name: "Bob", age: 30}
# person.city = "NYC"         # add property
person + {city: "NYC"}         # add property
# del person.age              # delete property
person.keys()               # get all keys
```

## Module System

```bash
# import module
use my_mod
use my_mod as a

# use module functions
a::method()
```

## Comments

```bash
# single line comment
let x = 10  # end-of-line comment
```

## Advanced Features

### Custom Operators

```bash
# define custom operators
let ..+ = (a, b) -> a.concat(b)  # custom binary operator
let __! = x -> math.sum(x)     # custom unary operator

# use custom operators
[1, 2] ..+ [3, 4]    # [1, 2, 3, 4]
[5,6,7]  __!              # 18
```


## Notes

Lumesh's design philosophy combines the elegance of modern programming languages with the practicality of shell, providing powerful error handling, pipeline operations, and module system. The priority system implemented via Pratt parser ensures correct parsing of complex expressions, while rich built-in modules and configuration options make it suitable for various scenarios, from simple command-line operations to complex system management scripts.
