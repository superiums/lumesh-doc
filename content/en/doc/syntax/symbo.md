---
title: Syntax and Space Rules
date: 2026-07-31 14:31:00
highlight: true
weight: 1
tags:
 - guid
categories:
 - wiki
 - guid
---

## I. Preface: Runtime Modes

- **Auto (Automatic Mode)**: If not set, follows automatic path: single line chooses command priority mode, multi-line chooses expression priority
- **CFM (Command Priority Mode)**: Once enabled, always follows command priority mode, syntax closer to bash shell
- **Normal (Expression Mode)**: Once enabled, always follows expression priority mode, syntax closer to programming languages

Switching: `:` at line start forces Normal; `>` at line start forces CFM.
Defaults: `LUME_CFM` in config file or cli option `-m`/`-M` to enable/disable; not set means auto mode.

Differences:
```bash
# single symbol
ls               # CFM: single symbol executes as command; Normal: single symbol asks for variable value

# individual symbols: `.`, `+`, `-`, `=`
# CFM: symbol immediately followed by character is treated as string (only in parameter positions, command positions unaffected; following a number, unaffected)
# Normal: symbol immediately followed by character is still recognized as operator
print a+b        # CFM: outputs string `a+b`
                 # Normal: outputs arithmetic result
```
Simplest approach: **always keep spaces on both sides of operators** is best practice, so you don't need to worry about mode (though Normal mode tries to allow laziness)

This guide primarily explains in **Normal mode**; operator precedence listed from low to high (within same precedence, indicate associativity). **CFM mode** has a dedicated section.

---

## II. Precedence Overview

```bash
assignment(=, :=)                     lowest, right associative
pipe (|) / redirection
error handling(?. ?+ ?? ?> ?! ?~ ?:)
lambda(->)                     right associative
ternary(?:)                        right associative
logical or(||)
logical and(&&)
comparison(==, >, <, !=, !==)
command arguments (space-separated bare words)
range(.. ..= ... ...)             right associative
addition (+ -)
multiplication/division/modulo(* / %)
exponentiation(^)                   right associative
custom operators(..xxx, __xxx)
unary prefix(! -)
index/property (.)
grouping/call ( ) [ ]
literals
```

Below explains each layer of user writing rules in this order.

---

## III. Assignment

```bash
x = 1          # regular assignment
x := expr      # lazy assignment, expr not evaluated immediately, evaluated when reading variable
x += 1         # compound assignment (addition, subtraction, multiplication, division all have corresponding += -= *= /= forms)
```
Assignment has lowest precedence, right associative, `x = a | b` evaluates pipe first then assigns.
Multiple assignments `let x,y = 1`, `x = y = 1` are both valid.

---

## IV. Pipe `|` and Redirection `>>` `>!`

### Pipe
Pipes support structured data, so no need to `echo data` like bash
```bash
cmd1 | cmd2
data | .upper()        # pipe method: omit library name, directly call method of current value
0...8 |> print 10 + _  # loop dispatch and receiver
data |^ cmd            # PTY pipe
```
If `|` right side is a standalone `.method()`, it's parsed as "pipe method", semantically equivalent to calling that method on left side result, same mechanism as chained call `x.method()`, just write naturally.

### Redirection
```bash
data >> file           # append output to file
data >! file           # overwrite output to file
cmd << file            # read file into command/function
```

Pipe and redirection have **same precedence**, and follow **left associativity**

---

## V. Error Handling Operators `? Series`

These operators follow any expression, low precedence, can wrap entire preceding expression result:

```bash
# success/failure hooks
risky_call() &: next       # execute function on success
risky_call() ?: handler    # execute function on error OR return default value (lazy evaluation)

# flow control (ignore/terminate)
risky_call() ?.        # ignore error
risky_call() ?!        # terminate on error (needed in pipes)

# output conversion
risky_call() ?~        # success -> true, failure -> false
risky_call() ?>        # replace return value with error info

# printing helpers
risky_call() ?+        # print error to standard output
risky_call() ??        # print error to standard error

# empty handler
risky_call() _: handler   # execute/return replacement on empty
risky_call() _! | next    # terminate on empty
```
Writing requirements: operator itself must be written as whole (no space in `?.`), but can have space between operator and preceding expression. Can also directly hang at end of `fn` declaration as that function's default error handler:

