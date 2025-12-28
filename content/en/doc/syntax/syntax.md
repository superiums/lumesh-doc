---
title: Lumesh Syntax Manual 1
subtitle: Basic Syntax
date: 2025-06-11 19:16:45  
highlight: true  
tags:  
 - syntax  
categories:  
 - wiki  
 - syntax  
---

> Basic Syntax

## I. Variables and Types

### 1. **Variable Declaration and Assignment**
   - **Declare Variables**: Use the `let` keyword, supporting multiple variable declarations. Types are automatically assigned based on the value.
     ```bash
     let x = 10                 # Single variable
     let a, b = 1, "hello"      # Multiple variable assignments (the number of expressions on the right must match)
     let a, b, c = 1            # Uniform assignment for multiple variables
     let a = b = 1              # Chained assignment for multiple variables
     ```

   - **Assignment Operators**:
     - `=`: Regular assignment.
     - `:=`: Delayed assignment (stores the expression as a reference).
     ```bash
     x := 2 + 3  # Delayed evaluation, stores the expression rather than the result
     echo x      # Outputs the expression: 2 + 3
     eval x      # Outputs the result: 5
     ```
   - **Delete Variables**: Use `del`.
     ```bash
     del x
     ```
   - **Using Variables**: Optional `$`, directly use `echo a x`.

   *Strict mode requires $: `echo $x`*

   - **Check Variable Type**:
     ```bash
     let a = 10
     type a                   # Integer
     type a == "Integer"      # True
     ```

   **Edge Cases**:
   - In strict mode (`-s`), variables must be initialized; otherwise, an error will occur.
   - When declaring multiple variables, the number of expressions on the right must match the number of variables on the left or be a single value; otherwise, a `SyntaxError` will be thrown.

### 2. **Data Types**
#### Basic Types
  | Type       | Example                     |
  |------------|-----------------------------|
  | Variable   | `x`, `$a`                   |
  | Integer     | `42`, `-3`                 |
  | Float      | `3.14`, `-0.5`, `10%`      |
  | String     | `"Hello\n"`, `'raw'`       |
  | Boolean    | `True`, `False`            |
  | List (Array) | `[1, "a", True]`         |
  | Map (Dictionary) | `{name: "Alice", age: 30}` |
  | Range      | `1..8`, `1..<10`           |
  | Regex      | `r'^\w+'`                  |
  | Time       | `t'2025-5-20'`             |
  | File Size  | `4K`, `5T`                  |
  | Null       | `None`                     |

#### Complex Types
  | Type       | Example                     |
  |------------|-----------------------------|
  | Function   | `fn add(x,y){return x+y}`  |
  | Lambda     | `let add = (x,y) -> x + y` |
  | Built-in Library | `Math.floor`          |

  **Scope Rules**
   - Lambda and function definitions create a child environment scope.
   - Child environments inherit parent environment variables without modifying the parent scope.

#### Strings
  - Single quotes denote raw strings.
  - Double quotes support text escaping (e.g., `\n`), Unicode escaping (e.g., `\u{2614}`), and ANSI escaping (e.g., `\033[34m`).
  - Backticks denote template strings, supporting `$var` variable substitution and `${var}` sub-expression execution capture, as well as ANSI escape characters.

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

     _Variable substitution within backticks is more efficient than sub-expression capture; it is recommended to use the former unless necessary._

  - **Edge Cases**:
     > The `\` in single-quoted strings is treated literally (e.g., `'Line\n'` outputs `Line\n`).
     > Undefined escape sequences will throw an error, such as:

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
  Fs.ls -l | where(size > 20K)   # Filter files larger than 20K
  ```

#### Date and Time Type
For example: `Fs.parse('2025-5-20')`

Date and time types can participate in calculations.
For example:
```bash
Fs.parse('2025-5-20') > Fs.parse('2025-1-20')  # Returns True
```
For specific operations, please refer to the built-in function `time` module.

#### Percentage

Written as a percentage, it will automatically be recognized as a float.

```bash
print 37% 2% + 3
# Output
0.37 3.02
```

_The percentage sign immediately following a number denotes a percentage, while a space followed by % denotes a modulus operation._

---

## II. Operators
<!-- > Operators can be viewed using the `ops` command -->

### 1. Operator Classification and Precedence

**Precedence from high to low** (the higher the number, the higher the precedence)

