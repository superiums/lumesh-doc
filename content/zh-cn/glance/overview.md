---
title: 快速概览
date: 2026-07-11 19:16:45
highlight: true
weight: 1
tags:
 - glance
categories:
 - wiki
 - why
---


### 交互

[demo](images/demo.gif)


### 语法高亮

![highlight](images/highlight.gif)

- 自动补全
   * 命令补全/提示
   * 参数补全/提示
   * 路径补全
   * 历史命令补全/提示
   * 内置库补全/提示，参数提示
   * ai补全提示(alt+i)

| ![complete](images/completion.gif) | ![complete](images/ai.gif) |
|------------------------|------------------------|


- 细致的错误提示

|          语法错误提示          |         运行错误提示           |
|------------------------|------------------------|
| ![err](images/err.gif) | ![err runtime](images/err_runtime.gif) |


### 按键绑定

#### 快捷键绑定,可自定义函数修改输入行
```bash
set LUME_HOT_BINDINGS = {
    CTRL_q: 'exit',
    ALT_m: save_cmdmark,
    CTRL_SHIFT_M: select_cmdmark,
    CTRL_SHIFT_D: select_dirmark,
    ALT_e: fix_typos,
    CTRL_SHIFT_t: timestamp,
    'CTRL_/': menu,
}
```

#### 斜杠命令
1. 内置/命令：
- `/` 命令菜单
- `/ <query>` 快速模糊目录跳转，类似zoxide的z命令
- `/h [prefix]` 历史命令模糊搜索
![slash cmd](images/bindings.gif)

2. 支持自定义/xxx命令
```bash
let open_file = (b) -> {fd -t file | ui.pick('select file:') ?! | handlr open -- _}

set LUME_SLASH_BINDINGS = {
    sm: save_cmdmark,
    sd: save_dirmark,
    m: select_cmdmark,
    d: select_dirmark,
    g: fuzzy_go,
    o: open_file,
    e: edit_file,
    sc: search_content,
    cm: git_commit,
}
```


### 性能

 * 内存使用

|          lume          |         fish           |
|------------------------|------------------------|
| ![mem_lume](images/mem_lume.png) | ![mem_fish](images/mem_fish.png) |
|          bash          |         dash           |
| ![mem_bash](images/mem_bash.png) | ![mem_dash](images/mem_dash.png) |


 * 循环测试

|          lume          |         fish           |
|------------------------|------------------------|
| ![time_lume](images/time_lume.png) | ![time_fish](images/time_fish.png) |
|          bash          |         dash           |
| ![time_bash](images/time_bash.png) | ![time_dash](images/time_dash.png) |

_其中fish无法成功完成百万次循环_


 * 软件包大小（安装后）

|         |    lume       |     bash      |     dash      |     fish      |
|---------|---------------|---------------|---------------|---------------|
| 版本    |    v0.16.10     |    v5.2.037   |    v0.5.12    |   v4.0.2      |
| 体积    |    3.97 MB     |    9.2 MB     |    153.8 KiB  |   21.64 MB    |


* 测试结果

| ![highlight](images/mem_chart.svg) | ![highlight](images/time_chart.svg) |
|------------------------|------------------------|

_由于fish无法完成百万次任务，我们取值它完成一半任务的时间_

### 语法

语法友好易用。

- 直接数学运算

```bash
 6 / 3
 5 - 1
 1+2^3*2
```

- 变量
```bash
let a = (2+3)*5
print "a=" a

let b,c = 1,2     # 支持多变量赋值
println b c
println(b,c)      # 也可以
```

- 数组/列表
```bash
let arr = [10, "a", true]
let arr_b = 0...10

arr[0]       # → 10
arr_b[1]

# 切片操作
arr[1:3]     # → ["a", true]（左闭右开）
arr[::2]     # → [10, true]（步长2）
arr[-1:]      # → true（切片支持负数索引）

# 复杂嵌套
[1,24,5,[5,6,8]][3][1]     # 显示6
```

- 映射/字典
```bash
let dict = {name: "Lume", age: 30}

# 基础访问
dict.name
dict[name]
```

- if条件语句
```bash
if a>0 && b==0 {
   print OK
}else{
   print BAD
}
```

- match匹配语句
```bash
match a {
 10 => print "ten",
 20 => print "twenty"
 _ => print other
}
```

- 其他语句
同时支持 *for, while, loop* 等循环语句.
条件赋值语句 *a?b:c*
延迟赋值语句 `let a := b + c`

- lambda表达式
```bash
let addone = x -> x+1
let add = (x,y) -> x+y

addone(2)
add(3,4)
```

- function函数

  > 支持高阶函数
  > 支持函数嵌套
  > 支持默认参数
  > 支持剩余参数收集

```bash
fn add(x,y=0,*other){     # =提供默认参数，*收集剩余参数
 x+y+len(other)
}

add(3)
add(3,4)
add(3,4,5,6,7)
```

- error catch

```bash
let e = x -> print x

3 / 0 ?: e # 你可以使用函数来捕获和处理错误
3 / 0 ?: 0 # 在失败时给出默认值

3 / 0 ?. # 你也可以选择忽略它。
3 / 0 ?? # 你也可以选择在 错误输出 上显示它。
3 / 0 ?+ # 你也可以选择将其合并到 标准输出。
3 / 0 ?! # 你也可以选择将错误作为结果使用，这对错误重定向很有用

```
错误捕获可以在表达式和整个函数声明中使用。

- 智能管道

**智能管道**可以自动适应输入和输出格式，智能支持字节管道和 *结构化数据管道*。

> 结构化管道示例 :

```bash

ls -l | into.table() | where( int C4 > 4000 )            # 使用系统ls命令

let thead = [mode,i,user,group,size,mday,mtime,name]
ls -l | into.table(thead)  | where(int size > 400) | select([name,size,mtime])   # 自定义转换表头

fs.ls -lh | where(size > 3M) | select(name)              # 使用内置ls命令
```

更多详细信息，请参见手册。

## 手册

 - [语法手册](/zh-cn/doc/syntax)
 - [内置库函数](/zh-cn/doc/libs/)
 - [按键绑定](/zh-cn/doc/keys)
 - [Bash语法对比](/zh-cn/glance/bash)

## 测试脚本

 - [syntax-test](https://codeberg.org/santo/lumesh/raw/branch/main/src/tests/op-test.lm)
 - [benchmark-test](https://codeberg.org/santo/lumesh/raw/branch/main/src/tests/benchmark.lm)
 - [benchmark-bash-test](https://codeberg.org/santo/lumesh/raw/branch/main/src/tests/benchmark.sh)
