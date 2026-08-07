---
title: Lume Built-in Functions Usage Guide
date: 2025-12-11 19:16:45
highlight: true
weight: 11
tags:
 - libs
categories:
 - wiki
 - libs
---


## General Rules

### 1. All four calling styles are valid

```bash
string.upper "abc"        # imperative
string.upper("abc")       # functional
"abc".upper()             # method style (recommended for chaining)
"abc" | string.upper()    # pipe style
"abc" | .upper()          # pipe method style (omit library name)
```
Since functional parameters have lower precedence than imperative, functional supports **lambda**, **pipe**, **block** and other types of parameters; imperative can only add parentheses to wrap as a group.

When pipe `.func()` omits library name, Lume will automatically find the corresponding module based on the passed value type (string → `string`, list → `list`, integer/float → `math`, Map → `map`, Set → `bset`, Table → `table`, etc.). So as long as you know how to use method chaining for one module, the usage for other modules is exactly the same, no need to memorize separately.

By default, the pipe will inject the left-side result into the **first parameter position** of the right-side expression. If customization is needed, you can explicitly use `_` to specify parameter injection position on the right side of the pipe.

For constant usage, please refer to
- [MATH](MATH)
- [COLOR](COLOR)
- [STYLE](STYLE)

### 2. When passing `<fn>` parameter, arrow functions can be used
If the function is a literal, functional parameter must be used, imperative parameter cannot be used.

`filter`/`map`/`find`/`fold`/`sort` etc. hint `<fn>` in hint is this syntax:

```bash
list.filter(data, x -> x > 0)
data | .filter(x -> x > 0)
list.fold(data, (acc, x) -> acc + x, 0)

# imperative will not parse successfully
list.filter data  x -> x > 0         # error
list.filter data  (x -> x > 0)       # can, but not recommended
```

Single parameter can omit parentheses (`x -> ...`), multi-parameter must add parentheses (`(acc, x) -> ...`). The parameter names in hint (e.g., `fn(item,acc)`) just tell you arrow functions pass values by position, they don't need to match the names in hint, just need to be in order.

### 3. `<num1> <num2>... | <array>` means both parameter passing methods work

`max`/`min`/`sum`/`average`/`select` and similar functions support:

```bash
math.max(1, 2, 3)      # multiple positional parameters
math.max([1, 2, 3])    # or directly pass an array/list
```

Both writing styles have completely the same effect, use whichever is more convenient.

### 4. Most "modify" functions don't modify original values, but return new values

Lume's built-in data structure operations (List/Set/Map/Table) are almost all immutable: `insert`/`remove`/`sort_by`/`shuffle` etc. return **new copies**, original variables won't be modified in-place. To keep results, need to reassign:

```bash
let s2 = s.insert(3)   # s itself doesn't change, s2 is new collection after insertion
```

### 5. Numeric literals support base prefixes and underscore separators, global effect

`0x`/`0o`/`0b` prefixes and `_` separators are not exclusive to any conversion function, but syntax itself supports these forms, you can use them anywhere you write numbers:

```bash
let n = 0xff_80
let big = 1_000_000
```

`into.int`/`string.to_int` and similar functions, besides recognizing literal format strings, their actual function is "convert other types (string/float/bool) to integer".

### 6. Parameter validation is strict, wrong count will error immediately

Built-in functions won't automatically fill `None` or ignore extra parameters like some languages, parameter count and hint `<required>`/[`optional`] mismatch will directly throw error and terminate execution. When writing scripts, if you get "expected N args" type error, first check parameter count.

### 7. Error handling suffixes can directly follow function calls

When any statement (built-in function/operator/system command/custom function, etc.) has an error, you can control behavior with error handling operators without using `try/catch`:

```bash
fs.read("no_such_file") ?: "default content"   # use default value on error
fs.read("no_such_file") ?: handler             # call error handler on error, equivalent to catch
fs.read("no_such_file") ?.                     # ignore error, continue execution
risky_call() ?!                                # terminate pipeline immediately on error (automatically terminates outside pipeline, no explicit termination needed)
```

### 8. In lume, `&&` and `||` are pure logical operators, unlike bash
If you need the same effect, please use:
```bash
cmd1 &: cmd2 ?: cmd3
```
Note the meaning is same as bash: when cmd1 succeeds, execute cmd2, any failure in the first two commands will execute cmd3

