---
title: 惊鸿一瞥
date: 2025-06-11 19:16:45
highlight: true
tags:
 - glance
categories:
 - wiki
 - why
---

__a lighting shell__

- 写起来像 `js`
- 用起来像 `bash`
- 跑起来像 **光**
- 放那里像 **气**
- 动起来像 **水**

![logo](images/lumesh_ban.svg)

## 为什么选择Lumesh


| 对比项目|    lume       |     bash      |     dash      |     fish      |
|---------|---------------|---------------|---------------|---------------|
| 速度(百万循环)    |     *****     |     ***       |     ****      |    *          |
| 交互    |     ****      |     **        |     *         |    *****      |
| 语法    |     *****     |     **        |     *         |    ****       |
| 体积    |     ****      |     ***       |     *****     |    **         |
| 错误提示|     *****     |     *         |     *         |    ***        |
| 错误处理|     *****     |     *         |     *         |    *        |
| 内置库  |     *****     |               |               |    *         |
| 按键绑定|     ☑      |               |               |      ☑        |
| 结构化管道|     ☑      |               |               |              |
| AI交互  |     ☑        |               |               |               |


## 快速概览

### 交互
- 自动补全
   * 可执行命令补全
   * 路径补全
   * 历史命令补全

| ![complete](images/ai.png) | ![complete](images/complete.png) |
|------------------------|------------------------|


- 细致的错误提示

|          语法错误提示          |         运行错误提示           |
|------------------------|------------------------|
| ![complete](images/errhint1.png) | ![complete](images/errhint2.png) |



### 语法高亮

| ![highlight](images/highlight0.png) | ![highlight](images/highlight1.png) |
|------------------------|------------------------|

### 按键绑定

用户可自定义快捷键绑定命令，例如：

| `alt + m` 保存命令书签 |  `alt + x` 用fzf搜索并执行书签  |
|------------------------|------------------------|
| ![highlight](images/hotkey1.png) | ![highlight](images/hotkey2.png) |

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
| 版本    |    v0.3.8     |    v5.2.037   |    v0.5.12    |   v4.0.2      |
| 体积    |    4.3 MB     |    9.2 MB     |    153.8 KiB  |   21.64 MB    |


* 测试结果

| ![highlight](images/mem_chart.png) | ![highlight](images/time_chart.png) |
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
let arr = [10, "a", True]
let arr_b = 0..10

arr[0]       # → 10
arr_b.1

# 切片操作
arr[1:3]     # → ["a", True]（左闭右开）
arr[::2]     # → [10, True]（步长2）
arr[-1:]      # → True（切片支持负数索引）

# 复杂嵌套
[1,24,5,[5,6,8]][3][1]     # 显示6
```

- 映射/字典
```bash
let dict = {name: "Alice", age: 30}

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
 20 => print "twenty"     # 逗号是可选的
 _ => print other
}
```

- 其他语句
同时支持 *For, While, Loop* 等循环语句.
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

3 / 0 ?. # 你也可以选择忽略它。
3 / 0 ?? # 你也可以选择在 错误输出 上显示它。
3 / 0 ?+ # 你也可以选择将其合并到 标准输出。
3 / 0 ?! # 你也可以选择将错误作为结果使用，这对错误重定向很有用
3 / 0 ?: 0 # 在失败时给出默认值

```
错误捕获可以在表达式和整个函数声明中使用。

- 智能管道

**智能管道**可以自动适应输入和输出格式，智能支持字节管道和 *结构化数据管道*。

> 结构化管道示例 :

```bash

ls -l | From.cmd() | where( int C4 > 4000 )            # 使用系统ls命令

let thead = [mode,i,user,group,size,mday,mtime,name]
ls -l | From.cmd(thead)  | where(int size > 400) | select([name,size,mtime])   # 自定义转换表头

Fs.ls -l | where(mode==420) | select(name)              # 使用内置ls命令
```

更多详细信息，请参见手册。

## 手册

 - [语法手册](/zh-cn/doc/syntax)
 - [内置库函数](/zh-cn/doc/libs/)
 - [快捷键列表](/zh-cn/doc/keys)
 - [Bash语法对比](/zh-cn/glance/bash)

## 测试脚本

 - [syntax-test](https://codeberg.org/santo/lumesh/raw/branch/main/src/tests/op-test.lm)
 - [benchmark-test](https://codeberg.org/santo/lumesh/raw/branch/main/src/tests/benchmark.lm)
 - [benchmark-bash-test](https://codeberg.org/santo/lumesh/raw/branch/main/src/tests/benchmark.sh)
