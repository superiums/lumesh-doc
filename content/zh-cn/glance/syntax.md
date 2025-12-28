---
title: Lumesh 语法概览
date: 2025-07-05 19:16:45
highlight: true
tags:
 - glance
categories:
 - wiki
 - why
 - syntax
---

Lumesh 是一个现代化的 shell 和脚本语言，采用 Pratt 解析器实现复杂的表达式解析。

## 基础语法结构

### 运算符优先级系统

Lumesh 使用精确定义的运算符优先级，从低到高排列：

1. **赋值运算符** (优先级 1): `=`, `:=`, `+=`, `-=`, `*=`, `/=`
2. **重定向和管道** (优先级 2): `|`, `|_`, `|>`, `|^`, `>>`, `>!`
3. **错误处理** (优先级 3): `?.`, `?+`, `??`, `?>`, `?!`
4. **Lambda 表达式** (优先级 4): `->`
5. **条件运算符** (优先级 5): `?:`
6. **逻辑或** (优先级 6): `||`
7. **逻辑与** (优先级 7): `&&`
8. **比较运算** (优先级 8): `==`, `!=`, `>`, `<`, `>=`, `<=`
9. 四则运算...

## 数据类型

### 基础类型

Lumesh 支持多种基础数据类型：

```bash
# 整数
let num = 42
let negative = -100

# 浮点数
let pi = 3.14159
let percent = 85%


# 字符串
let str1 = "双引号字符串，\n支持转义"
let str2 = '单引号原始字符串，\n不转义'
let template = `模板字符串 $var ${var + 1}`

# 布尔值
let flag = True
let disabled = False

# 空值
let empty = None

# 文件大小
let a = 3000K
```

### 集合类型

```bash
# 列表 (List)
let arr = [1, 2, 3, "mixed", True]
let nested = [[1, 2], [3, 4]]

# 哈希映射 (HMap) - 无序，快速查找
# let hmap = H{name: "Alice", age: 25}

# 有序映射 (Map) - 有序，支持范围查询
let map = {a: 1, b: 2, c: 3}

# 范围 (Range)
let range1 = 1..10      # 1 到 9 (不包含 10)
let range2 = 1..=10     # 1 到 10 (包含 10)
let range3 = 1..10:2    # 1, 3, 5, 7, 9 (步长为 2)

let array = 1...10      # 从范围直接建立数组
```

## 变量和赋值

### 基本赋值

```bash
# 变量声明和赋值
let x = 10
let name = "Lumesh"

# 多变量赋值
let a, b, c = 1, 2, 3
let x, y = getValue()
```

### 延迟赋值

Lumesh 独有的延迟赋值功能：

```bash
# 使用 := 进行延迟赋值，表达式不会立即执行
let cmd := ls -l /tmp
let calculation := 2 + 3 * 4

# 需要时才执行
eval(cmd)           # 执行 ls -l /tmp
eval(calculation)   # 计算得到 14

# 如果是命令，也可以直接执行
cmd
```

### 解构赋值

支持数组和映射的解构赋值：

```bash
# 数组解构
let [first, second, *rest] = [1, 2, 3, 4, 5]
# first = 1, second = 2, rest = [3, 4, 5]

# 映射解构
let {name, age} = {name: "Bob", age: 30, city: "NYC"}
# name = "Bob", age = 30

# 重命名解构
let {name: username, age: userAge} = user_data
```

## 运算符

### 算术运算符

```bash
let a = 10 + 5      # 加法: 15
let b = 10 - 3      # 减法: 7
let c = 4 * 6       # 乘法: 24
let d = 15 / 3      # 除法: 5
let e = 17 % 5      # 取模: 2
let f = 2 ^ 3       # 幂运算: 8
```

### 比较运算符

```bash
# 基本比较
a == b      # 相等
a != b      # 不等
a > b       # 大于
a < b       # 小于
a >= b      # 大于等于
a <= b      # 小于等于

# 模式匹配
text ~= "pattern"       # 正则匹配
text ~: "substring"     # 包含匹配
text !~= "pattern"      # 正则不匹配
text !~: "substring"    # 不包含匹配
```

### 逻辑运算符

```bash
condition1 && condition2    # 逻辑与
condition1 || condition2    # 逻辑或
!condition                  # 逻辑非
```

## 管道操作

Lumesh 提供多种管道类型，这是其独特特性：

```bash
# 标准管道 - 传递标准输出 或 结构化数据
cmd1 | cmd2

# 位置管道 - 替换指定的参数位置
data |_ process_func arg1 _ arg3

# 循环管道 - 每次派发一个元素，循环执行，可指定参数位置
list |> transform(param1, param2, _)

# PTY 管道 - 用于交互式程序
cmd1 |^ interactive_program
```

## 错误处理

Lumesh 内置了强大的错误处理机制：

```bash
# 忽略错误，继续执行
risky_command ?.

# 打印错误到标准输出
command ?+

# 打印错误到标准错误输出
command ??

# 打印错误并覆盖结果
command ?>

# 遇到错误时终止程序
command ?!

# 自定义错误处理，可用于提供默认值
command ?: error_handler
```

## 控制流

### 条件语句