### 8. To check how specific functions are used, just ask `help`

---

## Selected Function Explanations by Module

> Here are some selected module functions for explanation, as auxiliary to `help` information, to help you quickly master Lume built-in function usage.

Simple modules (boolean, about, filesize, bytes, console) usage can be determined from function names, so they are omitted.

### Top-level Functions (no prefix needed)

#### `eval`/`exec` vs `eval_str`/`exec_str`

`eval`/`exec` handle already parsed expressions (`Group`/`Quote` etc.), `eval_str`/`exec_str` handle string source code—the two groups are easy to mix:

```bash
eval(quote(1+1))          # evaluate already parsed expression
eval_str("1+1")           # parse string first then evaluate, and prints ">> Excuting: ..." hint
exec_str("let x=1")       # same as eval_str, but executes in a new child environment, doesn't pollute current scope
```

`eval_str`/`exec_str` only accept `String`/`Symbol`/`Variable`/`StringTemplate` (or `Group` wrapping string), passing other types errors.

#### `include`/`import`/`use`

`include`/`import` differ only in whether they share current environment: `include` uses `env` (pollutes/reuses current scope variables), `import` uses `env.fork()` (independent namespace import).

```bash
include("lib.lm")   # variables/functions defined in lib.lm directly enter current scope
import("lib.lm")    # returns execution result, but doesn't pollute current scope (need explicit result retrieval if reuse)
```

Both functions are for compatibility with traditional writing habits, more rigorous approach is to use `use` statement to import modules. Differences are:

- `use` uses independent namespace
- Code inside `use` doesn't execute immediately, until explicitly calling a function
- Imported namespace by `use` only has `fn` and module dependencies' `use`

#### `dig`/`get` (shared by top/list/map): dot path can mix accessing Map keys and List indices

Path is split by `.`, each segment can be a Map key or List/Range numeric index, depending on actual type at current level.

```bash
dig(data, "users.0.name")   # data.users is a list, get name field of element at index 0
```

If path doesn't exist, it errors immediately and terminates, not returning `none`, if you need tolerance, use `?.` or `?:`.

#### `format`: `{name}` and `{}` can be mixed

`{name}` gets value from current scope by variable name, `{}` consumes positional arguments starting from the 2nd parameter in order, both replacements don't interfere with each other and can appear together in a template.

```bash
let name = 'lume'
format('hi {name}, result={}', 1+1)   # hi lume, result=2
`hi {name}, result={1+1}`             # hi lume, result=2
```
format supports alignment/fill syntax: `{:*>10}`

| Part | Meaning | Description |
|---|---|---|
| `name` (optional) | variable name | before `:`, if omitted gets from positional parameters |
| `*` | fill character | can be any char, omitted defaults to `' '` |
| `>` / `^` / `<` | alignment | right/center/left alignment respectively |
| `10` | alignment width | can be any integer |

**format** functionality overlaps with `` `template` `` template string functionality, both can interpolate.

But format focuses on formatted output, template focuses on interpolation, if there's no formatting requirement, recommend using template for better performance.

#### `assert` / `when`: fail terminates vs conditionally execute

```bash
assert(x > 0)                         # terminate with error when condition is false
when(x > 0, print("positive"))        # execute only when condition is true, silently skip when false, no error
```

`assert` is used for pre-checks (stop if wrong), `when` is used for optional branches (silently skip if not met).

#### Other Top-level Function Explanations

- **`cd -` goes to previous directory**, relies on internally maintained `LWD` variable, each successful `cd` stores the old current directory into `LWD`.
- **`len` counts Unicode characters** (via `chars().count()`) rather than byte count for strings.

---

### `string` module

**`words` vs `words_quoted`**: `words` splits directly by whitespace; `words_quoted` first recognizes `"..."`/'...' wrapped fragments as a whole, then splits remaining parts by whitespace, suitable for parsing command-line style text.

```bash
string.words 'a "b c" d'          # ["a", "\"b", "c\"", "d"]
string.words_quoted 'a "b c" d'   # ["a", "b c", "d"]
```

**`grep`**: only does substring matching after splitting string by lines (`\n`), returns the entire matched line, doesn't support regex. For regex, use `reg.find_all`.