```bash
cmd_a ?& cmd_b ?: cmd_c    # same semantics as bash `&& ||`

fn risky(x) {
    ...
} ?: -1
```

**Note**: Here `?` is not ternary's `?` (see next section), distinguished by character following: `?.`/`?+`/`??`/`?>`/`?!`/`?~`/`?:` are fixed error handling symbol combinations; standalone `?` followed by regular expression is ternary operator.

---

## VI. Lambda `->`

```bash
x -> x + 1                # single parameter, no parentheses needed
(x, y) -> x + y             # multiple parameters, parentheses required
() -> 42                    # no parameters
```
Right associative. Left side must be single symbol or (list of symbols), default value/destructuring will error. Right side function body if `{...}` block use directly; ordinary expressions automatically wrapped in block.

---

## VII. Ternary Expression `? :`

```bash
cond ? true_val : false_val
```
`?` and `:` must appear together, right associative. Missing `:` branch (only write `cond ? val`) will error.

---

## VIII. Logical Operators `|| &&`

```bash
a || b
a && b
```
`a && b`, with spaces on both sides. Note lume's two symbols are pure logical operators, different from bash semantics. If need command short-circuit semantics, use `?&` and `?:` instead.

**Note**: `&&` **cannot appear at line start**; must have content before (even just space-separated previous expression).

`&` alone or combined (`&+`, `&-`, `&?`, `&.`) is not logical operator but special marker in command argument context, see Section 16.

---

## IX. Comparison Operators `== > < != !==`

```bash
a == b       # value comparison
a === b      # both type and value comparison
a > b
a != b
```
Write tightly with values on both sides.

`!` appearing alone at standalone/line start is not part of comparison operator but prefix negation, see Section 14; `!=`/`!==` only valid when `!` immediately follows previous value.

---

## X. Command Arguments (Bare Word Call)

### 10.1 General Arguments
```bash
foo x y z        # multiple literals/symbols after foo separated by spaces, recognized as command invocation
```
Condition: caller (`lhs`) must be one of symbol/variable/string/index/property; if `lhs` is already number/list literal and followed by symbol, will error:

```bash
3 foo    # error: cannot have symbol directly after number
foo 3    # correct
```

### 10.2 Extended Arguments
- 10.2.1 Path arguments `./` `../` `/` `.` `..`

  The `/` is special since it also serves as division
  + standalone `/` only indicates path when at command end, like `ls /` is path, `a / b` is division
  + `/a` is path, not division, division should have space after

- 10.2.2 Home directory expansion `~`
  `~` expands to Home directory path: `ls ~`

- 10.2.3 Wildcard expansion `*`
  Since `*` also serves as multiplication, need to distinguish

  + standalone `*` only wildcard when at command end, like `ls *`. While `a * b` is multiplication
  + `*.` `*/` `**/` starting with, are wildcards. While `a *b` is multiplication
  + `*` in paths is wildcard, like `./*`, `../*b`

### 10.3 Output Control and Background

Command tail supports these output control characters:
```bash
# suppress output
cmd &-       # suppress standard output
cmd &?       # suppress error output
cmd &.       # suppress all output

# output redirection
cmd &+       # append error output to standard output

# background execution
cmd &
```

---

## XI. Range Operators `.. ..= ... ...=`

```bash
a..b        # exclude b
a..=b       # include b
..b         # omit start, equivalent to _..b
a..         # omit end, equivalent to a.._
a..b:step   # with step
:2          # standalone step prefix, equivalent to _.._:2 (currently doesn't recognize ..:2)
```
Right associative. Number immediately followed by `..` is treated as "integer literal + range operator", won't be misidentified as decimal point: `1..10`'s `1` is complete integer.

