---
title: 语法：变量
subtitle: （基础语法）
date: 2025-06-11 19:16:45
highlight: true
weight: 20
tags:
 - syntax
categories:
 - wiki
 - syntax
---

> 基础语法：变量类型与赋值

## 一、变量与赋值

### 1. **数据类型**
####  基本类型
  | 类型       | 示例                     |
  |-----------|--------------------------|
  | 变量     | `x`, `$a`                    |
  | 整数       | `42`, `-3`               |
  | 浮点数     | `3.14`, `-0.5`, `10%`     |
  | 字符串     | `"Hello\n"`, `'raw'`      |
  | 布尔值     | `true`, `false`           |
  | 列表（数组）| `[1, "a", true]`          |
  | 集合  | `S{1, "a", true}`          |
  | 映射（BtreeMap）| `{name: "Alice", age: 30}`|
  | 映射（HashMap）| `H{name: "Alice", age: 30}`|
  | 区间       | `1..8`, `1..=10`          |
  | regex       | `r'^\w+` |
  | 时间       | `t'2025-5-20'` |
  | 文件大小   | `4K`,`5T`  |
  | 空值       | `none`                    |

#### 复杂类型
  | 类型       | 示例                     |
  |-----------|--------------------------|
  | 函数     | `fn add(x,y){return x+y}`    |
  | lambda     | `(x,y) -> x + y`  |
  | 内置库     | `math.floor`  |

  **作用域规则**
   - lambda, function函数 将创建子环境作用域。
   - 子环境继承父环境变量，不修改父作用域。

#### 字符串
  - 单引号为原始字符串。
  
  - 双引号支持转义：
    + 文本转义（如 `\n`）
    + unicode转义（如`\u{2614}`）
    + ansi转义（如`\033[34m`)
    
  - 点引号为字符串模板：
    + 支持`$var`变量替换
    + 支持`${cmd arg}`子语句执行捕获
    + ansi转义字符。


     ```bash
     print "Hello\nworld!\u{2614}"
     Hello                           #输出两行, \n被转义为换行符
     world!☔                         #unicode转义，\u{2614}是雨伞字符

     let str2 = 'Hello\nworld!'
     Hello\nworld!                   #输出一行，包括\n的原始形式

     let a = [1,2,5]
     print `a is $a, and a[0] is ${a[0]}`

     print "\033[31mRed msg\033[m"   #输出红色的Red msg
     ```

     _点引号内的变量替换比子语句捕获效率高，非必要情况建议使用前者_

  - **边缘情况**：
     > 未定义的转义序列会报错，如：

      ```bash
      echo "Hello\_"

      [PARSE FAILED] syntax error: invalid string escape sequence `\_`
            |
          1 | echo "Hello\_"
            |
      ```

#### 文件大小类型：

  以整数紧跟单位构成，支持如下单位：
  `"B" "K" "M" "G" "T" "P" `


  lumesh将自动识别单位并以人类可读模式输出，例如：
  ```bash
  print 3050M 1038B 3000G

  # 输出
  2.98G 1K 2.93T
  ```

  文件大小类型可参与运算。
  如：
  ```bash
  1M > 30K      # 返回 True
  fs.ls -l | where(size>20K)   # 筛选大于20k的文件
  ```

#### 日期时间类型
例如：`t'2025-5-20'`


日期时间类型可参与运算。
如：
```bash
t'2025-5-20' > t'2025-1-20'    # 返回True
```
具体操作请参考内置函数的`time`模块

#### 百分数

  书写为百分数，会自动识别为浮点数

  ```bash
  print 37% 2% + 3
  # 输出
  0.37 3.02
  ```

  _百分号紧跟数字后为百分数，数字后空格+%则为模运算_


### 2. **变量声明**
   - **声明变量**：使用 `let` 关键字，支持多变量声明。类型根据赋值自动分配。
     ```bash
     let a             # 申明
     let x = 10        # 申明并赋值
     ```

### 3. **变量赋值**

### 基本赋值

```bash
# 赋值
a = 3

# 变量声明和赋值
let x = 10
let name = "Lumesh"

# 多变量赋值
let a, b, c = 1, 2, 3
let a, b, c = 1
```

非严格模式可以不申明，直接赋值

*在严格模式下，申明是必须且唯一的*

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
let {name: userName, age: userAge} = user_data
```

### 4. **变量使用**
一般可直接使用 `a`或`$a`

*严格模式需要只能使用`$a`*
```bash
print $a
```

### 5. **变量删除**
使用 `del`。
```bash
del x
```

### 6. **变量类型检测**
使用 `typeof` 函数
```bash
let a = 10
typeof a                   # Integer
typeof a == "Integer"      # True
```
