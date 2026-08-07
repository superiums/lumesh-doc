---
title: "Syntax: Ranges, Lists, Sets, and Maps"
date: 2025-06-11 19:16:45
highlight: true
weight: 40
tags:
 - syntax
categories:
 - wiki
 - syntax
---

> Basic Syntax: Ranges, Lists, Sets, and Maps

## III. Ranges, Lists, Sets, and Maps

### 1. Ranges


- Range Expression
    Ranges use `..` (left-closed right-open) or `..=` (closed interval), no spaces on either side.
    Supports variable expansion.


    ```bash
    0..10        # doesn't include 10
    0..=10       # includes 10
    a..=b
    ```

    Range expressions support stepping: `:step`
    ```bash
    0..=6:2     # step of 2: [0,2,4,6]
    ```

    Can be used in loops, containment detection, array creation, etc.

    ```bash
    let r = 0..=8

    for i in r {...}       # more efficient than looping directly on array
    r ~: 5                 # check if contains element
    list.from(r)           # convert to array
    ```

### 2. Lists (Arrays)/Sets
- Lists represented by `[ ]`. Elements inside are ordered.
- Sets represented by `S{}`, sets automatically sorted inside, cannot be intervened from outside sorting.

-= Also can use `...` or `...=` to create directly from range

    ```bash
    0...5               # outputs [0,1,2,3,4]
    # equivalent to
    list.from(0..5)
    ```

*Two dots `..` `..=` create range, three dots `...` `...=` create array*

- Indexing using `.` or `[i]`.
    ```bash
    let arr = [10, "a", true]
    ```

- Indexing and slicing
    ```bash
    # basic indexing

    arr.1
    arr[0]       # → 10

    # slice operations
    arr[1..3]     # → ["a", true] (left-closed right-open)
    arr[_.._:2]     # → [10, true] (step size 2)
    arr[-1.._]      # → true (slice supports negative indices)
    ```

**Placeholder `_` in slice means open interval**

- Complex nesting
    ```bash
    # complex nesting
    [1,24,5,[5,6,8]][3][1]     # displays 6
    # # modify element
    # arr[2] = 3.14 # → [10, "a", 3.14]
    ```
- Advanced operations
Refer to [list](/zh-cn/doc/libs/list) module.

**Edge Cases**:
- Array index if out of bounds, triggers `out of bounds` error
- Array slice supports negative, meaning index counting from end
- If indexable operation performed on non-indexable object, triggers:
`[ERROR] type error: expected indexable type (list/dict/string), found symbol`

### 3. Maps (Dictionaries)
- Maps represented by `{}` or `M{}` for BtreeMap, and `H{}` for HashMap

    ```bash
    let mydict = {name: "Alice", age: 30}

    # allow shorthand:
    let a = b =3,
    H{a, b}
    M{
        a,
        b,
    }          # allow trailing comma, allow multi-line writing
    {a, }             # single key-value comma cannot be omitted
    ```

- Dictionary indexing
    ```bash
    # basic access

    mydict["name"]     # → "Alice"
    mydict.name        # → "Alice" (shorthand form)
    mydict@name        # → "Alice" (shorthand form)

    # dynamic key support
    let key = "ag" + "e"
    dict[key]       # → 30

    # nested access
    let data = {user: mydict}
    data.user.age # → 100
    ```
- Advanced operations
Refer to [map](/zh-cn/doc/libs/map) module.

**Edge Cases**:
| Scenario                          | Behavior                           |
|------------------------------|--------------------------------|
| Access non-existent array index          | Triggers `[ERROR] key `x` not found in map` error            |
| Index non-dictionary object          | Triggers `[ERROR] not valid index option` error  |
| Index undefined symbol          | Returns string, because file name operations are most common in shell |


*Progress: Completed file 6/15*
