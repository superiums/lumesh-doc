---
title: The Muddy Road of Bash
date : '2026-08-15T14:45:13+08:00'
weight: 1
highlight: true
layout: slides
fullWidth: true
showTableOfContents: false
---

{{< slide type="hero" tag="Deep Dive Comparison · 2026" sub="Bringing scripts back to nature and simplicity" >}}
A systematic review of Bash's common pitfalls and counter-intuitive designs,
Compared with Lume's solutions
{{< /slide >}}

{{< slide type="compare" title="1. Syntax Basics · Whitespace Sensitivity" >}}
{{< code side="bash" >}}
```bash
# ❌ Wrong
if[$a==$b]; then ...

# ✅ Correct
if [ "$a" == "$b" ]; then ... fi
# [ is a command, not syntax
# = must have spaces on both sides
```
{{< /code >}}
{{< code side="lume" >}}
```bash
a == b
if $a == $b { ... }
# Spaces are entirely optional
# expressions written directly
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Syntax Basics · Assignment Syntax" >}}
{{< code side="bash" >}}
```bash
# Wrong
a = 1

# Correct
a=1
# No spaces allowed in assignment,
# otherwise a is treated as a command
```
{{< /code >}}
{{< code side="lume" >}}
```bash
a=1
a = 1    # Equivalent, spaces optional
# Spaces required in CFM mode
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Strings & Quotes · Implicit Variable Splitting" caption="No quotes = implicit split + glob" >}}
{{< code side="bash" >}}
```bash
file="a b"

rm $file
# Parsed as rm a b, deletes two files

rm "$file"
# Quotes are a required amulet ✅
```
{{< /code >}}
{{< code side="lume" >}}
```bash
rm $file   
# Parsed as rm 'a b', matches intuition
# Unless IFS and LUME_IFS_MODE are modified
# Whitespace in arguments is not split
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="Strings & Quotes · Fine-Grained IFS Control" >}}
Bash's `IFS` is global, affecting all strings; Lume uses `LUME_IFS_MODE` to independently control each scenario:

```bash
# IFS affect: 0:never; 2:cmd args;
# 4:for; 8:string.split; 16:csv;
# 32:pick; 62:all
set LUME_IFS_MODE = 2
# Only splits in command args, 
# other scenarios unaffected
```

| Bit | Scenario | Meaning |
|-----|---------|---------|
| `1<<1` | `IFS_CMD` | Command string argument splitting |
| `1<<2` | `IFS_FOR` | `for i in str` iteration splitting |
| `1<<3` | `IFS_STR` | `string.split` default delimiter |
| `1<<4` | `IFS_CSV` | CSV parsing |
| `1<<5` | `IFS_PCK` | `ui.pick` option splitting |
{{< /slide >}}

{{< slide type="compare" title="Strings & Quotes · Variable Expansion Hidden Dangers" >}}
{{< code side="bash" >}}
```bash
input='$(rm -rf /)'
eval "echo $input"   # Boom
```
{{< /code >}}
{{< code side="lume" >}}
```bash
let input = '(rm -rf /)'
let safe = into.safe $input
eval_str `echo $safe`
# Prints the safe string s'(rm -rf /)'
# StringSafe type guarantees eval
# won't accidentally detonate
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Strings & Quotes · String Types" >}}
{{< code side="bash" >}}
```bash
str='hello world'
# Single quotes: raw string
"hello\nworld"
"hello $name"
# Double quotes, both escaping and interpolation



# Quotes within quotes -> nesting hell
```
{{< /code >}}
{{< code side="lume" >}}
```bash
str='hello world'
# Single quotes: raw string,
# only \' is escaped
"hello\nworld"
# Double quotes: 
# handle escape sequences only
`output: $var {var * 2}`
# Backticks: template string, 
# escaping + variable interpolation

r#'...'#  r#"..."#
# Inner quotes need no escaping
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Strings & Quotes · Quote Hell" >}}
{{< code side="bash" >}}
```bash
echo "Today is $(date "+%Y-%m-%d")"
sed "s/foo/bar \"$var\"/g" file.txt