`.` standalone has other meanings (method call, pipe method, relative path), see Section 15, not range operator.

---

## XII. Addition/Subtraction Operators `+ -`

### 12.1 Addition `+`

```bash
a + b     # with space, addition
a+b       # tight write, addition
a +b      # command argument: chmod +x
```
`+` immediately after identifier/number is definitely addition (or `+=`) operator.

### 12.2 Subtraction `-`

```bash
a - b     # both sides with space, subtraction
a-b       # string c
a - -b    # minus sign, negative sign correctly recognized as (-b) in Normal mode, -b is string in CFM mode

# Similar distinction
a -b      # command argument: ls -l
a --b     # command argument: ls --color
```
**Subtraction should have spaces on both sides** (exception: left side non-alphanumeric can be tight: `1-a`)
Tight write after letter `a-b` is hyphen, part of string.

Minus's "negative" meaning, and `-` flag or bare `-` end parameter writing, not subtraction operator, see Section 14 and 16.

---

## XIII. Multiplication/Division/Modulo Operators `* / %`

```bash
a * b;   a / b;   a % b     # both sides with space, definitely operators
a*b      a%b                 # `*` and `%` are more lenient, no space or single side space fine

# Similar distinction
a/b                          # `/` is hyphen, whole is string
1/b                          # when `/` left side non-character, no space is also division sign
ls /                         # ending `/` is path string

ls *                         # ending `*` is wildcard, `*.xx` `*/` `**/` and `*` in paths are wildcards
```
**Division operator should have spaces on both sides** (exception: left side non-alphanumeric can be tight: `1/a`).

---

## XIV. Exponentiation Operator `^`

```bash
a ^ b                        # both sides with space, definitely operator
a^b                          # tight write also works
a ^b                         # exponentiation

# Similar distinction
a^ b                         # `^` is postfix operator, lets a escape variable parsing
```
**Try to avoid using exponentiation as postfix** (exception: left side non-alphanumeric: `1^ a`).

---

## XV. Unary Prefix Operators `! -`

- **`!expr`**: prefix `!` negates:
  ```bash
  !true         # logical negation results in: false
  ```
- **`-expr`**: prefix `-` negates:
  ```bash
  -5            # negative sign
  -(a+b)        # negative sign
  ```

Cannot have space after prefix (must immediately follow number/letter/`(`/`[`/`{`/`.`/`$`)

**Comparison**:
`-` as prefix operator is (negation); as binary operator is (subtraction).

---

## XVI. Index and Property Access `.`

```bash
x[i]                 # index
x[a..b:step]         # slice

obj.method()         # method call/chained call
obj.prop             # property access

a.b().c()            # chained calls auto-merge, no manual nesting needed
```
`.` standalone (before is space/line start) has different semantics—**pipe method prefix**, used to omit library name and directly call method of current value:

```bash
"abc" | .upper()
```
And several path writing forms and decimals:

```bash
./script.sh    # relative path (current directory)
../lib         # relative path (parent directory)
.              # standalone, at end indicates current directory path string
..             # standalone, at end indicates parent directory path string
.5             # equivalent to 0.5
```
**Key point**: `.method()` (tightly after value) and `| .method()` (standalone/line start pipe method) have same semantics, just different positions, write naturally, no need to deliberately distinguish.

---

## XVII. Other Symbols

### 17.1 Postfix `^` after pure symbol indicates "skip evaluation":
```bash
sym^
```

### 17.2 Postfix `!` after pure symbol indicates "execute function":
```bash
add! 3 5        # equivalent to add(3,5)
```

### 17.3 Infix `::` indicates "module call":

```bash
mod::func(x)
mod::sub::func(x)
```
Left side must be pure symbol, can chain multiple levels.

---

## XVIII. Custom Operators

### 18.1 Custom Postfix Operator

Must start with `__`, followed by any symbol
```bash
let __^ = x -> x.upper().blue()
'lume'__^                                 # must be immediately after operand, no space
```


