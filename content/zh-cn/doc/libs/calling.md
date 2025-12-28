---
title: 函数调用方式
date: 2025-12-25 19:16:45
weight: 2
---

### 顶层函数

所有内置函数均支持三种调用语法：
> `func(arg1, arg2)` 或 `func arg1 arg2` 或 `func! arg1 arg2`

当参数包含复杂结构时（Lambda, 逻辑运算等）建议使用第一种形式

### 模块函数
可通过以下三种方式调用：

**模块名调用**

通过`模块名.函数名`调用
> `String.red(args)` 或 `String.red args`

**链式调用**

> `'a c b'.split().sort()`

**管道方法调用**

> `'a c b' | .split() | .sort()`
