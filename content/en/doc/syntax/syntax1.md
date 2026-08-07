---
title: "Syntax: Operators"
date: 2025-07-05 19:16:45
weight: 30
highlight: true
tags:
 - syntax
categories:
 - wiki
 - why
 - syntax
---

> Basic Syntax: Arithmetic, Logical, Custom Operators

## II. Operators
<!-- > Operators can be viewed via `ops` command -->

### 1. Operator Classification and Precedence

**Precedence from high to low** (higher number = higher priority)

| Priority | Operator/Structure              | Example/Description                  |
| --- | ------------------- | ---------------------- |
| 13  | Parentheses `()`             | `(a + b) * c`          |
| 12  | Unary Operators `!`, `-`, `__..`  | `!flag`, `-5`          |
| 11  | Exponentiation `^`            | `2 ^ 3`               |
| 10  | Multiplication/Division/Modulo `*`, `/`, `%`, `_*..` | `a * b % c`            |
| 9   | Addition/Subtraction `+`, `-`, `_+..`         | `a + b - c`            |
| 8   | Comparison `==`, `!=`, `>` etc | `a >= b`                 |
| 7   | Logical AND `&&`            | `cond1 && cond2`       |
| 6   | Logical OR `\|\|`          | `cond1 \|\| cond2`       |
| 5   | Conditional Operator `? :`       | `cond ? t : f`         |
| 4   | Lambda Expression `->`         |    `x -> x+1`              |
| 3   | Error Handling `?:` `?.`     |    `3 / 0 ?: 1`          |
| 2   | Pipe `\|`               | `ls -1 \| sort`               |
| 2   | Redirection `<<` `>>` `>!`  | `date _ >> /tmp/day.txt`      |
| 1   | Assignment `=`, `:=`        | `x = 5`, `let y := 10` |

Pipe and redirection have same level; logical operators have higher priority than pipe and redirection.


### 2. Space Rules
| Operator Type         | Requires Space                | Example                    |
|-------------------|---------------------------|-------------------------|
| Regular Operators         | Spaces on both sides                  | `a + b`, `x <= 10`       |
|                  | Non-strict mode, spaces can be omitted on both sides  | `a+b`, `x<=10`, `a=5`      |
|                  |          `-` and `/` should have spaces       | `b-3` is string, `3- b` is subtraction |
| Custom Operators       | Must start with underscore and have spaces on both sides     | `x _*+ y`, `a _?= b`     |
|                  | Postfix operator, must start with double underscore and have space     | `x __*+`     |
| Prefix Operators         | Space before or start, no space after                  | `!x`,  `-7`                 |
| Infix Operators           | No space before or after                  | `dict.key`,   `1..9`      |
| Postfix Operators           | No space before or after                  | `func(a)`,   `array[i]`, `10M`    |

**Custom Operators**
- Custom unary operators start with `__`, e.g., `__+`, same precedence as unary operators. Can only be used as postfix.

- Custom operators are symbols starting with `..`, can only contain symbols, no numbers or letters.
- Custom `+`-level operators start with `..+`, e.g., `..+%`, same precedence as `+` `-`
- Custom `*`-level operators start with `..*`, e.g., `..*-`, same precedence as `*` `/`

    ```bash
    let __++ = x -> x + 1;
    3 __++
    ```

---


### 3. Special Operators
- `===` `!==` type-aware equality comparison.
- `==` `!=` equality comparison, allows different types.

- `~:` `!~:` containment (can be used on strings, arrays, ranges, dicts (check if contains specific key)).
Supports regex containment detection.

Example:

  ```Bash
  5 === "5"       # false
  5 == "5"        # true
  5 == 5.0        # true
  "abc" ~: "a"    # true
  0..8 ~: 8      # false
  0..=8 ~: 8       # true
  "abc" ~: g'\d'  # false

  ```

**For non-comparable types, returns `none`**

Example: `'a' > 1`      # none


### 4. Arithmetic Operations
> `+` `-` `*` `/` are overloaded operators
> Besides completing arithmetic on numbers, can also complete more complex operations
> `%` `^` are still regular operators, only support operations between numbers.

#### Addition `+`

> Numbers

- Number + Number = high-precision addition
    ```bash
    1 + 2.5           # → 3.5
    ```

- Number + String = concatenate numerically
    ```bash
    1 + "2.5"           # → 3.5
    ```

- Number + List = sum together
    ```bash
    1 + [2.5,3]           # → 6.5
    ```

> Strings

- String + String = string concatenation
    ```bash
    "1" + 2.5           # → "12.5"
    ```


- String + List = concatenate together
    ```bash
    "1" + [2,3]           # → "123"
    ```

> Ranges

- Range + Integer = range expansion (positive from tail, negative from head)
    ```bash
    0..8 + 2           # → 0..10
    0..8 + (-2)           # → 2..8
    ```

> Lists

- List + List = list merge
    ```bash
    [1,2] + [3,4,5]           # → [1,2,3,4,5]
    ```

- List + Other = insert value into list
    ```bash
    [1] + 2.5           # → [1,2.5]
    ```

> Maps

- Map + Map = map merge
    ```bash
    {a:b} + {c:d}           # → {a:b,c:d}
    ```

- Map + Other = insert value into map
    ```bash
    {a:b} + c           # → {a:b,c:c}
    ```

> Bytes

- Byte + Byte = byte append
- Byte + String = byte append

*Other cases throw exception*

* * *

#### Subtraction `-`

> Numbers

- Number - Number = high-precision subtraction
    ```bash
    1 - 2.5           # → -1.5
    ```

- Number - String = subtract numerically
    ```bash
    1 - "2.5"           # → -1.5
    ```