### 18.2 Custom Binary Operator

Must start with `..`, followed by any symbol
```bash
let ..^ = (x,y) -> x^2 + y^2
2 ..^ 3                                  # must have spaces on both sides, distinguished from no-space range operator
```
Binary operators have precedence, can participate in chained binary operations, this is where binary operators are more convenient than functions
- `..+` custom operators have same precedence as `+`
- `..*` custom operators have same precedence as `*`
- `..` other custom operators have highest binary operator precedence (higher than `^`, lower than unary operators)


## XIX. Grouping, Calling, Collection Literals

```bash
a b c          # command call: b and c passed as arguments to command a
foo(x)         # function call: no space between function name and parentheses
(1, 2)         # grouping/tuple expression
[1, 2, 3]      # List
{a: 1, b: 2}   # Map (ordered)
M{a: 1, b: 2}  # alternative syntax for Map
H{a: 1, b: 2}  # HashMap (unordered)
S{1, 2, 3}     # BSet (ordered)
%{ ... }       # isolated scope block, same syntax as {}, but has independent scope
```

### Calling

- Command call
`cmd a b`
**In non-strict mode, symbol is first queried if it's a variable, if so evaluated**

If you want to pass a literal a itself:
  + Enable strict mode: `lume -s` or `sys.set_strict 1`
  + Use quotes: `cmd 'a' 'b'`
  + Use variable escape symbol: `cmd a^ b^`

- Function call
**Function call parentheses must be tightly after function name**: `foo (1,2)` with space between will be broken into independent grouping parentheses, not function call.

- Built-in functions support above 3 calling styles
But **when parameters contain lambda, block, and other complex statements, prefer function call style**

- Custom functions
  + Should use function call style
  + If need flat command call style, add `!` suffix after function name: `foo! 1 2`

### Collections
**Collection literal prefixes `H{`/`M{`/`S{}` must be written at line start/after space**, cannot be tightly after other alphanumeric characters.

**Map literal rules**:
- Key can only be symbol or string, not number/expression.
- Value can be literal, variable, symbol, List, or another Map/HashMap/BSet nested, or any **expression**:
  ```bash
  {a: 1+2}     # error
  let x = 1+2
  {a: x}       # correct: evaluate first then put in
  ```
- Value can be omitted, get same-named variable: `{a, b}` equivalent to `{a: a, b: b}`.
- **When only one key and no colon/comma, `{a}` is parsed as "single statement code block" not single-key map**, to express single-key map must write `{a: a}` or add trailing comma `{a,}`. More recommended is `M{a}`.
- Spaces before/after `:` are optional


---

## XX. Literals

### 20.1 Numbers

```bash
1_000_000        # _ as thousands separator, can appear anywhere in integer/decimal
3.14_159
0b1010_1010      # binary
0o755            # octal
0xff_80          # hexadecimal

50%              # Float: 0.5, % must be tight to number
100M             # FileSize: 100MB, unit must be tight to number

# Similar
192.168.0.1      # IP address, recognized as whole string, not split into number + . operator
```
`.5` valid (omits integer part); `3.` (no decimal part) invalid, need write full `3.0`.

### 17.2 Strings

```bash
"..."       # escaped string
'...'       # raw string
`...`       # template string
r'...'      # regex literal
t'...'      # time literal
s'...'      # safe string (never evaluated as expression)
b'...'      # byte literal
```
No space between prefix letter and quote. Protocol headers starting with below will be directly treated as string, no quotes needed: `https://`, `http://`, `ftp://`, `ftps://`, `file://`.

### 17.3 Special Value Symbols

```bash
true false      # boolean
none            # null
_               # standalone use is parameter placeholder (not `_` inside identifier)
```
`_` usage examples:
```bash
data | cmd _ suffix   # pipeline data placeholder
1.._                  # represent infinite endpoint
a[1.._]               # represent infinite endpoint
ls _                  # command blank argument placeholder
```

