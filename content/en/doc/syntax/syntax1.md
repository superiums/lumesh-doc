---
title: Operators
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

> Basic Syntax: Arithmetic Operations, Logical Operations, Custom Operations

## II. Operators
<!-- > Operators can be viewed using the `ops` command -->

### 1. Classification and Precedence of Operators

**Precedence from high to low** (the higher the number, the higher the precedence)

| Precedence | Operator/Structure         | Example/Description         |
|------------|-----------------------------|------------------------------|
| 13         | Parentheses `()`            | `(a + b) * c`               |
| 12         | Unary Operators `!`, `-`, `__..` | `!flag`, `-5`               |
| 11         | Exponentiation `^`          | `2 ^ 3`                      |
| 10         | Multiplication/Division/Modulus `*`, `/`, `%`, `_*..` | `a * b % c`               |
| 9          | Addition/Subtraction `+`, `-`, `_+..` | `a + b - c`               |
| 8          | Comparison `==`, `!=`, `>` etc. | `a >= b`                    |
| 7          | Logical AND `&&`            | `cond1 && cond2`            |
| 6          | Logical OR `\|\|`             | `cond1 \|\| cond2`            |
| 5          | Conditional Operator `? :`   | `cond ? t : f`              |
| 4          | Lambda Expression `->`      | `x -> x+1`                  |
| 3          | Error Handling `?:`, `?.`   | `3 / 0 ?: 1`                |
| 2          | Pipe `|`                    | `ls -1 | sort`              |
| 2          | Redirection `<<`, `>>`, `>!` | `date _ >> /tmp/day.txt`    |
| 1          | Assignment `=`, `:=`        | `x = 5`, `let y := 10`      |

Pipes and redirection are at the same level, while logical operations have a higher precedence than pipes and redirection.

### 2. Space Rules
| Operator Type         | Requires Spaces            | Example                    |
|-----------------------|---------------------------|----------------------------|
| Regular Operators      | Spaces on both sides      | `a + b`, `x <= 10`        |
|                       | In non-strict mode, some symbols can omit spaces | `a+b`, `x<=10`, `a=5`     |
|                       | `-` and `/` must have spaces | `b-3` is a string, `3- b` is subtraction |
| Custom Operators       | Must start with an underscore and have spaces on both sides | `x _*+ y`, `a _?= b`     |
|                       | Suffix operators must start with double underscores and have spaces | `x __*+`                  |
| Prefix Operators       | Space before or start, no space after | `!x`, `-7`                |
| Infix Operators        | No spaces before or after  | `dict.key`, `1..9`        |
| Suffix Operators       | No spaces before or after  | `func(a)`, `array[i]`, `10M` |

**Custom Operators**
- Custom operators start with `_` and can only contain symbols, not numbers or letters.
- Custom unary operators start with `__`, such as `__+`, with precedence equal to unary operators. They can only be used as suffix operators.
- Custom binary operators starting with `_+`, such as `_+%`, have precedence equal to `+` and `-`.
- Custom multiplication-level operators starting with `_*`, such as `_* -`, have precedence equal to `*` and `/`.

    ```bash
    let __++ = x -> x + 1;
    3 __++
    ```

---

### 3. Special Operators
- `==` and `!=` perform type-sensitive equality comparison.
- `~=` and `!~=` perform value equality comparison.

- `~:` and `!~:` check for containment (can be used for strings, arrays, ranges, dictionaries (to check for a specific key)).
Supports regex containment checks.

Examples:

  ```Bash
  5 == "5"        # False
  5 ~= "5"        # True
  "abc" ~: "a"    # True
  0..8 ~: 8      # False
  0..8 ~: 8       # True
  "abc" ~: '\d'   # False
  ```

### 4. Arithmetic Operations
> `+`, `-`, `*`, `/` are overloaded operators.
> In addition to performing basic arithmetic on numbers, they can also handle more complex operations.
> `%` and `^` remain ordinary operations, only supporting operations between numbers.

#### Addition `+`

> Numbers

- Number + Number = Numbers added with high precision
    ```bash
    1 + 2.5           # → 3.5
    ```

- Number + String = Added as numeric values
    ```bash
    1 + "2.5"           # → 3.5
    ```

- Number + List = Summed together
    ```bash
    1 + [2.5,3]           # → 6.5
    ```

> Strings

- String + String = String concatenation
    ```bash
    "1" + 2.5           # → "12.5"
    ```

- String + List = Concatenated together
    ```bash
    "1" + [2,3]           # → "123"
    ```

> Ranges

- Range + Integer = Range expansion (positive numbers from the tail, negative numbers from the head)
    ```bash
    0..8 + 2           # → 0..10
    0..8 + (-2)           # → 2..8
    ```

> Lists

- List + List = List merging
    ```bash
    [1,2] + [3,4,5]           # → [1,2,3,4,5]
    ```

- List + Other = Inserting value into the list
    ```bash
    [1] + 2.5           # → [1,2.5]
    ```

> Maps

- Map + Map = Map merging
    ```bash
    {a:b} + {c:d}           # → {a:b,c:d}
    ```

- Map + Other = Inserting value into the map
    ```bash
    {a:b} + c           # → {a:b,c:c}
    ```

> Bytes

- Bytes + Bytes = Bytes appending
- Bytes + String = Bytes appending

*Other cases throw exceptions*

* * *

#### Subtraction `-`

> Numbers

- Number - Number = Numbers subtracted with high precision
    ```bash
    1 - 2.5           # → -1.5
    ```

- Number - String = Subtracted as numeric values
    ```bash
    1 - "2.5"           # → -1.5
    ```

> Strings