curl -X POST -d \
"{\"name\":\"$name\", \
\"msg\":\"Hello '$msg'\"}" \
http://example.com
```
{{< /code >}}
{{< code side="lume" >}}
```bash
print `Today is {date '+%Y-%m-%d'}`
sed `s/foo/bar "$var"/g` file.txt

curl -X POST -d \
r#`{"name":"{name}", \
"msg": "Hello '$msg'"}`# \
http://example.com
# Backtick interpolation + hashed string,
# inner quotes written directly

let data = {name, msg: `Hello '$msg'`}
curl -X POST -d into.json($data) \
http://example.com
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Strings & Quotes · Multi-line Text" >}}
{{< code side="bash" >}}
```bash
cat <<EOF
line1
line2
EOF
```
{{< /code >}}
{{< code side="lume" >}}
```bash
let a = 'line1
line2'
# All three string quote types 
# directly support multi-line text
# No heredoc patch needed
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Strings & Quotes · Case Operations" >}}
{{< code side="bash" >}}
```bash
[[ "${a,,}" == "${b,,}" ]]
```
{{< /code >}}
{{< code side="lume" >}}
```bash
a.lower() == b.lower()
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="2. Type System · Default String Type" >}}
{{< code side="bash" >}}
```bash
a=1
b=2
c=$a+$b
echo $c
# 1+2 (string concatenation, not addition)

# ✅ Must
((c=a+b))
c=$((a+b))
```
{{< /code >}}
{{< code side="lume" >}}
```bash
# Variables have explicit types,
# operations written directly, 
# no special syntax needed
a + b
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Type System · Numeric Types" caption="Full support for integers and floats, explicit errors on overflow" >}}
{{< code side="bash" >}}
```bash
echo $((1/2))   # 0 (integer division)
echo "scale=2; 1/2" | bc   # 0.50

echo $((99999999999999999999999))
# Bizarre number 
# (integer overflow happens silently)
```
{{< /code >}}
{{< code side="lume" >}}
```bash
1 / 2         # 0   integer division
1 / 2.0       # 0.5 float division
a - b / c * d ^ 2
# Written naturally, no external tools needed
math.sin(x)
# Advanced math, using the math library

echo 99999999999999999999999     # Error
# syntax error: expect Integer,
# found error: number too large
# to fit in target type
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Type System · Array Syntax" >}}
{{< code side="bash" >}}
```bash
arr=(a b c)

echo $arr              # a (only takes the first element)
echo ${arr[@]}         # a b c
echo "${arr[0]}"       # a
echo "${arr[-1]}"      # last element (Bash 4.3+)
echo "${#arr[@]}"      # array length
echo "${arr[@]:1:2}"   # slice: start at index 1, take 2

echo "${map["name"]}"
echo "${!map[@]}"      # all keys
echo "${#map[@]}"      # number of elements
```
{{< /code >}}
{{< code side="lume" >}}
```bash
arr = [a, b, c]
print arr
print arr[0]
print arr.len()
print arr[1..-1]    # slice
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Type System · Index Reordering" >}}
{{< code side="bash" >}}
```bash
arr=(a b c)
unset arr[1]
echo "${arr[@]}"
# a c (indices remain 0 2, not 0 1)
```
{{< /code >}}
{{< code side="lume" >}}
```bash
arr = [a, b, c]
arr.remove_at(1)
# → [a, c], indices become 0, 1,
# automatically reordered
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Type System · Associative Arrays Must Be Declared First" >}}
{{< code side="bash" >}}
```bash
map["a"]=1       # ❌ regular array
declare -A map
map["a"]=1       # ✅ associative array
```
{{< /code >}}
{{< code side="lume" >}}
```bash
a = [1, 2]          # array
m = {a: 1, b: 2}    # map
# Array and map types are independent,
# syntax is intuitive
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="Type System · Nested Access">}}
Bash does not support accessing nested data structures. Lume supports SQL-style `select` and dot-path `get`:

