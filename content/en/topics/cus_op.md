---
title: Custom Operators  
date: 2025-12-25 19:16:45
---

Custom Operators
----------------

### Custom Unary Operators

Custom unary operators must start with `__`, and are used as postfix operators after definition.

```bash
    # Define
    let __! = x -> Math.sum(x)
    let __+ = x -> x.to_upper()
    
    # Use
    [5,6,7]  __!              # 18
    'lume' __+                # LUME
    
```
> Why not define prefix operators? Because they are similar to function calls, it's better to just define a function directly.  
> If you think there is a need for this, you can submit a request on the [project homepage](https://codeberg.org/santo/lumesh/issue).

### Custom Binary Operators

Custom binary operators must start with `_` (but not with `__`), and are used as binary operators after definition.

```bash
    # Define custom operator
    let ..+ = (a, b) -> a.concat(b)  # Custom addition
    
    # Use custom operator
    [1, 2] ..+ [3, 4]    # [1, 2, 3, 4]
    
```
Binary operators involve operator precedence.

*   Those starting with `..+` have the same precedence as addition.
*   Those starting with `..*` have the same precedence as multiplication.
*   Other operators starting with `..` have higher precedence than exponentiation but lower than unary operators (such as `!`).

### Use Cases

1.  Data Processing Pipeline

```bash
    let ..-> = (data, processor) -> processor(data)
    
    let a = [-3,-2,1,5,8, 'fast','pipe']
    let clean = x -> list.filter(x, i -> typeof(i)=='Integer')
    let filter = x -> list.filter(x, i -> i>0)
    let save = x -> {x >> /tmp/saved}
    
    a ..-> clean ..-> filter ..-> save
    
```
2.  Type Checking

```bash
    let ..? = (value, expected_type) -> typeof(value) == expected_type ? value : throw(format('expected {expected_type}, found {}', typeof(value)))
    
    5 ..? 'String'
    
```
