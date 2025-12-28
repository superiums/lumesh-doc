---
title: Lumesh 语法特性简介
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


### 独特语法特性

#### 1. 多种管道操作符
与传统 shell 不同，Lumesh 提供多种管道类型：

```bash
cmd1 | cmd2      # 标准管道，传输结构化数据、文本流
cmd1 |_ cmd2     # 位置管道，传输到指定的参数位置
cmd1 |> cmd2     # 单派管道，循环派发列表数据
cmd1 |^ cmd2     # PTY 管道
```
结构化管道：
```bash
ls -l | Into.table() | where(size > 5K)
Fs.ls -l | where(size > 5K) | select(name,size,modified)
ls -1 |> adb push _ /sdcard/Download/
```

#### 2. 错误处理操作符
Lumesh 内置了丰富的错误处理机制：

```bash
command ?.        # 忽略错误
command ?: e      # 错误捕获 或 默认值
command ?+        # 打印到标准输出
command ??        # 打印到错误输出
command ?>        # 覆盖打印 （数据通道）
command ?!        # 遇错终止  (终止管道)
```

#### 3. 延迟赋值
使用 `:=` 进行延迟赋值，表达式不会立即执行：

```bash
let cmd := ls -l    # 延迟执行
```

#### 4. 链式调用
支持类似面向对象语言的链式方法调用：

```bash
"hello world".split(' ').join(',')
data | .filter(x -> x > 0)
```

#### 5. 解构赋值
支持数组和映射的解构赋值：

```bash
let [a, b, c] = [1, 2, 3]
let {name, age} = user_data
```

#### 6. 区间操作符
提供多种区间操作符：

```bash
1..10      # 包含结束
1..<10     # 不包含结束
1..10:2    # 带步长
1...10     # 直接创建数组
```

#### 7. 函数装饰器
支持函数装饰器语法：

```bash
@decorator_name
@decorator_with_args(param1, param2)
fn my_function() { ... }
```

#### 8. 模式匹配
内置强大的模式匹配功能，支持正则表达式：

```bash
match value {
    pattern1 => result1,
    pattern2 => result2,
    _ => default
}
```

#### 9. 重载的运算符
利用常规四则运算符处理更多常见任务：
```bash
"1" + [2,3]
[1,2] + 3
[1,2] + [3,4,5]
{a:b} + c
{a:b} + {c:d}
```

#### 10. 函数式编程

```bash
0...10 | List.filter(x -> x % 2 == 0)
0..10 | .map(x -> x * 2)
```

内置大量实用的函数库, 以实现便捷的函数式编程，如
- **集合操作**: `List.reduce, List.map`
- **文件系统**: `Fs.ls, Fs.read, Fs.write`
- **字符串处理**: `String.split, String.join`、正则模块、格式化模块
- **时间操作**: `Time.now, Time.format`
- **数据转换**: Into模块, Parse模块
- **数学计算**: 完整的数学函数库
- **日志记录**: Log模块
- **UI操作**: `Ui.pick, Ui.confirm`

### 控制流结构


```bash
# 条件语句
if condition { action } else { alternative }
match expr { a => brachA; ... }
# 循环语句
for item in collection { process(item) }
while condition { body }
loop { infinite_loop }
# 简单重复
repeat 10 {a += 1}
```

### 表达式优先级
Lumesh 使用精确定义的运算符优先级系统：

- 赋值运算符 (`=`, `:=`, `+=` 等) - 优先级 1
- 重定向和管道 (`|`, `>>`, `>!`) - 优先级 2
- 错误处理 (`?.`, `?+`, `??`) - 优先级 3
- Lambda 表达式 (`->`) - 优先级 4
- ...

### 数据类型

#### 字符串
支持三种字符串类型：
- 双引号字符串：`"hello world"`
- 原始字符串：`'raw string'`
- 模板字符串：`` `template $variable` ``

#### 集合类型

```bash
[1, 2, 3]                    # 数组
{key: value, name: "test"}   # 映射
1..10                        # 区间
1...10                       # 数组
```

#### 函数
- 支持参数收集，支持默认参数
- 支持lambda函数
- 支持函数嵌套
```bash
fn add(x){ x+1 }
(x,y) -> x+y
```

### 模块系统

支持模块导入和使用：

```bash
use module_name

Fs.ls("/path")      # 使用内置模块
```

## Notes

Lumesh 的语法设计融合了现代编程语言的特性与 shell 的实用性。其独特的管道操作符、错误处理机制和链式调用语法是与传统 shell 最大的区别。

解析器采用 Pratt 算法实现，支持复杂的表达式嵌套和优先级处理。

Wiki pages you might want to explore:
- [Lume vs Bash, ppt](/rv/zh.html)
- [语法概览 (superiums/lumesh)](/zh-cn/glance)
- [特性概览 (superiums/lumesh)](/zh-cn/feature)
- [日常用举例 (superiums/lumesh)](/zh-cn/cases)
- [使用Lumesh编写lf配置文件的语法演示](/zh-cn/cases/case_lf)
- [语法手册 (superiums/lumesh)](/zh-cn/doc/syntax)
- [内置函数 (superiums/lumesh)](/zh-cn/doc/libs/)
