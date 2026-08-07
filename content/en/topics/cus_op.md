---
title: Custom Operators
date: 2025-12-25 19:16:45
---

## Custom Operators

### Custom Unary Operators
Custom unary operators must start with `__`. After definition, they are used as suffix operators.

```bash
# Definition
let __! = x -> Math.sum(x)
let __+ = x -> x.upper()()

# Usage
[5,6,7]  __!              # 18
'lume' __+                # LUME
```

> Why not define prefix operators? Because they're similar to function calls, so it's better to just define functions directly.
> If you think there's a need for them, you can submit a request at [the project homepage](https://codeberg.org/santo/lumesh/issue).

### Custom Binary Operators
Custom unary operators must start with `_` (but not `__`). After definition, they are used as binary operators.

```bash
# Define custom operator
let ..+ = (a, b) -> a.concat(b)  # Custom addition

# Use custom operator
[1, 2] ..+ [3, 4]    # [1, 2, 3, 4]
```

Binary operators involve operator precedence.
- Operators starting with `..+` have the same precedence as addition.
- Operators starting with `..*` have the same precedence as multiplication.
- Other operators starting with `..` have higher precedence than exponentiation but lower than unary operators (like `!`).


### Application Scenarios

1. Data processing pipeline
```bash
let ..-> = (data,processor) -> processor(data)

let a = [-3,-2,1,5,8, 'fast','pipe']
let clean = x -> list.filter(x, i -> typeof(i)=='Integer')
let filter = x -> list.filter(x, i -> i>0)
let save = x -> {x >> /tmp/saved}

a ..-> clean ..-> filter ..-> save
```

2. Type checking

```bash
let ..? = (value, expected_type) -> typeof(value) == expected_type ? value : throw(format('expected {expected_type}, found {}', typeof(value)))

5 ..? 'String'
```