**`pad_start`/`pad_end`/`center`**: third optional parameter only takes its first character as fill char, passing multi-char string only uses one char; if original string length already meets requirement, returns as-is without truncation.
**`color`/`color_bg`**: color values support three formats -- `#hex`, color name (see `string.colors()`), or `r,g,b` triple format, doesn't support `rgb(...)` style with function name.
**`clr`/`clr_bg`**: color values 0..=255.

#### `string.slice` (`s[a:b]` syntax) out-of-bounds behavior is consistent: silently truncates without error

```bash
"hello".slice(2)        # from index 2 to end -> "llo"
"hello".slice(2, 100)   # end index out of bounds, automatically truncates to string end, no error
"hello".slice(-3, -1)   # supports negative from end
```

When `slice` start/end indices are out of bounds or reversed (`start >= end`), returns empty string instead of error, consistent with language built-in `s[a..b]` range slice syntax. Both can be used interchangeably, choice is purely style preference.

#### `string.remove_prefix`/`remove_suffix`: if not found, returns as-is, not error

```bash
"hello.txt".strip_suffix(".txt")   # -> "hello"
"hello.txt".strip_suffix(".md")    # suffix doesn't match, returns original "hello.txt" (no error)
```

---

### `list` module

#### `map`/`filter` dual parameter callback, parameter order is `(index, item)`

```bash
list.map(data, (i, x) -> i)        # i is index, x is element
list.filter(data, (i, x) -> i > 0) # same, index then element
```

Single parameter form `x -> ...` still only passes element, based on `map`/`filter` internally passing callback parameters by position `[index, item]`.

#### `fold`/`foldr` callback parameter order is `(acc, item)`

```bash
list.fold([1,2,3], (acc, x) -> acc + x, 0)   # x is element, acc is accumulator
```

When `init` parameter is omitted, defaults to `0`, using in non-numeric scenarios (like concatenating lists) can easily cause errors, must explicitly pass initial value.

#### `sort` three writing styles are mutually exclusive, dispatches based on parameter type/count

`sort` supports three second parameter forms: `fn(a,b)->int|bool`, single field name string, multiple field names (list or multiple parameters), dispatches by type. `key_fn` returns `Integer` (comparison result -1/0/1) or `Boolean` (`true` means a>b), both return types are accepted. Among them, `+`/`-` can be used as field prefix for ascending/descending. `sort` in `list`/`table`/`string` have unified internal implementation, but in string elements there's no field name, in list elements can use column index.

```bash
string.sort(data)                   # 1 param: natural sort
list.sort(data, (a,b) -> a - b)     # 2 param: comparison function, returns negative/0/positive int or bool
table.sort(data, "name")            # 2 param: single field name, sort Map list by that field
list.sort(data, "-name")            # 2 param: single field name, sort Map list by that field descending
list.sort(data, ["a","b"])          # 2 param: field name list, multi-level sort
list.sort(data, "a", "b")           # 3+ param: equivalent to multiple field name list
```

Comparison function returning `Integer` or `Boolean` both work, but semantics differ: integer follows traditional `<0 / 0 / >0`, boolean is `true` = greater.

#### `group`: key can be function or field name

key can be `fn(item)->key` or field name string, result uniformly converted to string as grouping key.

```bash
list.group(rows, "dept")             # group by field name, requires each row to be Map/HMap, otherwise errors
list.group(items, x -> x % 2)        # group by function, applicable to any type element
```

#### Other list function explanations

- **`chunks`**: last group not filling `size` is also preserved (not discarded).
- **`zip`**: result length takes the shorter of two lists, extra parts discarded, no error, no fill with `none`.
- **`remove`/`remove_at`**: when match value or index out of bounds, **silently returns original list**, won't error, when writing scripts checking "if deletion succeeded" cannot rely on whether it errors.

#### `list.find` / `bset.find` / `map.find` return **the element itself**, `list.position` / `table.position`/`rposition` return **the index**

`find` series (bset/map modules) is "given condition, get the matched data row"; `table.position`/`rposition` is "given condition, get the matched row number". `list.find` vs `list.find_last`: both can pass value or function, and can specify starting index.

```bash
list.find(s, x -> x > 10)                # returns first element satisfying condition
list.position(s, x -> x > 10)            # returns first index satisfying condition
table.position(t, row -> row.age > 18)   # returns first row index (integer) satisfying condition
table.rposition(t, row -> row.age > 18)  # returns last row index satisfying condition
```