| Precedence | Operator/Structure        | Example/Description          |
|------------|---------------------------|-------------------------------|
| 13         | Parentheses `()`          | `(a + b) * c`                |
| 12         | Unary Operators `!`, `-`, `__..` | `!flag`, `-5`                |
| 11         | Exponentiation `^`        | `2 ^ 3`                       |
| 10         | Multiplication/Division/Modulus `*`, `/`, `%`, `_*..` | `a * b % c`                |
| 9          | Addition/Subtraction `+`, `-`, `_+..` | `a + b - c`                |
| 8          | Comparison `==`, `!=`, `>` etc. | `a > b`                     |
| 7          | Logical AND `&&`          | `cond1 && cond2`             |
| 6          | Logical OR `||`           |                               |
| 5          | Conditional Operator `? :` | `cond ? t : f`               |
| 4          | Lambda Expression          | `x -> x + 1`                 |
| 3          | Error Handling `?:` `?.`  | `3 / 0 ?: 1`                 |
| 2          | Pipeline `|`              | `ls | sort`                  |
|            | Redirection `<<` `>>` `>!` | `date >> /tmp/day.txt`       |
| 1          | Assignment `=`, `:=`      | `x = 5`, `let y := 10`       |

Pipelines and redirections are on the same level, while logical operations have higher precedence than pipelines and redirections.

### 2. Space Rules
| Operator Type         | Requires Spaces            | Example                    |
|-----------------------|---------------------------|----------------------------|
| Regular Operators      | Spaces on both sides       | `a + b`, `x <= 10`         |
|                       | In non-strict mode, some symbols can omit spaces | `a+b`, `x<=10`, `a=5`      |
|                       | `-` and `/` should have spaces | `b-3` is a string, `3- b` is subtraction |
| Custom Operators       | Must start with an underscore and have spaces on both sides | `x _*+ y`, `a _?= b`     |
|                       | Suffix operators must start with double underscores and have spaces | `x __*+`                   |
| Prefix Operators       | Space before or at the start, no space after | `!x`, `-7`                 |
| Infix Operators        | No spaces before or after   | `dict.key`, `1..9`        |
| Suffix Operators       | No spaces before or after    | `func(a)`, `array[i]`, `10M` |
|                       | Future deprecation, not recommended | `a++`, `a--`              |

`a--` is recommended to be replaced with `a -= 1`  
Because `--` is commonly used for parameter passing in shells, it is not advisable to use `a--` or `a++`.

**Custom Operators**
- Custom operators start with `_` and can only contain symbols, not numbers or letters.
- Custom unary operators start with `__`, such as `__+`, with precedence equal to unary operators. They can only be used as suffix operators.
- Custom addition-level operators start with `_+`, such as `_+%`, with precedence equal to `+` and `-`.
- Custom multiplication-level operators start with `_*`, such as `_*-`, with precedence equal to `*` and `/`.

    ```bash
    let __++ = x -> x + 1;
    3 __++
    ```

**Edge Cases**:
- `x++y` is illegal, while `x+++y` is legal.

---

### 3. Special Operators
- `==` Type-equal comparison.
- `~=` Value-equal comparison.
- `~~` String regex match.
- `~:` Contains (can be used for strings, arrays, ranges, dictionaries (to check for a specified key)).
- `!~~` String regex not match.
- `!~:` Does not contain.

Examples:

```bash
5 == "5"        # False
5 ~= "5"        # True
"abc" ~: "a"    # True
0..8 ~: 8       # False
0..8 ~: 8      # True
"abc" ~~ '\d'   # False
```

### 4. Overloaded Operators

#### Addition `+`

> Numbers

- Number + Number = High-precision addition
    ```bash
    1 + 2.5           # → 3.5
    ```

- Number + String = Value addition
    ```bash
    1 + "2.5"           # → 3.5
    ```

- Number + List = Sum together
    ```bash
    1 + [2.5, 3]           # → 6.5
    ```

> Strings

- String + String = String concatenation
    ```bash
    "1" + 2.5           # → "12.5"
    ```

- String + List = Concatenation
    ```bash
    "1" + [2, 3]           # → "123"
    ```

> Ranges

- Range + Integer = Range expansion (positive from the tail, negative from the head)
    ```bash
    0..8 + 2           # → 0..10
    0..8 + (-2)           # → 2..8
    ```

> Lists

- List + List = List merging
    ```bash
    [1, 2] + [3, 4, 5]           # → [1, 2, 3, 4, 5]
    ```

- List + Other = Insert value into the list
    ```bash
    [1] + 2.5           # → [1, 2.5]
    ```

> Maps

- Map + Map = Map merging
    ```bash
    {a: b} + {c: d}           # → {a: b, c: d}
    ```