```bash
# select: pick columns from List[Map]
# (similar to SQL SELECT)
fs.ls -l | select name size modified

# get: dot-path access into nested structures
let config = {db: {host: "localhost", port: 5432}}
config.db.host            # direct property access → "localhost"
config | get "db.host"    # pipeline access
get config "db.port"      # function access

# literals can be nested freely
[1,24,5,[5,6,8]][3][1]     # shows 6
```
{{< /slide >}}

{{< slide type="text" title="Type System · Special Types">}}
In Bash, ranges, regex, and time are all represented as strings with no dedicated type; in Lume, all types can participate in operations.

```bash
# Range type
1..10       # half-open interval [1, 10),
# lazy Range object
1..=10      # closed interval [1, 10]

1..10:2     # step of 2: 1, 3, 5, 7, 9
_..5        # from Int::MIN to 5
1..10 ~: 3  # true

# File size and percentage literals
50%         # → 0.5 (Float)
3M          # → FileSize(3MB)
1.5G        # → FileSize(1.5GB)
3M > 1G         # → false
filesize.b(1K)  # → 1024

# Regex and time literals
g'\d+'
t'2026-7-23'
t'08:10' - t'08:09'  # time diff (ms): 60000
```
{{< /slide >}}

{{< slide type="compare" title="3. Operators & Conditionals · [ ] vs [[ ]] vs (( ))" caption="Forget the tedious brackets">}}
{{< code side="bash" >}}
```bash
[[ "$a" =~ ^[0-9]+$ ]]
[[ "$a" > 0 && "$b" < 0 ]]
# Use [[ ]] over [ ] whenever possible

[ "$a" -eq "123" ]   # integer comparison
[ "$a" = 123 ]       # string comparison
(( a == 123 ))       # clearer
```
{{< /code >}}
{{< code side="lume" >}}
```bash
a ~: g'\d+'     # regex match
a > 0 && b < 0  # logical operation
# No brackets, no double-bracket syntax


a == b          # unified comparison
# across all types, no brackets needed
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Operators & Conditionals · Regex and Wildcards" >}}
{{< code side="bash" >}}
```bash
[[ "$a" == *.log ]]     # wildcard
[[ "$a" =~ \.log$ ]]    # regex
```
{{< /code >}}
{{< code side="lume" >}}
```bash
ls *.log
for f in *.log { ... }

$a ~: '.log'        # string contains
$a ~: r'.log$'      # regex match
# ~: can also check whether 
# sets/lists/ranges/map keys contain
#  the right-hand expression
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Operators & Conditionals · Return Value Semantics" caption="Counter-intuitive">}}
{{< code side="bash" >}}
```bash
grep "abc" file.txt
echo $?   # 0 means found

if grep "abc" file.txt; then
    echo "found"
fi
# true = 0, false ≠ 0
```
{{< /code >}}
{{< code side="lume" >}}
```bash
let found = fs.read file.txt | .grep('abc')
if found {     # equivalent to if !found.is_empty()
    echo 'found'
}
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="4. Control Flow · Pattern Matching" >}}
{{< code side="bash" >}}
```bash
case $1 in
    a)
        echo "a"
        ;;&
    b|c)
        echo "bc"
        ;;
    (b|c)
        echo "ok"
        ;;
esac
```
{{< /code >}}
{{< code side="lume" >}}
```bash
match x {
    1, 2, 3      => "small"          # multi-value match
    4..10        => "medium"         # range match
    r'^\d+$'     => "numeric str"    # regex match
    "none", none => "empty"          # string + none
    _            => "other"          # fallback
}
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="Control Flow · As Expressions">}}
In Bash, control flow is a statement and cannot be assigned as an expression. In Lume, control flow can be used as an expression:

