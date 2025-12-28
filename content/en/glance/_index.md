---
title: Take a glance
date: 2025-06-11 19:16:45
highlight: true
tags:
 - glance
categories:
 - wiki
 - why
---

__Lumesh is a lighting shell__

- write like `js`
- works like `bash`
- runs like **light**
- stays like **air**
- flows like **water**

![logo](/images/lumesh_ban.svg)

## Why lumesh

| compare |    lume       |     bash      |     dash      |     fish      |
|---------|---------------|---------------|---------------|---------------|
| speed(million circle)    |     *****     |     ***       |     ****      |    *          |
| interactive    |     ****      |     **        |     *         |    *****      |
| sytax    |     *****     |     **        |     *         |    ****       |
| size    |     ****      |     ***       |     *****     |    **         |
| error tips|     *****     |     *         |     *         |    ***        |
| error catch|     *****     |     *         |     *         |    *        |
| builtin Lib  |     *****     |               |               |    *       |
| key-bindings |     ☑     |               |               |       ☑       |
| structured pipe|     ☑     |               |               |              |
| AI helper  |     ☑        |               |               |               |


## Overview

### interactive
- auto complete
> + complete with executable cmds
> + complete with path
> + complete with history

| ![complete](/images/complete-cmd.png) | ![complete](/images/complete-path.png) |
|------------------------|------------------------|

 + *with AI*

|          AI complete          |         error hint           |
|------------------------|------------------------|
| ![complete](/images/ai.png) | ![complete](/images/errhint1.png) |


- error hint
detailed error error hint. as shown above.

### highlight

| ![highlight](/images/highlight0.png) | ![highlight](/images/highlight1.png) |
|------------------------|------------------------|

### key-bindings

custom key-bindings supported，eg:

| `alt + m` save bookmark |  `alt + x` fuzzy search and execut |
|------------------------|------------------------|
| ![highlight](/images/hotkey1.png) | ![highlight](/images/hotkey2.png) |

### performance

* memery usage

|          lume          |         fish           |
|------------------------|------------------------|
| ![mem_lume](/images/mem_lume.png) | ![mem_fish](/images/mem_fish.png) |
|          bash          |         dash           |
| ![mem_bash](/images/mem_bash.png) | ![mem_dash](/images/mem_dash.png) |


* loop test

|          lume          |         fish           |
|------------------------|------------------------|
| ![time_lume](/images/time_lume.png) | ![time_fish](/images/time_fish.png) |
|          bash          |         dash           |
| ![time_bash](/images/time_bash.png) | ![time_dash](/images/time_dash.png) |

_fish was unable to finish 1,000,000 times circle._


 * pakage size（after install）

|         |    lume       |     bash      |     dash      |     fish      |
|---------|---------------|---------------|---------------|---------------|
| version |    v0.3.8     |    v5.2.037   |    v0.5.12    |   v4.0.2      |
| size    |    4.3 MB     |    9.2 MB     |    153.8 KiB  |   21.64 MB    |


* test result


| ![highlight](/images/mem_chart.png) | ![highlight](/images/time_chart.png) |
|------------------------|------------------------|

_as fish was unable to fishish one million times task, we take the time of its harf task_

### syntax

the snytax is user-friendly.

- direct math
```bash
 6 / 3
 5 - 1
 1+2^3*2
```

- work with vars
```bash
let a = (2+3)*5
print "a=" a

let b,c = 1,2     # multi assign supported
println b c
println(b,c)      # also works
```

- work with array list
```bash
let arr = [10, "a", True]
let arr_b = 0..10

# index
arr[0]       # → 10
arr_b.1

# slice
arr[1:3]     # → ["a", True]（left close right open）
arr[::2]     # → [10, True]（step is 2）
arr[-1:]      # → True（support negative slice）

# nested
[1,24,5,[5,6,8]][3][1]     # 6
```

- work with map
```bash
let dict = {name: "Alice", age: 30}

# index
dict[name]
dict.name
```

- if statement
```bash
if a>0 && b==0 {
   print OK
}else{
   print BAD
}
```

- match statement
```bash
match a {
 10 => print "ten",
 20 => print "twenty"     # comma is optional
 _ => print other
}
```

- other statement
also *For, While, Loop* supported.
conditional assign: *a?b:c*
lazy assign: `let a := b + c`

- lambda expression
```bash
let addone = x -> x+1
let add = (x,y) -> x+y

addone(2)
add(3,4)
```

- function

  > support function as arg
  > support function nest
  > support default value
  > suppport params collector


```bash
fn add(x,y=0,*other){
 x+y+len(other)
}

add(3)
add(3,4)
add(3,4,5,6,7)
```

- error catch

```bash
let e = x -> print x
3 / 0 ?: e               # you could use a function to catch and deel with errors

3 / 0 ?.                 # aslo, you may like to ignore it.
3 / 0 ??                 # aslo, you may like to display it on stderr.
3 / 0 ?+                 # aslo, you may like to merge it to stdout.
3 / 0 ?!                 # aslo, you may like to use error as result, useful to pipe errors out.

3 / 0 ?: 0               # give a default value while failing
```
error catch could be use in expression and whole function declare.

- smart pipes
**Smart pipelines** that can automatically adapt to input and output formats, intelligently supporting byte pipelines and **structured data pipelines**.

> structured pipes :

```bash

ls -l | From.cmd() | where( int C4 > 4000 )            # use normal ls command

let thead = [mode,i,user,group,size,mday,mtime,name]
ls -l | From.cmd(thead)  | where(int size > 400) | select([name,size,mtime])      #parse with table head

Fs.ls -l | where(mode==420) | select(name)              # use builtin ls
```
see handbook for more details.

## Related
 - [Features](features)
 - [syntax overview](syntax)
 - [bash compare](./bash)

## Handbook

 - [syntax handbook](/doc/syntax)
 - [keyboard shortcut](/doc/keys)
 - [builtin libs](/doc/libs/)

## Tests

 - [syntax-test](https://codeberg.org/santo/lumesh/raw/branch/main/src/tests/op-test.lm)
 - [benchmark-test](https://codeberg.org/santo/lumesh/raw/branch/main/src/tests/benchmark.lm)
 - [benchmark-bash-test](https://codeberg.org/santo/lumesh/raw/branch/main/src/tests/benchmark.sh)