- Map + Other = Insert value into the map
    ```bash
    {a: b} + c           # → {a: b, c: c}
    ```

*Other cases will throw an exception.*

* * *

#### Subtraction `-`

> Numbers

- Number - Number = High-precision subtraction
    ```bash
    1 - 2.5           # → -1.5
    ```

- Number - String = Value subtraction
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

- *String - Integer = String removal (positive from the tail, negative from the head)*
    ```bash
    "98101" - 1           # → "9801"
    "98101" - (-2)           # → "101"
    ```
Exceeding the length will return an empty string.

> Ranges

- Range - Integer = Range scaling (positive from the tail, negative from the head)
    ```bash
    0..8 - 2           # → 0..6
    0..8 - (-2)           # → -2..8
    ```

> Lists

- List - List = List difference
    ```bash
    [1, 2, 3, 4, 5] - [3, 4, 5]           # → [1, 2]
    ```

- List - Other = List removal
    ```bash
    [1, 2, 3] - 2           # → [1, 3]
    ```

> Maps

- Map - Map = Map difference
    ```bash
    {a: b, c: d} - {c: d}           # → {a: b}
    ```

- Map - Other = Remove key from the map
    ```bash
    {a: b, c: d} - c           # → {a: b}
    ```

*Other cases will throw an exception.*

* * *

#### Multiplication `*`

> Numbers
- Number * Number = Number multiplication
    ```bash
    2 * 2.5           # → 5
    ```
- Number * String = Value multiplication
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
    [[1, 2, 3], [4, 5, 6]] * [[7, 8], [9, 10], [11, 12]]

    # → Returns
        +----------+
        | C0   C1  |
        +==========+
        | 58   64  |
        | 139  154 |
        +----------+

    ```
*Matrix multiplication requires correct dimensions.*
*If there are missing elements internally, calculations will be filled with 0.*

- List * Number = Each element multiplication
    ```bash
    [1, 2, 3] * 2.5           # → [2.5, 5, 7.5]
    ```

* * *

#### Division `/`

> Numbers

- Number / Number = Number division
    ```bash
    5 / 2             # → 2
    5 / 2.0           # → 2.5
    ```
- Number / String = Value division
    ```bash
    5 / "2"               # → 2
    5.0 / "2.5"           # → 2
    ```

> Lists

- List / Number = Each element division
    ```bash
    [2, 4, 6] / 2          # → [1, 2, 3]
    ```

* * *

#### Other `+=` `-=` `*=` `/=`
Only applicable to numerical operations.

For uninitialized variables, they are automatically initialized to 0.

Unlike arithmetic operations, non-numeric types will return None instead of throwing an exception.
For example:

```bash
a += 1               # → 1
a += [1]             # None
```
**Edge Cases**:
- Division by zero will throw an error.

* * *

### 5. Implicit Type Conversion
When performing operations between numbers, automatic conversion to higher precision types occurs.
When adding different types of data, it always attempts to automatically convert to the type of the first data.

```bash
# Non-strict mode
3 + "5"                        # → 8 (automatically converted to integer)
"10" + 2                       # → "102"  (automatically converted to string)
"10" * 2                       # → "1010"  (string repetition)
[1, 2, 3] + 5                  # → [1, 2, 3, 5]
[2, 3] - 2                     # → [3]
0..8 + 3                       # → 0..11
{name: "wang", age: 18} - name # → {age: 18}
```

* * *

## III. Ranges, Lists, and Maps

### 1. Ranges

- Range expressions
    Ranges use `..<` (left-closed, right-open) or `..` (closed range), with no spaces on either side.
    Supports variable expansion.

    ```bash
    0..<10        # Excludes 10
    0..10         # Includes 10
    a..b
    ```

    Range expressions support stepping: `:step`
    ```bash
    0..6:2     # Represents step of 2: [0, 2, 4, 6]
    ```

    Can be used for loops, containment checks, array creation, etc.

    ```bash
    let r = 0..8

    for i in r {...}       # More efficient than looping directly over an array
    r ~: 5                 # Check if it contains an element
    List.from(r)           # Convert to an array
    ```

### 2. Lists (Arrays)
- Lists are represented with `[]`. The elements inside are ordered.
- They can also be created directly from ranges using `...` or `...<`.

    ```bash
    0...5               # Outputs [0, 1, 2, 3, 4]
    # Equivalent to
    List.from(0..5)
    ```

*Two dots `..` `..<` create ranges, while three dots `...` `...<` create arrays.*

- Indexing is done using `.` or `[i]`.
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
    [1, 24, 5, [5, 6, 8]][3][1]     # Displays 6
    # # Modify elements
    # arr[2] = 3.14 # → [10, "a", 3.14]
    ```