```bash
# Statement context: no return value
for i in 1..5 { print i }

# Assignment context: returns a List
let squares = for i in 1..5 { i * i }   # → [1, 4, 9, 16, 25]

# Pipeline context: returns a List and continues flowing
for i in 1..10 { i * 2 } | list.filter(x -> x > 10)

# if expression
let result = if x > 0 { "positive" } else { "non-positive" }
```
{{< /slide >}}

{{< slide type="compare" title="5. Function System · Parameter Passing & Return Values" >}}
{{< code side="bash" >}}
```bash
foo() {
    echo "$1"
    # no parameter names, no types,
    #  no default values
}

foo() {
    return 100 
    # can only return exit codes 0-255
}

foo() { echo "hello"; }
res=$(foo)
# returning strings requires 
# global variables
# or command substitution

time() { echo "my time"; }
time          # calls the function, 
# shadowing the external command
# of the same name
command time
# forces calling the external command
```
{{< /code >}}
{{< code side="lume" >}}
```bash
fn greet(name, greeting="Hello") {
    println greeting ", "  name "!"
}
greet("Alice")        # Hello, Alice!
greet("Bob", "Hi")    # Hi, Bob!

fn sum(*nums) {
    nums | list.fold((acc, x) -> acc + x, 0)
}
sum(1, 2, 3, 4, 5)   # 15

time()   # calls the function,
# doesn't affect external command time
time _   # calls the external command
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="Function System · Lambdas, Closures & Currying" >}}
Bash does not support lambdas
{{< code side="lume" >}}
```bash
# Lambda
let double = x -> x * 2
let add = (x, y) -> x + y

let base = 10
let adder = x -> x + base
# closure: automatically captures free variables

fn make_adder(base) {
    x -> x + base          # returns a lambda that remembers base
}
let add5 = make_adder(5)
add5(3)    # 8
add5(10)   # 15

let multiply = (x, y) -> x * y
let double = multiply(2)   # currying (partial application)
# returns a new lambda, awaiting the second argument
double(7)    # 14
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Function System · Decorators" caption="Execution order: logger.before → timer.before → function body → timer.after → logger.after">}}
{{< code side="bash" >}}
```bash
# Bash does not support decorators
```
{{< /code >}}
{{< code side="lume" >}}
```bash
@logger("debug")
@timer
fn my_function(x) {
    x * 2
}
# Decorators return a [before_fn, after_fn] list
# NAME, ARGS, RESULT variables accessible 
# within the decorator environment
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="6. Scope & Variables · Variables Default to Global" >}}
{{< code side="bash" >}}
```bash
func() {
    a=1
}
func
echo $a   # 1 (global pollution)
```
{{< /code >}}
{{< code side="lume" >}}
```bash
let a = 5
fn add() { a = 1 }
add()
print a   # 5 
# the a inside the function is local;
# modifying the parent requires explicit set
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Scope & Variables · Undefined Variables" >}}
{{< code side="bash" >}}
```bash
rm -rf /$undefined_dir
# if undefined_dir is empty,
# this is equivalent to rm -rf /
# set -u can save you
```
{{< /code >}}
{{< code side="lume" >}}
```bash
rm -rf /$undefined_dir
# recognized as a literal:
# rm -rf '/$undefined_dir'
rm -rf `/$undefined_dir`
# error: undeclared variable `undefined_dir`
# the default value of an undefined variable
# is none, not an empty string
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="7. Processes & Pipelines · Subshell Trap">}}
In Bash, the right side of a pipeline, command substitution `$()`, and `()` all run in a subshell — variable changes don't propagate back to the parent process:
{{< code side="bash" >}}
```bash
count=0
seq 10 | while read i; do
    ((count++))
done
echo "$count"   # 0 (variable changes lost)

# ✅ Alternative
while read x; do a=1; done <<< "123"
```

```bash
a=1
b=$(a=2; echo $a)
echo "$a"   # 1 
# subshell can't modify the parent shell's variable

