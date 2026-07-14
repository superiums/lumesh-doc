---
title: 语法：区间、列表、集合与映射
date: 2025-06-11 19:16:45
highlight: true
weight: 40
tags:
 - syntax
categories:
 - wiki
 - syntax
---

> 基础语法：区间、列表、集合与映射

## 三、区间、列表、集合与映射

### 1. 区间


- 区间表达式
    区间使用 `..`（左闭右开） 或 `..=`（为闭区间）,左右不能有空格。
    支持变量扩展。


    ```bash
    0..10        #不包含10
    0..=10       #包含10
    a..=b
    ```

    区间表达式支持步进：`:step`
    ```bash
    0..=6:2     # 表示步进为2: [0,2,4,6]
    ```

    可用于循环、包含检测、数组创建等。

    ```bash
    let r = 0..=8

    for i in r {...}       # 比直接在数组上循环效率更高
    r ~: 5                 # 检测是否包含元素
    list.from(r)           # 转为数组
    ```

### 2. 列表（数组）/集合
- 列表用`[ ]`表示。内部元素是有序的。
- 集合用`S{}`表示，集合内部自动排序，无法从外部干预排序。
-= 也可以用`...`或`...=`直接从区间创建

    ```bash
    0...5               # 输出[0,1,2,3,4]
    # 等同于
    list.from(0..5)
    ```

*两个点`..` `..=`创建区间，三个点`...` `...=`创建数组*

- 索引用`.` 或 `[i]`表示。
    ```bash
    let arr = [10, "a", True]
    ```

- 索引和切片
    ```bash
    # 基础索引

    arr.1
    arr[0]       # → 10

    # 切片操作
    arr[1..3]     # → ["a", True]（左闭右开）
    arr[_.._:2]     # → [10, True]（步长2）
    arr[-1.._]      # → True（切片支持负数索引）
    ```

**占位符`_`在切片中表示不关闭区间**

- 复杂嵌套
    ```bash
    # 复杂嵌套
    [1,24,5,[5,6,8]][3][1]     # 显示6
    # # 修改元素
    # arr[2] = 3.14 # → [10, "a", 3.14]
    ```
- 高级操作
参考 [list](/zh-cn/doc/libs/list) 模块。

**边缘情况**：
- 数组索引如果超出边界，会触发`out of bounds`错误
- 数组切片支持负数，表示从后往前数的index
- 如果对不能索引的对象进行索引，会触发如下错误：
`[ERROR] type error: expected indexable type (list/dict/string), found symbol`

### 3. 映射（字典）
- 映射用`{}`或`M{}表示BtreeMap, 用`H{}`表示HashMap

    ```bash
    let mydict = {name: "Alice", age: 30}

    # 允许简写：
    let a = b =3,
    H{a, b}
    M{
        a,
        b,
    }          # 允许末尾逗号，允许多行书写
    {a, }             # 单个键值的逗号不能省略
    ```

- 字典索引
    ```bash
    # 基础访问

    mydict["name"]     # → "Alice"
    mydict.name        # → "Alice"（简写形式）
    mydict@name        # → "Alice"（简写形式）

    # 动态键支持
    let key = "ag" + "e"
    dict[key]       # → 30

    # 嵌套访问
    let data = {user: mydict}
    data.user.age # → 100
    ```
- 高级操作
参考 [map](/zh-cn/doc/libs/map) 模块。

**边缘情况**：
| 场景                          | 行为                           |
|------------------------------|--------------------------------|
| 访问不存在的数组索引          | 触发`[ERROR] key `x` not found in map`错误            |
| 对非字典对象进行索引          | 触发 `[ERROR] not valid index option` 错误  |
| 对未定义的符号进行索引        | 返回字符串，因为shell中操作文件名是最常见的操作 |
