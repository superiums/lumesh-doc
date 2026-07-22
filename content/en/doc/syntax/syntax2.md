---
title: Ranges, Lists, and Maps
date: 2025-06-11 19:16:45
highlight: true
weight: 40
tags:
 - syntax
categories:
 - wiki
 - syntax
---

> Basic Syntax: Ranges, Lists, and Maps

## III. Ranges, Lists, and Maps

### 1. Ranges

- Range expressions
    Ranges use `..` (left-closed, right-open) or `..=` (closed range), with no spaces on either side.
    Variable expansion is supported.

    ```bash
    0..10        # does not include 10
    0..=10         # includes 10
    a..b
    ```

    Range expressions support stepping: `:step`
    ```bash
    0..6:2        # indicates a step of 2: [0,2,4,6]
    ```

    Can be used for loops, containment checks, array creation, etc.

    ```bash
    let r = 0..8

    for i in r {...}       # more efficient than looping directly on an array
    r ~: 5                 # check if it contains an element
    list.from(r)           # convert to an array
    ```

### 2. Lists (Arrays)
- Lists are represented with `[ ]`. The internal elements are ordered.
- They can also be created directly from ranges using `...` or `...=`

    ```bash
    0...5               # outputs [0,1,2,3,4]
    # equivalent to
    list.from(0..5)
    ```

*Two dots `..` `..=` create ranges, while three dots `...` `...=` create arrays.*

- Indexing is represented with `.` or `[i]`.
    ```bash
    let arr = [10, "a", True]
    ```

- Indexing and Slicing
    ```bash
    # Basic indexing

    arr.1
    arr[0]       # → 10

    # Slicing operations
    arr[1:3]     # → ["a", True] (left-closed, right-open)
    arr[::2]     # → [10, True] (step of 2)
    arr[-1:]     # → True (slicing supports negative indexing)
    ```

- Complex Nesting
    ```bash
    # Complex nesting
    [1,24,5,[5,6,8]][3][1]     # displays 6
    # Modify element
    # arr[2] = 3.14 # → [10, "a", 3.14]
    ```
- Advanced Operations
Refer to the [list](/doc/libs/list) module.

**Edge Cases**:
- Array indexing that exceeds bounds will trigger an `out of bounds` error.
- Array slicing supports negative numbers, indicating indices counted from the end.
- Indexing on non-indexable objects will trigger the following error:
`[ERROR] type error: expected indexable type (list/dict/string), found symbol`

### 3. Maps (Dictionaries)
- Maps are represented with `{}`

    ```bash
    let mydict = {name: "Alice", age: 30}

    # Allows shorthand:
    let a = b = 3,
    {a, b}
    {
        a,
        b,
    }          # allows trailing commas, allows multi-line writing
    {a, }             # trailing comma for a single key-value cannot be omitted
    ```

- Dictionary Indexing
    ```bash
    # Basic access

    mydict["name"]     # → "Alice"
    mydict.name        # → "Alice" (shorthand)
    mydict@name        # → "Alice" (shorthand)

    # Dynamic key support
    let key = "ag" + "e"
    dict[key]       # → 30

    # Nested access
    let data = {user: mydict}
    data.user.age # → 100
    ```
- Advanced Operations
Refer to the [map](/doc/libs/map) module.

**Edge Cases**:
| Scenario                          | Behavior                           |
|----------------------------------|-----------------------------------|
| Accessing a non-existent array index | Triggers `[ERROR] key `x` not found in map` error            |
| Indexing a non-dictionary object  | Triggers `[ERROR] not valid index option` error  |
| Indexing an undefined symbol      | Returns a string, as operating on filenames is the most common operation in shell |