- String - String = String removal (first occurrence)
    ```bash
    "i am lume" - "a"           # → "i m lume"
    ```

- String - Float = String removal (first occurrence)
    ```bash
    "i am lume v4.2" - 4.2           # → "i am lume v"
    ```

- *String - Integer = String removal (positive numbers from the tail, negative numbers from the head)*
    ```bash
    "98101" - 1           # → "9801"
    "98101" - (-2)           # → "101"
    ```
Exceeding length returns an empty string.

> Ranges

- Range - Integer = Range scaling (positive numbers from the tail, negative numbers from the head)
    ```bash
    0..8 - 2           # → 0..6
    0..8 - (-2)           # → -2..8
    ```

> Lists

- List - List = List difference
    ```bash
    [1,2,3,4,5] - [3,4,5]           # → [1,2]
    ```

- List - Other = List removal
    ```bash
    [1,2,3] - 2           # → [1,3]
    ```

> Maps

- Map - Map = Map difference
    ```bash
    {a:b,c:d} - {c:d}           # → {a:b}
    ```

- Map - Other = Map key removal
    ```bash
    {a:b,c:d} - c           # → {a:b}
    ```

*Other cases throw exceptions*

* * *

#### Multiplication `*`

> Numbers
- Number * Number = Numbers multiplied
    ```bash
    2 * 2.5           # → 5
    ```
- Number * String = Multiplied as numeric values
    ```bash
    2 * "2.5"           # → 5
    ```

> Strings

- String * Integer = String repetition
    ```bash
    "2" * 5           # → "22222"
    ```

- List * List = Matrix multiplication
    ```bash
    [[1,2,3],[4,5,6]] * [[7,8],[9,10],[11,12]]

    # → Returns
        +----------+
        | C0   C1  |
        +==========+
        | 58   64  |
        | 139  154 |
        +----------+

    ```
*Matrix multiplication requires correct dimensions.*
*If there are missing elements internally, they will be filled with 0 during calculation.*

- List * Number = Each element multiplied
    ```bash
    [1,2,3] * 2.5           # → [2.5,5,7.5]
    ```

* * *

#### Division `/`

> Numbers

- Number / Number = Numbers divided
    ```bash
    5 / 2             # → 2
    5 / 2.0           # → 2.5
    ```
- Number / String = Divided as numeric values
    ```bash
    5 / "2"               # → 2
    5.0 / "2.5"           # → 2
    ```

> Lists

- List / Number = Each element divided
    ```bash
    [2,4,6] / 2          # → [1,2,3]
    ```

* * *


### 5. Implicit Type Conversion
Operations between numbers automatically convert to a higher precision type.
When adding different types of data, it always attempts to automatically convert to the type of the first data.

  ```bash
  # Non-strict mode
  3 + "5"                        # → 8 (automatically converted to integer)
  "10" + 2                       # → "102"  (automatically converted to string)
  "10" * 2                       # → "1010"  (string repetition)
  [1,2,3] + 5                    # → [1,2,3,5]
  [2,3] - 2                      # → [3]
  0..8 + 3                       # → 0..11
  {name:wang, age:18} - name     # → {age:18}
  ```


### 6. Increment Operators
> `+=`, `-=`, `*=`, `/=`
Only applicable to numeric operations.

For uninitialized variables, they are automatically initialized to 0.

Unlike arithmetic operations, for non-numeric values, it will return none instead of throwing an exception.
For example:

  ```bash
  a += 1               # → 1
  a += [1]             # none
  ```
**Edge Cases**:
- Division by zero will throw an error.

### 7. Logical Operators
> `&&`, `||` return Bool values.
They convert the values on both sides to Bool values and perform logical operations. For example:
```bash
False && print a   # Does not execute print, returns False
True && print a    # Executes print, returns False because the right side statement returns none
```

- Compared to Bash
Logical operators differ from Bash, and their meaning is the same as in other programming languages, performing purely logical operations.

  + In Bash, `&&` means the right side executes only if the left side succeeds, and it does not return a value.
  + In Lumesh, `&&` means the right side is computed only if the left side returns True, always returning a boolean value.
Similarly,
  + In Bash, `||` means the right side executes only if the left side fails, and it does not return a value.
  + In Lumesh, `||` means the right side is computed only if the left side returns False, always returning a boolean value.

To achieve similar effects as in Bash, you can:
  + Execute the right side only if the left side succeeds: `left_arm ; right_arm`
  + Execute the right side only if the left side fails: `left_arm ?: right_arm`
  
### 8. Ternary Operator
> `test ? t : f`

Equivalent to `if test { t } else { f}`

Supports nesting, such as `test?t:b?tb:fb`

* * *

## III. Custom Operators
### Custom Unary Operators
Custom unary operators must start with `__`, and after definition, they are used as suffix operators.

```bash
# Definition
let __! = x -> Math.sum(x)
# Usage
[5,6,7]  __!              # 18
```

> Why not define prefix operators? Because they are similar to function calls, it is better to define a function directly.
> If you think there is a need for it, you can submit a request at the [project homepage](https://codeberg.org/santo/lumesh/issue).

### Custom Binary Operators
Custom binary operators must start with `_` (but not with `__`), and after definition, they are used as binary operators.

```bash
# Define a custom operator
let _+ = (a, b) -> a.concat(b)  # Custom addition

# Use the custom operator
[1, 2] _+ [3, 4]    # [1, 2, 3, 4]
```

Binary operators involve operator precedence.
- Operators starting with `_+` have precedence equal to addition.
- Operators starting with `_*` have precedence equal to multiplication.
- Other operators starting with `_` have precedence higher than exponentiation and lower than unary operators (like `!`).