After getting index, can use `list.get` / `table.get`/`get_cell` to get specific row/cell.

#### `list.remove` vs `list.remove_at`: remove by value vs remove by index

```bash
list.remove([1,2,3,2], 2)         # remove first element with value 2 -> [1,3,2]
list.remove([1,2,3,2], 2, true)   # third parameter true = remove all matching values -> [1,3]
list.remove_at([1,2,3,4], 1)      # remove by index, remove position 1 (0-based) -> [1,3,4]
list.remove_at([1,2,3,4], 1, 2)   # remove 2 consecutive from index 1 -> [1,4]
```

`remove` returns original list silently when match value not found, no error; `remove_at` returns original list silently when index out of bounds.

---

### `bset` module

- **`first`/`last` throw error on empty collection, instead of returning `none`**, differs from many list functions "out of bounds returns none/empty" convention:

```bash
bset.first(s)          # empty collection throws error and terminates script immediately
bset.first(s) ?.       # ignore error
if bset.is_empty(s) { ... } else { bset.first(s) }
```

- **`add`/`remove` return brand new collection, don't modify original collection** (after `clone()` then `insert`/`remove`), if forgetting to receive return value, original variable won't change.
- **`BTreeSet` is naturally ordered**, so `first`/`last` get "minimum value"/"maximum value" not "first/last insertion order item", `to_list` converted order is also sorted, not insertion order.

---

### `map`/`hmap` module

**`merge` is deep merge, `union` is shallow merge**—easily confused but semantically completely different:

```bash
map.union({a:1, b:{x:1}}, {b:{y:2}, c:3})   # {a:1, b:{y:2}, c:3}  -- b is directly completely replaced
map.merge({a:1, b:{x:1}}, {b:{y:2}, c:3})   # {a:1, b:{x:1,y:2}, c:3} -- b recursively merged
```

`union`/`intersect`/`difference` only do top-level key comparison, nested `Map` values won't be recursive; `merge` specially handles values that are also `Map`/`HMap` fields, other types (including conflict types) directly use latter to override former. `merge` supports passing 2+ maps, sequentially merge into previous result; if a parameter is not map type will be silently treated as empty map, no error.

**`from_list` only accepts `[[k,v],[k,v],...]` strict tuple list**, elements not `List` of length 2 will error:

```bash
map.from_list([["a",1],["b",2]])   # {a:1, b:2}
map.from_list([["a",1,"x"]])       # error
```

---

### `table` module

#### `where` / `table.filter`: can directly use column names and `NR`/`NF` in condition

`where`'s second parameter is **lazy expression**, when executing it will expand current row into local variables then evaluate: row expands into variables with same name, and injects `NR` (row number) uniformly.

```bash
fs.ls -lh | where(size > 5K)      # size is column name, use directly as variable
fs.ls -l | where(NR > 0)          # skip first row of data (header row not affected)
```

**Don't** write `where` condition as string or function, it's directly inline expression, variable names come from data itself. If want to use function, can use `table.filter` function, both of following ways get same result:

```bash
fs.ls -l | table.filter( x -> x.type == 'file')
fs.ls -l | where(type == 'file')
```

#### `select`/`get_column` (table module) vs top-level `select`: only works on `Table` rows

`select` selects multiple columns, returns table; `get_column` selects single column, returns list.

```bash
fs.ls -l | select(name, size)        # returns table
fs.ls -l | .get_column(name)          # returns list
fs.ls -l | .get_column(0)
```

#### `table.grep` is pure text search, `table.filter` is conditional expression filtering

`grep` doesn't need to write `fn`, directly pass string for substring matching in entire line text; `filter` needs `fn(row_map)->bool`.

```bash
t | .grep("error")                         # lines containing "error" in any column
t | .filter(row -> row.status == "error")  # exactly judging a specific column
```

Both easily confused as synonyms, but `grep` can't do numeric comparison, `filter` can't do fuzzy substring search.

#### `table.get_cell` / `table.get_column` / `table.select`: cell, column, multiple columns

```bash
t.get_cell(row_idx, col)     # single value
t.get_column(col)            # entire column (list)
t.select(col1, col2)         # multiple columns, still table
```

Their return types in order: "value → list → table", choose by needed granularity, don't use `select` for single column then use as value.