> Strings

- String - String = string remove (first occurrence)
    ```bash
    "i am lume" - "a"           # → "i m lume"
    ```

- String - Float = string remove (first occurrence)
    ```bash
    "i am lume v4.2" - 4.2           # → "i am lume v"
    ```

- *String - Integer = string remove (positive from tail, negative from head)*
    ```bash
    "98101" - 1           # → "9801"
    "98101" - (-2)           # → "101"
    ```
If beyond length, returns empty string

> Ranges

- Range - Integer = range scale (positive from tail, negative from head)
    ```bash
    0..8 - 2           # → 0..6
    0..8 - (-2)           # → -2..8
    ```

> Lists

- List - List = list difference
    ```bash
    [1,2,3,4,5] - [3,4,5]           # → [1,2]
    ```

- List - Other = list remove
    ```bash
    [1,2,3] - 2           # → [1,3]
    ```

> Maps

- Map - Map = map difference
    ```bash
    {a:b,c:d} - {c:d}           # → {a:b}
    ```

- Map - Other = map key remove
    ```bash
    {a:b,c:d} - c           # → {a:b}
    ```

*Other cases throw exception*

* * *

#### Multiplication `*`

> Numbers
- Number * Number = number multiplication
    ```bash
    2 * 2.5           # → 5
    ```
- Number * String = multiply numerically
    ```bash
    2 * "2.5"           # → 5
    ```

> Strings

- String * Integer = string repeat
    ```bash
    "2" * 5           # → "22222"
    ```

- List * List = matrix multiplication
    ```bash
    [[1,2,3],[4,5,6]] * [[7,8],[9,10],[11,12]]

    # → returns
        +----------+
        | C0   C1  |
        +==========+
        | 58   64  |
        | 139  154 |
        +----------+

    ```
*Matrix multiplication requires correct dimensions*
*If missing elements inside, will be filled with 0 during calculation*

- List * Number = multiply each element
    ```bash
    [1,2,3] * 2.5           # → [2.5,5,7.5]
    ```

* * *

#### Division `/`

> Numbers

- Number / Number = number division
    ```bash
    5 / 2             # → 2
    5 / 2.0           # → 2.5
    ```
- Number / String = divide numerically
    ```bash
    5 / "2"               # → 2
    5.0 / "2.5"           # → 2
    ```

> Lists

- List / Number = divide each element
    ```bash
    [2,4,6] / 2          # → [1,2,3]
    ```

* * *


### 5. Implicit Type Conversion
Operations between numbers automatically convert to higher precision type.
Operations between different types always try to automatically convert to the type of the first operand.

  ```bash
  # Non-strict mode
  3 + "5"                        # → 8 (auto converted to integer)
  "10" + 2                       # → "102"  (auto converted to string)
  "10" * 2                       # → "1010"  (string repeat)
  [1,2,3] + 5                    # → [1,2,3,5]
  [2,3] - 2                      # → [3]
  0..8 + 3                       # → 0..11
  {name:wang, age:18} - name     # → {age:18}
  ```


### 6. Increment Operators
> `+=` `-=` `*=` `/=`
Only applicable to number operations.

For uninitialized, auto-initialize to 0

Example:

  ```bash
  a += 1               # → 1
  ```
**Edge Cases**:
- Division by zero will error.

### 7. Logical Operators
> `&&` `||` return Bool
They convert both sides to Bool and perform logical operations. Example:
```bash
false && print a   # doesn't execute print, returns false
true && print a   # executes print, returns false, because right side returns none
```

- Comparison with Bash
Logical operators differ from Bash, but same as other programming languages, they perform pure logical operations.

  + In Bash, `&&` executes right side only if left side succeeds, doesn't return value.
  + In Lumesh, `&&` calculates right side only if left returns true, always returns boolean.
Similarly,
  + In Bash, `||` executes right side only if left fails, doesn't return value.
  + In Lumesh, `||` calculates right side only if left returns false, always returns boolean.

To achieve similar effect to Bash:
  `a &: b ?: c` 

### 8. Conditional Operator
> `test ? t : f`

Equivalent to `if test { t } else { f}`

Supports nesting, e.g., `test?t:b?tb:fb`

* * *

## III. Custom Operators
### Custom Unary Operator
Custom unary operator must start with `__`, after definition, used as postfix operator.

```bash
# definition
let __! = x -> Math.sum(x)
let __+ = x -> x.upper()()

# usage
[5,6,7]  __!              # 18
'lume' __+                # LUME

```

### Custom Binary Operator
Custom binary operator must start with `_` (but not `__`), after definition, used as binary operator.

```bash
# define custom operator
let ..+ = (a, b) -> a.concat(b)  # custom addition

# use custom operator
[1, 2] ..+ [3, 4]    # [1, 2, 3, 4]
```

Binary operator involves precedence.
- Starting with `..+`, same precedence as addition.
- Starting with `..*`, same precedence as multiplication.
- Other operators starting with `..`, higher precedence than exponentiation, lower than unary (e.g., `!`).


### Use Cases

1. Data Processing Pipeline
```bash
let ..-> = (data,processor) -> processor(data)

let a = [-3,-2,1,5,8, 'fast','pipe']
let clean = x -> list.filter(x, i -> typeof(i)=='Integer')
let filter = x -> list.filter(x, i -> i>0)
let save = x -> {x >> /tmp/saved}

a ..-> clean ..-> filter ..-> save

```

2. Type Checking

```bash
let ..? = (value, expected_type) -> typeof(value) == expected_type ? value : throw(format('expected {expected_type}, found {}', typeof(value)))

5 ..? 'String'

```

*Progress: Completed file 5/15*
