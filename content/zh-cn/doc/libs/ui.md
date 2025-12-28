---
title: Lumesh UI 模块
date: 2025-06-23 19:20:45
highlight: true
tags:
 - libs
 - ui
categories:
 - wiki
 - libs
---

`ui` 模块提供了一套交互式命令行界面函数，支持用户输入多种类型的数据（整数、浮点数、文本、密码等）以及选择选项的功能。调用方式可以使用 `Ui.function_name(arg1, arg2)` 或空格分隔参数 `Ui.function_name arg1 arg2`。

## 查看帮助
```bash
help ui
```

## 函数列表
| 函数名     | 描述                   | 参数格式                               | 返回值类型    |
|------------|------------------------|----------------------------------------|---------------|
| int        | 读取整数输入            | `<提示消息>`                           | Integer       |
| float      | 读取浮点数输入          | `<提示消息>`                           | Float         |
| text       | 读取文本输入            | `<提示消息>`                           | String        |
| passwd     | 读取密码输入            | `<提示消息> [是否需要确认]`            | String        |
| confirm    | 用户确认（是/否）       | `<提示消息>`                           | Boolean       |
| pick       | 单选（选择一个选项）    | `[提示或配置] <选项列表/字符串>`        | 任意类型      |
| multi_pick | 多选（选择多个选项）    | `[提示或配置] <选项列表/字符串>`        | List          |

---

## 输入函数详细说明

### 1. int - 读取整数输入
从用户获取整数输入，验证输入有效性。

**语法：**
```bash
Ui.int "提示消息"
```

**示例：**
```bash
; 读取用户年龄
let age = (Ui.int "请输入您的年龄：")
```

---

### 2. float - 读取浮点数输入
从用户获取浮点数输入，验证输入有效性。

**语法：**
```bash
Ui.float "提示消息"
```

**示例：**
```bash
; 读取商品价格
let price = Ui.float "请输入商品价格："
```

---

### 3. text - 读取文本输入
从用户获取一行文本输入。

**语法：**
```bash
Ui.text "提示消息"
```

**示例：**
```bash
# 读取用户名
let name = Ui.text("请输入您的姓名：")
```

---

### 4. passwd - 读取密码输入
安全读取密码输入，支持是否需要确认密码的选项。

**语法：**
```bash
Ui.passwd "提示消息" [confirm?]
```

**参数：**
- `confirm?`（可选）：布尔值，默认为true
  - `true`：需要确认密码（输入两次）
  - `false`：不需要确认（输入一次）

**示例：**
```bash
# 读取密码（需要确认）
let pwd1 = (Ui.passwd "设置密码：" True)

# 读取密码（不需要确认）
let pwd2 = (Ui.passwd "请输入密码：" false)
```

---

### 5. confirm - 用户确认
让用户回答是/否的问题，返回布尔值。

**语法：**
```bash
Ui.confirm "提示消息"
```

**示例：**
```bash
# 确认是否继续操作
if (Ui.confirm "是否继续操作？") {
    # 继续执行的代码
}
```

---

## 选择函数详细说明

### 6. pick - 单选
让用户从多个选项中**单选**一个选项。

**语法：**
```bash
Ui.pick [配置|提示] 选项列表
```

**参数格式：**
- **参数1（可选）：配置对象或提示字符串**
  - 类型：`Map` 或 `String`
  - 支持配置项：
    - `msg`: 提示消息（字符串）
    - `page_size`: 每页显示的选项数量（整数）
    - `starting_cursor`: 初始光标位置（整数）
- **选项数据源（二选一）：**
  1. **列表表达式**：`list("选项1" "选项2" ...)`
  2. **多行字符串**：`"选项1\n选项2\n..."`
  3. **多参数形式**：直接传入多个选项表达式 `Ui.pick 选项1 选项2 ...`

**示例：**

1. 简单单选：
```bash
let color = (Ui.pick "选择颜色：" ["红", "绿", "蓝"])
```

2. 使用字符串选项源：
```bash
let fruits = "苹果\n香蕉\n橙子"
let choice = (Ui.pick "选择喜欢的水果：" fruits)
```

3. 使用详细配置：
```bash
let oss = ["win","linux","mac"]
let choice = Ui.pick({
    msg: "请选择操作系统：",
    page_size: 5,
    starting_cursor: 1,
    }, oss)
```

### 6. multi_pick - 多选
让用户从选项列表中**多选**。

**语法：**
```bash
Ui.multi_pick [配置|提示] 选项列表
```

**参数格式：**
- **参数1（可选）：配置对象或提示字符串**
  - 类型：`Map` 或 `String`
  - 支持配置项：
    - `msg`: 提示消息（字符串）
    - `page_size`: 每页显示的选项数量（整数）
    - `starting_cursor`: 初始光标位置（整数）
- **选项数据源（二选一）：**
  1. **列表表达式**：`list("选项1" "选项2" ...)`
  2. **多行字符串**：`"选项1\n选项2\n..."`
  3. **多参数形式**：直接传入多个选项表达式 `Ui.pick 选项1 选项2 ...`

**示例：**

```bash
Fs.ls -l | Ui.multi_pick "删除哪些文件?"

let oss = ["win","linux","mac","android","nokia"]
let choice = Ui.multi_pick({
    msg: "您用过哪些操作系统：",
    page_size: 5,
    starting_cursor: 1,
    }, oss)

```