#### `table.from_maps`: header order depends on first appearance order, not alphabetical

```bash
table.from_maps([{a:1,b:2}, {c:3,a:9}])
# header order: a, b, c (merged by order of first appearance in each map, missing fields auto-filled None)
```

If a few records have inconsistent fields, result table will automatically fill missing columns with `none`, won't error.

---

### `fs` module (`ls`)

`fs.ls` parameters are **position-independent short option string + a path**, doesn't support long options, options and path can be arbitrarily ordered, the last non `-` parameter is treated as path:

```bash
fs.ls -l -a /tmp        # equivalent to
fs.ls -la /tmp
fs.ls /tmp -l -a        # works, but not recommended, especially when path is `/`,会被误认为是除法
```

`-l` (default: detailed info) additionally outputs `type`/`size`/`modified` fields, `-t` controls whether `modified` is timestamp or `DateTime`, `-u`/`-m` respectively add `user`/`mode` fields independently (not dependent on `-l`), `-p` adds complete `path` field—these flags add fields to result map independently, not mutually exclusive: "first choose category then choose fields" understanding is wrong, each flag only responsible for adding one field to result map.

`-?` prints help information.

---

### `from` module

**`jq`'s `select(...)` supports numeric comparison** (like `.field>number`), supports logical combinations (`and`/`or`), string comparison or nested `select`:

```bash
from.jq('{"list":[{"n":1},{"n":5}]}', '.list|select(.n>2)')    # OK
from.jq(data, '.list|select(.n>2 and .n<10)')                  # logical combination
```

`jq` parameter order is `<json_data> <query_string>`, and `input` must be string (cannot pass already parsed Map/List), internally re-parses via `parse::<JsonValue>()`.

- **`from.json`/`from.csv` silently return `None` for empty strings, don't error**, but errors when format is incorrect, both easy to confuse ("no data" and "data wrong" handled differently).
- **`from.csv` follows current `IFS` environment variable as delimiter** (default comma), if previously used `set IFS ';'` to modify global delimiter, `csv` parsing result will also change,容易造成"same code runs differently in different environments" confusion.
- **`from.script` only parses into expressions (AST), doesn't auto-execute.**

---

### `reg` (regex) module

#### `is_match` judges whether the entire regex can match in text (non-anchored full string equality); to judge "entire string exactly equals", need to add `^`/`$` to pattern yourself.

```bash
reg.is_match("2025-01-01", g'\d{4}-\d{2}-\d{2}')     # only judge if overall matches -> true/false
```

#### `reg.find` / `reg.find_all`: single match vs all matches

```bash
regex.find(p, text)       # only returns first match: {start,end,found}
regex.find_all(p, text)   # returns list of all matches: [{start,end,found}, ...]
```

#### `reg.capture` / `reg.captures` / `reg.named_captures`: three ways to get groups

```bash
regex.capture(p, text)          # list of groups from first match [full, g1, g2, ...]
regex.captures(p, text)         # list of groups from all matches [[full,g1,...], ...]
regex.named_captures(p, text)   # named groups (?<name>...), returns map
```

Whether to use positional groups or named groups depends on whether you wrote `(?<name>...)` in regex, three aren't mutually replaceable. `named_captures` pattern must use `(?<name>...)` named capture group syntax, only named capture groups appear in result Map, regular `(...)` capture groups are ignored:

```bash
regex.named_captures(r'(?<y>\d{4})-(?<m>\d{2})', '2025-07')
# => {y: "2025", m: "07"}
```

**First parameter can be `Regex` or `String`**, `find`/`is_match`/`capture`/`split`/`replace` all automatically recognize text and regex order (who is `Regex` type who is pattern), not forced to fixed parameter position.

---

### `math` module

**`gt`/`ge`/`lt`/`le`/`eq`/`ne` are completely equivalent to operators `>`/`<`/`==`, just function forms, convenient to pass as parameter in pipes**:

```bash
x | math.gt(0)    # equivalent to x > 0, but can directly pass function reference
```

Integer and float mixing comparison won't throw type error.

**`bit_shl`/`bit_shr` shift amount must be `0..=63`, out of range directly errors**, unlike some languages where out-of-range shift is undefined behavior or auto-modulo:

```bash
math.bit_shl(1, 64)   # Error: shift amount out of range (0-63)
```