---

## XVIII. Control Flow Statements

### 18.1 `if`/`while`/`for`/`loop`

Body must have `{}` (or `%{}`) block:

```bash
if cond { a }

for item in list { ... }
for i, item in list { ... }   # with index, index first, comma separated

while cond { ... }
loop { ... }
```
`else` exception, can be `else if`, block, or single line expression:
```bash
if cond { a } else if cond2 { b } else { c }
if cond { a } else print("no")
```

- When using loops, recommend `for`, more efficient than other loops.
- Above statements can also be used as expressions, written on right side of `=`.
- Loop in assignment environment or pipeline environment will automatically collect return data each iteration.

### 18.2 `match`

Branches separated by **newline**, multiple patterns in same branch joined by **comma**:

```bash
match x {
    1, 2, 3 => "small"
    a..b => "range"
    'foo' => "string match"
    g'\d+' => "regex match"
    _ => "default"
}
```

---

## XIX. Declaration and Statement Keywords

```bash
let x = 1
let x, y = 1, 2                # variable count must match value count, or 1 value broadcast to all variables
let x := expr                  # lazy assignment
let [a, b, *rest] = list       # array destructuring
let {a, b: renamed} = map      # Map destructuring, {a} shorthand, {key:newName} rename
set x = expr                   # modify parent/global scope variable
alias name = expr              # command alias
export name [= expr]           # export variable
del name                       # delete variable
use "path/to/mod.lm" [as m]    # import module
```
Keywords only effective at line start/after space, safe as method name: `regex.match(x, y)` won't be misidentified as `match` statement.

---

## XX. CFM Mode Exclusive: Command Argument Writing

**In CFM (command priority) mode**

Correct writing:
```bash
git tag v0.0.1        # parameter positions allow ., compatible with version number writing
dd if=/dev/zero       # parameter positions allow =
IFS=''                # = at command name position is for variable assignment
ls                    # command
```

Incorrect writing:
```bash
print math.max(a)     # `.` cannot be recognized as method call
# Only affects parameter positions `.`, doesn't affect command position `.`, directly write `math.max(a)` is correct
let a=1               # `=` cannot be recognized as assignment, `a=1` as whole is treated as symbol
# Only affects parameter positions `=`, doesn't affect command position `=`, directly write `a=1` is correct
```

**Key difference**:
- **Normal mode**: symbols are treated as operators, single symbol treated as variable query.
- **CFM mode**: certain symbols are treated as parts of string, single symbol treated as command execution.

**Note**
When mode not explicitly set, in auto mode, i.e., single line automatically in **CFM mode**, multi-line in **Normal** mode.
---

## XXI. Decorators `@`

```bash
@deco
fn name(...) { ... }
```
Written at expression start (line start/after space) is decorator for function declaration, can stack multiple, each on its own line.

---

## XXII. Escaping and Line Continuation

- Inside strings `\` escapes next character (multi-byte characters skipped as whole).
- Line ending `\` + newline is continuation character, allows writing single statement over multiple lines.
- `\` in path/parameter scanning also escapes spaces: `ls foo\ bar` recognized as single whole path.

---

## XXIII. Variable/Command Name Allowed Characters

Besides letters numbers underscore, normal symbols also allow `~ ? & # $ @ - / \` (compatible with path/command line parameter writing, like `connman-gtk`). These characters will be split into operators at specific positions (see earlier sections), **for pure variable name, recommend only letters, numbers, underscore to avoid ambiguity**.

---

## Summary:

1. Precedence from low to high main line: `assignment < pipe < error handling < lambda < ternary < logical or < logical and < comparison < command arguments < range < addition/subtraction < multiplication/division/modulo < exponentiation < unary < index/property < grouping`.
2. Binary operators try to have spaces on both sides, can be lazy when no ambiguity.
3. Prefix, infix, postfix operators have different space rules.
4. When encountering errors, try adding `:` at line start to switch mode and see.

*Progress: Completed file 4/15*