(a=1)
echo "$a"   # empty (changes in subshell are lost)
```
{{< /code >}}

The subshell can't modify the parent shell's variables — this is an iron law of Bash. In addition, Bash pipelines can only pass text byte streams via `stdout`/`stdin`; you must `echo` to pass data out:

```bash
echo "hello" | wc
```
{{< /slide >}}

{{< slide type="text" title="Processes & Pipelines · Lume's Pipeline System">}}
Four pipeline types, supporting structured data, no `echo` needed, and no subprocess started:

{{< code side="lume" >}}
```bash
data | process              # standard pipeline: supports structured data (List/Map passed directly)
data | positional a _ c     # positional pipeline: _ is a placeholder, data injected at the specified position
data |> transform           # dispatch pipeline: applies the right-hand function to each element of a collection separately
data |^ interactive         # PTY pipeline: for interactive programs like vi/ssh/htop

"hello" | wc

# Structured data
fs.ls -lh | where(size > 5K)
[1,2,3,4,5] | .filter(x -> x > 2) | .map(x -> x * x)

# Data is not lost
(a=1)
print $a    # 1

# Loop dispatch
ls -1 |> cp -r _ /tmp/     # runs cp for each file
```

Chained calls (more convenient data flow than pipelines):

```bash
"hello world".split(' ').join(',')    # → "hello,world"
[3,1,2].sort().rev()
data | .filter(x -> x > 0)
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="8. IO & String Handling · Output Commands" >}}
{{< code side="bash" >}}
```bash
echo "-n"   # treated as an argument

# ✅ Safer
printf "%s\n" "$var"
```
{{< /code >}}
{{< code side="lume" >}}
```bash
print "-n"
# print statement is faster and safer
# than third-party echo
```
{{< /code >}}
{{< /slide >}}


{{< slide type="compare" title="IO & String Handling · Redirection" >}}
{{< code side="bash" >}}
```bash
cmd > out.txt   # silently overwrites the file
# ✅ To prevent accidental operations
set -o noclobber

command > all.log 2>&1
command > /dev/null 2>&1
# dense symbols, counter-intuitive semantics

command 2>&1 > out.log   # Wrong
```
{{< /code >}}
{{< code side="lume" >}}
```bash
cmd _ >! out.txt
# Use >! instead of >, more conspicuous
# Append operation >> unchanged

command &+ > all.log      # merge
command &.                # ignore
# Error redirection, simple and direct
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="9. Wildcards & File Operations · Unmatched Wildcards" >}}
{{< code side="bash" >}}
```bash
# If no .log files exist

rm *.log
# Attempts to delete a file
# literally named '*.log'

# Solution: defensive coding
shopt -s nullglob
```
{{< /code >}}
{{< code side="lume" >}}
```bash
# If no .log files exist

rm *.log
# Error: wildcard not matched: `*.log`

# Solution: ignore or catch
rm *.log ?.
# Ignore the error, continue execution
rm *.log ?: do_handler
# Continue only after handling the exception
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Wildcards & File Operations · for Loops and Filenames" >}}
{{< code side="bash" >}}
```bash
for f in *; do
    echo "$f"
    # variables always need quotes
done
```
{{< /code >}}
{{< code side="lume" >}}
```bash
for f in ./* {
    print $f
}
# No extra quotes needed for variables
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="10. Module Import" >}}
{{< code side="bash" >}}
```bash
# utils.sh
MY_CONSTANT="hello"
my_func() { echo "util function"; }

# main.sh
source utils.sh
# Now, MY_CONSTANT and my_func
# are exposed naked in the global namespace.
# If two libraries define functions
#  with the same name,
# the latter mercilessly overwrites 
#  the former, with no warning at all.
# No module system, no namespaces.
# Large bash script projects
#  inevitably evolve into
# one giant global-namespace junkyard.
```
{{< /code >}}
{{< code side="lume" >}}
```bash
# Using modules, with clean, clear namespaces
use myutils as utils
utils::my_function()

