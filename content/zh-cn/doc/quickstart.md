---
title: 快速开始
date: 2026-03-16 16:16:00
highlight: true
weight: 1
tags:
 - install
categories:
 - wiki
 - install
---

# Lumesh 用户教程 快速开始

## 1. 快速入门指南

### 1.1 安装

- 执行安装命令
```bash
curl -fsSL https://raw.githubusercontent.com/superiums/lumesh/main/docs/install.sh | bash

```

- 或手动下载并运行：
 [https://github.com/superiums/lumesh/releases](https://github.com/superiums/lumesh/releases)

```bash
bash install.sh
```


### 1.2 启动
安装完成后，启动交互式 shell：
```bash
lume          # 完整交互式 shell（REPL、补全、高亮）
lumesh script.lm  # 执行脚本文件
```

### 1.3 CFM 模式快速上手（命令优先模式）

- 为什么需要CFM模式？

  > 编程与命令的矛盾

  + 操作符和数字是字符串？

      为了提供更现代化的编程特性，Lumesh能够识别数字、运算符等更多类型，例如：

      您可以直接执行： `1+2` 这样的指令。

      但传统命令中也有部分命令把数字和运算符作为普通字符串处理，例如`ping 1.1.1.1`, `chmod +x script.sh`.

      您可以在这些命令中添加引号来让Lumesh正确识别他们为字符串： 如
    `ping '1.1.1.1'` , `chmod '+x' script.sh`

      但为了更方便，我们提供了CFM模式。
    
  + 单个单词是命令还是表达式？

      在编程体验中，通常一个单独的symbol就是一个变量；如果将它作为命令，这很危险。

      在Bash中通常直接作为命令，如`ls`， 这样很便捷。

      在Lumesh中，既要安全，又要便捷，所以有了CFM模式。

  > CFM（Command First Mode）是 Lumesh 为日常命令行使用设计的模式, 以提供更好的命令兼容性。

#### 1.3.1 什么是 CFM
- 在单行输入时，优先将第一个词识别为命令。
- 无需为 IP 地址、运算符等加引号。
- 示例：
  ```bash
  ls                    # 直接执行命令
  ping 1.1.1.1          # 直接执行，无需引号
  chmod +x script.sh    # 运算符直接写 
  ```

#### 1.3.2 临时切换模式
在交互式 REPL 中，可用行前缀临时切换解析模式：
- `:` 前缀：临时切换到普通模式（可写表达式）
- `>` 前缀：临时强制进入 CFM（即使全局关闭）
```bash
: 1 + 2                # 普通模式，计算表达式
> ls                   # 强制 CFM
```


#### 1.3.3 全局开启/关闭 CFM
通过 `sys.set_cfm` 函数或配置变量`set LUME_CFM = true`控制：
```bash
sys.set_cfm(true)      # 开启 CFM（默认）
sys.set_cfm(false)     # 关闭 CFM
```

#### 1.3.4 CFM 与普通模式的区别
- CFM 禁用数字字面量和数学运算（避免与命令参数冲突）。
- 普通模式支持完整表达式语法（数字、范围、比较等）。
- 脚本执行默认不开启 CFM，可用 `>` 前缀强制。 

### 1.4 其他模式简要说明

#### 1.4.1 严格模式（Strict Mode）
- 变量必须先声明后使用，禁止隐式声明。
- 运算符需空格分隔。
- 通过 `sys.set_strict(true)` 或配置 `LUME_STRICT=true` 开启。 

#### 1.4.2 打印直出模式（Print Direct Mode）
- 控制是否在 REPL 直接打印表达式结果。
- 通过 `sys.set_pdm` 切换，或配置 `LUME_PRINT_DIRECT`。  

## 2. 语言参考手册

### 2.1 命令语法
- 命令优先(CFM)模式下，命令与Bash基本保持一致。但提供部分增强语法支持。
如：
```bash
ls
ls ~ | string.red
```

- 普通(NM)模式下，命令与Bash略有区别。如上节所述：
1. 如有单个符号，需添加占位符，以让lume识别为命令，如：
```bash
ls _
```

> 管道符右侧可免：`'/opt' | ls`

2. 如有操作符或数字作为参数，应加引号
```bash
ping '1.1.1.1'
chmod '+x' .
```

- 模式切换：参考上节

### 2.2 语法设计特性

#### 2.2.1 JavaScript-like 语法
支持解构、lambda、链式调用：
```bash
let user = {name: "Alice", age: 25}
let {name, age} = user
let numbers = 1..10 | list.filter(x -> x > 5)
```

#### 2.2.2 现代编程语言特性
- 函数装饰器：
```@decorator
fn my() {}
```
- 模块导入：`use module as alias; alias::func()`
- 错误捕获操作符：`?.` `?:` `?+` 等。

#### 2.2.3 表达式类型系统
所有结构均为表达式，支持 50+ 表达式类型。  

### 2.3 数据类型概览

#### 2.3.1 基础类型
- 字符串（双引号转义、单引号原始）
- 数字（整型、浮点）
- 布尔（`true`/`false`）

#### 2.3.2 复合类型
- 列表：`[1, "a", true]` `1...10`
- 映射：`{key: "value"}`
- 范围：`1..10`（左闭右开）

增强类型：
- 集合：`S{a,b,c}`
- 映射：`M{a,b,c}` `H{a,b,c}`

#### 2.3.3 特殊类型
- 正则：`r'^\w+'`
- 时间：`t'2025-5-20'`
- 文件大小：支持 `5K`、`1M` 等后缀。
- 百分数： `5%`

### 2.4 控制流结构概览

#### 2.4.1 条件语句
```bash
let res = if cond { "A" } else { "B" }
```

#### 2.4.2 循环语句
```bash
for i in 0..3 { print i }
while x < 10 { x += 1 }
loop { break }
```

#### 2.4.2 match语句
```bash
match x {
    a => {}
    b => {}
}
```

#### 2.4.3 异常处理机制
使用错误捕获操作符：
```bash
command ?.         # 忽略错误
command ?: default # 错误时返回默认值
command ?: x -> {} # 错误处置函数
```
 
### 2.5 管道
- 功能与Bash管道基本相同，支持字节流
- **增强**：支持结构化数据流
- **增强**：支持占位符 `_`, 从而改变管道目标位置
```bash
'b' | print 'a' _ 'c'
```
- **增强**：支持循环派发
```bash
0..6 |> print 
```

### 2.6 内置模块概览

> 模块列表：通过 `help libs` 查看。

#### 2.6.1 内置常数
| COLOR | MATH  | STYLE |
|-------|-------|-------|

用法：
`COLOR.RED + 'lume'`


#### 2.6.2 内置库函数
| 模块 | 功能 | 示例 |
|------|------|------|
| string/regex | 字符串处理 | `string.split`, `string.join` |
| list/set | 集合操作 | `list.map`, `list.filter` |
| map/hmap | 映射操作 | `map.keys`, `map.merge` |
| fs | 文件系统 | `fs.read`, `fs.write`, `fs.exists` |
| time | 时间操作 | `time.now`, `time.format` |
| math/rand | 数学计算 | `math.abs`, `math.pow` |
| ui | 用户交互 | `ui.pick`, `ui.confirm` |
| sys/console | 系统功能 | `sys.env`, `sys.set_cfm` |
| into/from/log | 数据转换与日志 | `into.str`, `from.json`, `log.info` |  

- 加载机制：动态懒加载，首次调用时初始化。
- 命名空间：`lib.function()` 调用。

#### 2.7 作用域
- 以`%{ }`声明的语句块，具有独立作用域
- 函数和循环语句，自动具有独立作用域
- let 声明的变量，只在所在作用域有效
- 要更改父级作用域变量，可使用set

## 3. 基础配置概览

### 3.1 config.lm 结构说明
配置文件位于 `~/.config/lumesh/config.lm`（Unix）或对应平台路径，包含：
- 外观：logo、主题、欢迎语。
- 提示符：`LUME_PROMPT_SETTINGS` 与 `LUME_PROMPT_TEMPLATE`。
- 交互：Vi 模式、补全、历史。
- 按键绑定：热键与缩写。  

### 3.2 环境变量设置
常用变量：
- `LUME_CFM`：是否默认开启 CFM。
- `LUME_STRICT`：是否默认开启严格模式。
- `LUME_THEME`：语法高亮主题。
- `LUME_HOT_MODIFIER`/`LUME_HOT_KEYS`：自定义热键。
- `LUME_ABBREVIATIONS`：文本缩写映射。  

### 3.3 初始配置优化建议
- 启用 CFM 以提升日常命令行体验。
- 根据需要开启严格模式编写脚本。
- 自定义热键（如 Alt+H 历史、Alt+X 书签）。
- 配置提示符模板显示 Git 分支与模式标签。  

---