**`min`/`max`/`sum`/`average` becomes `Float` if any parameter is `Float`**.

Internal uses "accumulate integers first, switch to float when encounter float" optimization logic, pure integer input will get integer result:

```bash
math.sum(1, 2, 3)        # 6 (Integer)
math.sum(1, 2, 3.0)      # 6.0 (Float)
math.min(1, 2)           # 1
```

---

### `rand` random module

#### `rand.choose` / `rand.sample`: pick one vs pick multiple without replacement

```bash
rand.choose(list)       # randomly pick 1 (possibly pick same result with multiple calls)
rand.sample(list, 3)    # pick 3 without replacement at once, results are unique
```

Need "lottery without replacement" scenario use `sample`, need "pick one casually" use `choose`.

---

### `sys` module

**`has` only checks current scope, `defined` checks upward through scope chain to root**—easily confused as synonyms:

```bash
let x = 1
fn f() { sys.has("x") }      # false, x doesn't exist in f's local scope
fn f() { sys.defined("x") }  # true, found x by searching upward through outer scope
```

**`max_syntax`/`max_runtime`/`max_usemode` are same function as getter/setter**: no parameters returns current value, passing an integer sets new value and returns `None`:

```bash
sys.max_runtime()      # check current runtime recursion limit
sys.max_runtime(2000)  # increase it, can call when script recursion is deep to avoid "max recursion" error
```

**`set_cfm`/`set_strict`/`set_pdm` will print ON/OFF hints, and take effect immediately globally** (they modify thread-level state, not current scope variable), frequently switching in scripts should note these are **process-level** switches not local switches.

---

### `console` module

- **`raw_mode`/`alt_screen` are manual switches, won't auto-restore**: after entering raw mode or alternate screen, if script errors and exits mid-way, terminal will be stuck in that mode (unlike `ui.pick` internally cleans up when session ends), make sure to ensure paired calls or use `?:` as fallback.
- **`cursor_up`/`down`/`left`/`right` parameter meaning is "movement distance" not "target coordinate"**, different from absolute positioning semantics of `cursor_to(x,y)`, don't mix them.
- **`keys()` returns escape sequence list, can directly compare with strings read by `read_key()`/`read_line()`**, to judge which special key user pressed:

```bash
let k = console.read_key()
let arrows = console.keys()
```

`read_key` internally temporarily switches raw mode (no need to manually call `raw_mode`), reads single key once then auto-restores; `read_line`/`read_password` go through standard input line reading, doesn't need raw mode.

---

### `ui` module

**`pick`/`multi_pick` parameter count determines cfg position**: 1 param is pure options, 2nd param is msg string or cfg map, 3+ params means last is fixed as cfg, others all combined into options list:

```bash
ui.pick(["a","b","c"])                       # 1 param: pure options
ui.pick(["a","b","c"], "choose:")            # 2 param: second is msg
0...15 | ui.pick({msg:"choose:", page_size:5})  # 2 param: second is cfg map
ui.pick("a","b","c", {msg:"choose:"})        # 3+ param: last fixed is cfg, previous all options
```

In cfg Map you can pass `page_size`/`starting_cursor` and other fields, field names are `inquire` library config items:

| Field | Type |
|---|---|
| `page_size` | Integer |
| `starting_cursor` | Integer |
| `vim_mode` | Boolean |
| `reset_cursor` | Boolean |
| `filter_input_enabled` | Boolean |
| `help_message` | String |
| `starting_filter_input` | String |
| `formatter` | `fn(index,value) -> String` |
| `sorter` | `fn(integer,integer) -> Integer` (positive for Greater, negative for Lesser) |

**Only `multi_pick` available:**

| Field | Type |
|---|---|
| `all_selected_by_default` | Boolean |
| `keep_filter` | Boolean |
| `validator` | `fn(selected_items) -> Boolean/String` (string means invalid hint) |

**When string as option, it will split by `IFS` separator**, not as entire string as one option; if `IFS_PCK` enabled and `IFS` variable set, split using that value, otherwise default split by newline `\n`:

```bash
ui.pick("a\nb\nc")     # equivalent to ui.pick(["a","b","c"])
```

**`widget`/`joinx`/`joiny`/`join_flow` width/height are auto-calculated**: when not passing `width`/`height`, will calculate based on content and title length, only need explicit parameters when fixed size is required.