# 17 built-in modules, loaded on demand,
#  never pollute the global environment
list.map(...)
# list operations
string.split(...)
# string operations
fs.read(...)
# file operations
time.now()
# time operations
math.sqrt(16)
# math functions
regex.find(g'\d+', text)
# regex operations
ui.pick("Choose one:", options)
# interactive selection
# Used to simple merging? 
# Lume supports that too: include
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="11. Error Handling & Debugging · Doesn't Error or Stop by Default" >}}
{{< code side="bash" >}}
```bash
# Bash by default doesn't error,
#  doesn't stop, doesn't warn
# Strongly recommended to add 
# at the top of scripts:
set -euo pipefail
# -e: exit immediately on command failure
# -u: error on undefined variables
# -o pipefail: pipeline fails 
# if any command in it fails
```
{{< /code >}}
{{< code side="lume" >}}
```bash
# Compiler-level error messages,
#  out of the box:
# 3 lines of context 
# before and after the error
# Precise line and column numbers
# Red-highlighted error location,
#  ^~~~ pointer arrow
# Specific error description 
# and fix suggestions
# Automatically terminates on error,
#  unless the exception has been handled
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Error Handling & Debugging · Debugging Tools" caption="tap is a pipeline debugging tool, prints intermediate results without interrupting data flow">}}
{{< code side="bash" >}}
```bash
echo "DEBUG: x=$x"
echo $?
set -x    
# extremely noisy, like a tsunami
# bash: syntax error near
#  unexpected token '('
# Do you know which line it is?
```
{{< /code >}}
{{< code side="lume" >}}
```bash
# Dedicated debugging statements:
# debug, ddebug, typeof, assert, condition
# Logging module: log

[1, 2, 3] | list.map(x -> x * 2) \
| tap | list.filter(x -> x > 3)
#  ↑ prints the intermediate result,
#  data continues flowing without loss
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="Error Handling & Debugging · Error Catch Mechanism">}}
Bash relies on exit codes to judge success/failure, with crude error handling. Lume provides 7 suffix error-catch operators:
{{< code side="lume" >}}

```bash
# Success/failure hooks
risky_call() &: next
# execute function on success
risky_call() ?: handler
# execute function or return default on error (lazy evaluation)

# Flow control (ignore/terminate)
risky_call() ?.
# ignore the error
risky_call() ?!
# terminate on error (only needed within a pipeline)

# Output conversion
risky_call() ?~
# convert success/failure into a boolean
risky_call() ?> 
# replace the return value with the error message

# Print helpers
risky_call() ?+
# print error to stdout
risky_call() ?? 
# print error to stderr

# None-value hooks
risky_call() _: handler
# execute on encountering none
risky_call() _! | next
# terminate on encountering none (only needed within a pipeline)
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="Error Handling & Debugging · Error Catch Mechanism">}}
Practical patterns:
{{< code side="lume" >}}

```bash
# Bash-like && ||
validate() &: process() ?: cleanup()
validate() ?~ ? process() : cleanup()

# Capture error info, handle programmatically
risky_command ?: (e) -> {
    println "Operation failed"
    println "Error code: " e.code
    println "Error message: " e.msg
    println "Error location: " e.expr
    default_value    # return a default value, graceful degradation
}

# Read config file, fall back to default on failure
let config = fs.read "config.json" ?: "{}"

# Actively throw an error
fn divide(a, b) {
    if b < 0 { throw "Divisor cannot be negative" }
    a / b
}
divide(10, -1) ?: (e) -> { println e.msg }
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="12. Background Tasks" >}}
{{< code side="bash" >}}
```bash
sleep 1000 &
exit   # sleep is still running

# ✅ Correct approach
trap 'kill $(jobs -p)' EXIT
```
{{< /code >}}
{{< code side="lume" >}}
```bash
sleep 1000 &
exit        # sleep exits along with it

jobs        # view background tasks
jobs -k id  # terminate a background task
# All background tasks automatically exit
#  when the main process ends
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="13. Interaction & UI · Colors and Display" >}}
{{< code side="bash" >}}
```bash
"\033[31;1merror\033[m"
# Color display requires 
# hand-writing ANSI escape codes


