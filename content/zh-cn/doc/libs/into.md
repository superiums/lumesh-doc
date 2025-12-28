---
title: Lumesh Into 模块
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - into
 - conversion
categories:
 - wiki
 - libs
---

Into 模块提供了全面的数据类型转换功能，支持在不同数据类型之间进行转换，包括基础类型转换、时间解析、数据序列化和第三方命令输出解析等。所有转换函数都提供详细的错误处理和类型验证。

## 功能概览

| 功能类别 | 主要函数 | 用途 |
|---------|---------|------|
| **基础类型转换** | `str`, `int`, `float`, `boolean`, `filesize` | 基本数据类型转换 |
| **时间转换** | `time` | 字符串到日期时间转换 |
| **表格转换** | `table` | 命令输出到表格转换 |
| **序列化** | `toml`, `json`, `csv` | 数据序列化为不同格式 |
| **语法高亮** | `highlighted` | ANSI语法高亮 |

## 基础类型转换函数

**`str <value>`** - 将表达式格式化为字符串
- **参数**：`value` (必需): `Any` - 要转换的值
- **返回**：`String` - 格式化后的字符串表示
- **示例**：`Into.str(123)` 返回 `"123"`

**`int <value>`** - 将浮点数或字符串转换为整数
- **参数**：`value` (必需): `Float|String` - 要转换的值
- **返回**：`Integer` - 转换后的整数
- **错误**：无法转换时抛出错误
- **示例**：
  - `Into.int("123")` 返回 `123`
  - `Into.int(3.14)` 返回 `3`

**`float <value>`** - 将整数或字符串转换为浮点数
- **参数**：`value` (必需): `Integer|String` - 要转换的值
- **返回**：`Float` - 转换后的浮点数
- **错误**：无法转换时抛出错误
- **示例**：
  - `Into.float("3.14")` 返回 `3.14`
  - `Into.float(123)` 返回 `123.0`

**`boolean <value>`** - 将值转换为布尔值
- **参数**：`value` (必需): `Any` - 要转换的值
- **返回**：`Boolean` - 转换后的布尔值
- **逻辑**：使用 Lumesh 的真值判断规则

**`filesize <size_str>`** - 解析文件大小字符串为字节数
- **参数**：`size_str` (必需): `String` - 文件大小字符串（如 "1KB", "2MB", "3GB"）
- **返回**：`Integer` - 对应的字节数
- **支持单位**：B, KB, MB, GB, TB, PB
- **示例**：
  - `Into.filesize("1KB")` 返回 `1024`
  - `Into.filesize("2.5MB")` 返回 `2621440`

## 时间转换函数

**`time <datetime_str> [datetime_template]`** - 将字符串转换为日期时间
- **参数**：
  - `datetime_str` (必需): `String` - 日期时间字符串
  - `datetime_template` (可选): `String` - 日期时间格式模板
- **返回**：`DateTime` - 解析后的日期时间对象
- **示例**：
  - `Into.time("2023-12-25")` - 解析 ISO 格式日期
  - `Into.time("25/12/2023", "%d/%m/%Y")` - 使用自定义格式

## 表格转换函数

**`table [headers|header...] <command_output>`** - 将第三方命令输出转换为表格
- **参数**：
  - `headers` (可选): `List[String]|String...` - 表格头部定义
  - `command_output` (必需): `String` - 命令输出字符串
- **返回**：`List[Map]` - 结构化的表格数据
- **用途**：解析 `ps`, `ls`, `df` 等命令的输出为结构化数据
- **示例**：
  ```bash
  ps aux | Into.table()
  ls -l | Into.table("mode", "links", "owner", "group", "size", "date", "name")
  ```

## 序列化函数

**`toml <expr>`** - 将 Lumesh 表达式序列化为 TOML
- **参数**：`expr` (必需): `Any` - 要序列化的表达式
- **返回**：`String` - TOML 格式字符串
- **支持类型**：映射、列表、基础类型
- **示例**：`Into.toml({"name": "Alice", "age": 30})`

**`json <expr>`** - 将 Lumesh 表达式序列化为 JSON
- **参数**：`expr` (必需): `Any` - 要序列化的表达式
- **返回**：`String` - JSON 格式字符串
- **支持类型**：映射、列表、基础类型
- **示例**：`Into.json([1, 2, 3])` 返回 `"[1,2,3]"`

**`csv <expr>`** - 将 Lumesh 表达式序列化为 CSV
- **参数**：`expr` (必需): `List[Map]` - 要序列化的表格数据
- **返回**：`String` - CSV 格式字符串
- **要求**：输入必须是映射列表（表格格式）
- **示例**：
  ```bash
  [{"name": "Alice", "age": 30}, {"name": "Bob", "age": 25}] | Into.csv()
  ```

## 高亮
**`highlighted <script_string>`** - 对脚本字符串进行语法高亮
- **参数**：`script_string` (必需): `String` - 要高亮的脚本字符串
- **返回**：`String` - 带高亮的字符串
- **示例**：
  ```bash
  Into.highlighted "let x = 10; print x"
  # 返回带有 ANSI 颜色代码的高亮字符串

  Fs.read "script.lm" | Into.highlighted() | print
  # 高亮显示脚本文件内容
  ```

**`striped <string>`** - 移除所有 ANSI 转义码
- **参数**：`string` (必需): `String` - 包含 ANSI 转义码的字符串
- **返回**：`String` - 移除转义码后的纯文本

## 使用示例

### 基础类型转换
```bash
# 数字转换
Into.int("42")        # 返回 42
Into.float("3.14")    # 返回 3.14
Into.str(123)         # 返回 "123"

# 布尔转换
Into.boolean(1)       # 返回 true
Into.boolean("")      # 返回 false
```

### 文件大小转换
```bash
# 解析文件大小
Into.filesize("1KB")    # 返回 1024
Into.filesize("2.5MB")  # 返回 2621440
Into.filesize("1GB")    # 返回 1073741824
```

### 数据序列化
```bash
# JSON 序列化
let data = {"users": [{"name": "Alice"}, {"name": "Bob"}]}
Into.json(data)

# TOML 序列化
let config = {"database": {"host": "localhost", "port": 5432}}
Into.toml(config)

# CSV 序列化
Fs.ls("-l") | Into.csv()
```

### 命令输出解析
```bash
# 解析系统命令输出
ps aux | Into.table() | where(cpu > 5.0)
df -h | Into.table() | select("filesystem", "used", "available")
```

## 管道操作示例

```bash
# 数据处理管道
"123.45" | Into.float() | Math.round() | Into.str()
# 结果: "123"

# 文件大小处理
Fs.ls("-l") | List.map((f) -> Into.filesize(f.size)) | List.sum()
# 计算目录总大小

# 配置文件生成
{"server": {"port": 8080, "host": "0.0.0.0"}} | Into.toml() | Fs.write("config.toml")
```

## Notes

Into 模块是 Lumesh 中重要的数据转换工具，提供了类型安全的转换机制。所有转换函数都包含详细的错误处理，确保转换失败时能提供清晰的错误信息。参数类型说明中，`<>` 表示必需参数，`[]` 表示可选参数。特别注意文件大小转换支持常见的存储单位，时间转换支持多种日期格式。

多数函数已经连接到对应类型，可以通过链式调用使用，如：

```bash
"36".to_int()
'2000K'.to_filesize()
```