```bash
# if-else 表达式
let result = if condition {
    "true branch"
} else {
    "false branch"
}

# 三元运算符，支持嵌套
let value = condition ? true_value : false_value
```

### 循环结构

```bash
# while 循环
while condition {
    # 循环体
    update_condition()
}

# for 循环，可用于字符串，字符串切割遵循IFS设置；可使用*通配符
for item in collection {
    process(item)
}

# 范围循环
for i in 0..10 {
    println(i)
}

# 无限循环
loop {
    if break_condition {
        break
    }
}

# 简单重复
repeat 10 {a += 1}
```

### 模式匹配
支持正则
```bash
match value {
    1 => "one",
    2, 3 => "two or three",
    xx => "is symbol/string xx",
    '\w' => "is word",
    '\d+' => "is digit",
    _ => "default case"
}
```

## 函数

### 函数定义

```bash
# 基本函数
fn greet(name) {
    "Hello, " + name
}

# 带默认参数的函数
fn add(a, b = 0) {
    a + b
}

# 可变参数函数
fn sum(a, *numbers) {
    numbers | List.fold(0, (acc, x) -> acc + x)
}
```

### Lambda 表达式

```bash
# 单参数 lambda
let square = x -> x * x

# 多参数 lambda
let add = (a, b) -> a + b

# 复杂 lambda 体
let process = data -> {
    let filtered = data | List.filter(x -> x > 0)
    filtered | List.map(x -> x * 2)
}
```

### 函数装饰器

支持函数装饰器语法：

```bash
@timing
@cache(300)
fn expensive_calculation(input) {
    # 复杂计算
    heavy_computation(input)
}
```

## 链式调用

支持面向对象风格的链式方法调用：

```bash
# 字符串链式操作
"hello world"
    .split(' ')
    .map(s -> s.to_upper())
    .join('-')

# 数据处理链
data
    .filter(x -> x.active)
    .sort(x -> x.priority)
    .take(10)
```

## 索引和切片

```bash
# 数组索引
let arr = [1, 2, 3, 4, 5]
let first = arr[0]          # 1


# 数组切片
let slice1 = arr[1:4]       # [2, 3, 4]
let slice2 = arr[1:]        # [2, 3, 4, 5]
let slice3 = arr[:3]        # [1, 2, 3]
let slice3 = arr[-3:]       # [3, 4, 5]
let slice4 = arr[::2]       # [1, 3, 5] (步长为2)

# 映射索引
let obj = {name: "Alice", age: 25}
let name = obj[name]      # "Alice"
let ages = obj.age           # 25 (点号访问)
let age = obj@age           # 25 (@访问)
```

## 范围操作

```bash
# 基本范围
1..10           # 1 到 10
1..<10          # 1 到 9 (显式不包含)

# 带步长的范围
1..10:2         # 1, 3, 5, 7, 9
0..100:10       # 0, 10, 20, ..., 90

```

## 字符串处理

```bash
# 字符串插值
let name = "World"
let age =18
let greeting = `Hello, ${age>18 ? "Mr.":"Dear"} $name !`

# 多行字符串
let multiline = "
这是一个
多行字符串
"

# 原始字符串 (不转义)
let raw = 'C:\path\to\file'
```

## 集合操作

```bash
# 列表操作
let numbers = [1, 2, 3, 4, 5]
numbers.append(6)             # 添加元素
numbers + 6                   # 添加元素
# numbers.pop()               # 移除最后一个元素
numbers - 4                   # 移除指定元素
numbers.len()                 # 获取长度

# 映射操作
let person = {name: "Bob", age: 30}
# person.city = "NYC"         # 添加属性
person + {city: "NYC"}         # 添加属性
# del person.age              # 删除属性
person.keys()               # 获取所有键
```

## 模块系统

```bash
# 导入模块
use my_mod
use my_mod as a

# 使用模块函数
Fs.ls("/tmp")
String.split("hello world", " ")
Math.sin(Math.PI / 2)
```

## 注释

```bash
# 单行注释
let x = 10  # 行末注释
```

## 高级特性

### 自定义运算符

```bash
# 定义自定义运算符
let _+ = (a, b) -> a.concat(b)  # 自定义加法
let __! = x -> Math.sum(x)     # 自定义前缀运算符

# 使用自定义运算符
[1, 2] _+ [3, 4]    # [1, 2, 3, 4]
[5,6,7]  __!              # 18
```

<!-- ### 宏系统

```bash
# 定义宏
fn dbg(expr) {
    println(`Debug: ${expr} = ${eval(expr)}`)
}

# 使用宏
dbg(2 + 3)  # 输出: Debug: 2 + 3 = 5
``` -->



## Notes

Lumesh 的设计理念是将现代编程语言的优雅语法与 shell 的实用性相结合，提供强大的错误处理、管道操作和模块系统。通过 Pratt 解析器实现的优先级系统确保了复杂表达式的正确解析，而丰富的内置模块和配置选项使其适用于各种场景，从简单的命令行操作到复杂的系统管理脚本。

了解更多：
- [特性概览 (superiums/lumesh)](/zh-cn/overview)
- [应用举例 (superiums/lumesh)](/zh-cn/cases)
- [语法手册 (superiums/lumesh)](/zh-cn/syntax)
- [内置函数 (superiums/lumesh)](/zh-cn/libs/index)