read -p "your choose:"
# Interaction relies on text-based Q&A
```
{{< /code >}}
{{< code side="lume" >}}
```bash
'hi lume'.green().bold()
COLOR.red + 'hello'
STYLE.BOLD + 'lume'

fs.ls -lh | ui.pick 'select a file'
# Built-in color functions and 
# COLOR constants, integrated interactive UI
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="Interaction & UI · Modern Interaction Capabilities" caption="Bash completely lacks the following modern interaction capabilities">}}

{{< code side="lume" >}}

**Abbreviation expansion**    Expands on space

**Programmable hotkeys**      Hotkeys can modify the current input line, but not the `env`

**Programmable slash commands**  Can modify the `env`, but not the input line

**Programmable prompt**       Supports custom functions, supports `starship`

**Syntax highlighting themes**  Change themes or customize individually

**Auto-completion**:          Commands, arguments, paths, history, and built-in functions can all be auto-completed.

**AI completion**            Supports openai-compatible APIs
{{< /code >}}

{{< /slide >}}

{{< slide type="perf" title="14. Performance: Loop Summation 1 Million Times" bashMs="2224" lumeMs="199" speedup="11.2" note="1 million loop summation iterations" >}}
{{< /slide >}}

{{< slide type="text" title="Summary">}}
Bash's many pitfalls are not accidental, but an inevitable consequence of its design philosophy: **everything is text, everything is a command**. This philosophy was revolutionary at Unix's birth, but its cost becomes increasingly apparent against the demands of modern scripting:

- **Lack of types**: Strings, integers, arrays, and booleans have no underlying distinction, forcing arithmetic to require special syntax and comparisons to require different operators — one misstep and semantics become chaotic.
- **Implicit behavior**: Variable expansion, word splitting, and glob expansion are enabled by default; omitting quotes is a landmine.
- **Subshell isolation**: Pipelines, command substitution, and `()` all spawn subprocesses, so variable changes can't propagate back, blocking data flow.
- **Defensive programming**: `set -euo pipefail`, `shopt -s nullglob`, quotes, `[[ ]]` — each one is a lesson learned in blood and tears.

Lume's design starts from a different direction: **safe defaults, explicit over implicit**.

- **Type system**: Integers, floats, arrays, maps, ranges, regex, and time literals each have their own type; operations are written directly, no parenthesis magic needed.
- **Safe defaults**: Errors on undefined variables, errors on unmatched wildcards, automatic termination on command failure — no need to manually enable defensive modes.
- **Consistency**: Spaces are optional, `==` unifies comparison, `~:` unifies matching — no need to memorize the differences between `-eq` / `=` / `==`.
- **Modern language features**: Lambdas, closures, currying, decorators, pattern matching, control-flow expressions — complex logic no longer requires "switching languages".
- **Structured pipelines**: Pipelines carry structured data, start no subprocesses, and data is never lost.

{{< /slide >}}

{{< slide type="text" title="Summary">}}

| Dimension | Bash | Lume |
|------|------|------|
| Type system | Everything is a string | Full type system |
| Default behavior | Lax, requires manual defenses | Strict, safety-first |
| Arithmetic | `$(( ))` / `bc` | Written directly |
| Conditionals | `[ ]` / `[[ ]]` / `(( ))` | Written directly |  
| Pipelines | Text stream, subprocess | Structured data, no subprocess |  
| Error handling | Exit codes, requires `set -e` | Auto-terminates, 10 catch operators |  
| Functions | Positional args, exit-code return | Named args, arbitrary return types |  
| String interpolation | Quote hell | Backtick templates, no nested escaping |  
  
Bash's survival rule is "anything that can be omitted will eventually explode"; Lume's design goal is to make the most natural way of writing also the correct way.  
{{< /slide >}}