- Advanced Operations
Refer to the [list](/doc/libs/list) module.

**Edge Cases**:
- Accessing an out-of-bounds array index will trigger an `out of bounds` error.
- Array slicing supports negative indices, counting from the end.
- Indexing a non-indexable object will trigger the following error:
`[ERROR] type error: expected indexable type (list/dict/string), found symbol`

### 3. Maps (Dictionaries)
- Maps are represented with `{}`.

    ```bash
    let mydict = {name: "Alice", age: 30}

    # Allows shorthand:
    let a = b = 3,
    {a, b}
    {
        a,
        b,
    }          # Allows trailing commas, allows multi-line writing
    {a, }             # A single key-value's comma cannot be omitted
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
    data.user.age # → 30
    ```
- Advanced Operations
Refer to the [map](libs/map) module.

**Edge Cases**:
| Scenario                          | Behavior                           |
|----------------------------------|------------------------------------|
| Accessing a non-existent array index | Triggers `[ERROR] key `x` not found in map` error |
| Indexing a non-dictionary object | Triggers `[ERROR] not valid index option` error |
| Indexing an undefined symbol     | Returns a string, as file name operations are common in shell |
| Indexing an undefined symbol     | Returns a string, as file name operations are common in shell |

---

## IV. Statements

1. Statement Blocks
    Represented with `{}`. Generally used for control flow statements.

2. Statement Groups (Sub-command Capture)
    Use parentheses to represent sub-commands; sub-commands do not create new processes and do not isolate variable scopes.
  `echo (len [5, 6])`

3. Statements
    Separate with `;` or `enter`.

  - **Newline**: `;` or enter.

  - **Continuation**: Use `\` + newline for multi-line writing.

       ```bash
       let long_expr = 3 \
                    + 5  # Equivalent to "3 + 5"

       let long_str = "Hello
                       World"  # Equivalent to "Hello\n World"
       ```

    Note: *Content within quotes does not require a continuation character*.

4. Comments
    Comments start with `#`.

---

## V. Control Structures

### **Conditional Statements**
#### **If Condition**
  Supports nesting:

  `if cond1 { ... } else if cond2 { ... } else { ... }`

  Does not use the `then` keyword; code blocks are wrapped in `{}`.

  ```bash
  if True {1} else {if False {2} else {3}}

  if x > 10 {
      print("Large")
  } else if x == 10 {
      print("Equal")
  } else {
      print("Small")
  }
  ```

#### **Match Statement**
   Replaces the switch statement in bash.
   Supports matching multiple options in one branch, separated by commas.
   Supports regex matching, which needs to be wrapped in '' or "".
   Supports literal matching directly, which will not be interpreted as a variable.

   _To improve matching efficiency, it is recommended to only write regex in quotes; ordinary strings should be written as literals._

  ```bash
  let fruit = "apple"
  match fruit {
    pea, cherry => print "is my favorite",
    "*pple" => print "is my love",
    _ => print "is others"
  }
  ```

### **Loop Statements**
#### **Repeat Loop**
  ```bash
  repeat 10 {a += 1}   # Outputs [1, 2...10]
  ```
#### **For Loop**

  ```bash
  for i in 0..5 {    # Outputs 0, 1, 2, 3, 4
      print(i)
  }

  for i in [1, 5, 8] { print i }

  for i in *.md {      # Supports * expansion
    Into.cp i /tmp/
  }
  ```
  Supports * expansion.
#### **While Loop**
  ```bash
  let count = 0
  while count < 3 {
      print(count)
      count = count + 1
  }
  ```
#### **Loop Loop**
  ```bash
  let count = 0
  let result = loop {             # Statements can also be used as expressions
    print(count)
    count = count + 1
    if count > 3 {
        break count
    }
  }
  print result
  ```

### Statement Expressions

  - Control statements can also be used as expressions:
       ```bash
       let a = if b > 0 {5} else {-5}
       ```

  - Conditional expressions
      ```bash
      a = c > 0 ? t : f
      ```
  Supports nesting.

---

For information on functions, commands, pipelines, error handling, etc., please continue reading:
 - [Syntax Manual 2](syntax2)

For information on function libraries, please read:
 - [Built-in Libs](/doc/libs)  
 - [Bash Syntax Comparison](/glance/bash)
