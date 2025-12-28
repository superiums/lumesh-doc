---
title: Lumesh Regex 模块
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - regex
 - pattern
categories:
 - wiki
 - libs
---

Regex 模块提供了全面的正则表达式操作功能，支持模式匹配、查找、捕获组提取、文本分割和替换等操作。所有函数都基于 `regex-lite` 库实现，提供高效的正则表达式处理能力。

## 功能概览

| 功能类别 | 主要函数 | 用途 |
|---------|---------|------|
| **匹配定位** | `find`, `find_all` | 查找匹配位置和内容 |
| **匹配验证** | `match` | 验证整个文本是否匹配模式 |
| **捕获组操作** | `capture`, `captures`, `capture_name` | 提取捕获组内容 |
| **文本处理** | `split`, `replace` | 文本分割和替换 |

## 匹配定位函数

**`find <pattern> <text>`** - 查找第一个匹配项
- **参数**：
  - `pattern` (必需): `String|Regex` - 正则表达式模式
  - `text` (必需): `String` - 要搜索的文本
- **返回**：`Map|None` - 匹配信息映射，包含 `start`、`end`、`found` 字段，未找到时返回 None
- **示例**：`Regex.find(r'\d+', "abc123def")` 返回 `{start: 3, end: 6, found: "123"}`

**`find_all <pattern> <text>`** - 查找所有匹配项
- **参数**：
  - `pattern` (必需): `String|Regex` - 正则表达式模式
  - `text` (必需): `String` - 要搜索的文本
- **返回**：`List[Map]` - 所有匹配项的列表，每个元素包含 `start`、`end`、`found` 字段
- **示例**：`Regex.find_all(r'\d+', "abc123def456")` 返回匹配所有数字的列表

## 匹配验证函数

**`match <pattern> <text>`** - 检查整个文本是否匹配模式
- **参数**：
  - `pattern` (必需): `String|Regex` - 正则表达式模式
  - `text` (必需): `String` - 要验证的文本
- **返回**：`Boolean` - 整个文本匹配时返回 true
- **示例**：`Regex.match(r'^\d+$', "123")` 返回 `true`

## 捕获组操作函数

**`capture <pattern> <text>`** - 获取第一个匹配的捕获组
- **参数**：
  - `pattern` (必需): `String|Regex` - 包含捕获组的正则表达式
  - `text` (必需): `String` - 要匹配的文本
- **返回**：`List|None` - 捕获组列表，索引0为完整匹配，后续为各捕获组
- **示例**：`Regex.capture(r'(\d{4})-(\d{2})-(\d{2})', "2023-12-25")` 返回 `["2023-12-25", "2023", "12", "25"]`

**`captures <pattern> <text>`** - 获取所有匹配的捕获组
- **参数**：
  - `pattern` (必需): `String|Regex` - 包含捕获组的正则表达式
  - `text` (必需): `String` - 要匹配的文本
- **返回**：`List[List]` - 所有匹配的捕获组列表
- **示例**：多次匹配时返回每次匹配的捕获组

**`capture_name <pattern> <text>`** - 获取命名捕获组
- **参数**：
  - `pattern` (必需): `String|Regex` - 包含命名捕获组的正则表达式
  - `text` (必需): `String` - 要匹配的文本
- **返回**：`Map|None` - 命名捕获组的映射，键为组名，值为匹配内容
- **示例**：`Regex.capture_name(r'(?P<year>\d{4})-(?P<month>\d{2})', "2023-12")` 返回 `{year: "2023", month: "12"}`

## 文本处理函数

**`split <pattern> <text>`** - 按正则表达式分割文本
- **参数**：
  - `pattern` (必需): `String|Regex` - 分割模式
  - `text` (必需): `String` - 要分割的文本
- **返回**：`List[String]` - 分割后的字符串列表
- **示例**：`Regex.split(r'\s+', "hello   world  test")` 返回 `["hello", "world", "test"]`

**`replace <pattern> <replacement> <text>`** - 替换所有匹配项
- **参数**：
  - `pattern` (必需): `String|Regex` - 匹配模式
  - `replacement` (必需): `String` - 替换内容
  - `text` (必需): `String` - 目标文本
- **返回**：`String` - 替换后的文本
- **示例**：`Regex.replace(r'\d+', "X", "abc123def456")` 返回 `"abcXdefX"`

## 参数处理机制

Regex 模块的函数支持灵活的参数类型处理。支持处理不同的参数组合：

- 支持 `Regex` 类型和 `String` 类型的模式参数
- 自动将字符串编译为正则表达式
- 提供详细的错误信息

## 使用示例

### 基础匹配操作
```bash
# 查找数字
Regex.find(r'\d+', "Price: $123.45")
# 返回: {start: 8, end: 11, found: "123"}

# 验证邮箱格式
Regex.match(r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$', "user@example.com")
# 返回: true
```

### 捕获组操作
```bash
# 解析日期
Regex.capture(r'(\d{4})-(\d{2})-(\d{2})', "Today is 2023-12-25")
# 返回: ["2023-12-25", "2023", "12", "25"]

# 使用命名捕获组
Regex.capture_name(r'(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})', "2023-12-25")
# 返回: {year: "2023", month: "12", day: "25"}
```

### 文本处理
```bash
# 分割文本
Regex.split(r'[,;]\s*', "apple, banana; cherry")
# 返回: ["apple", "banana", "cherry"]

# 替换操作
Regex.replace(r'\b\w+@\w+\.\w+\b', "[EMAIL]", "Contact us at support@example.com")
# 返回: "Contact us at [EMAIL]"
```

### 管道操作示例
```bash
# 提取所有数字并求和
"Price: $123, Tax: $45, Total: $168" | Regex.find_all(r'\d+') | List.map((m) -> Into.int(m.found)) | List.sum()
# 结果: 336

# 清理和格式化文本
"  Hello,   World!  " | Regex.replace(r'\s+', " ") | String.trim()
# 结果: "Hello, World!"
```

## Notes

Regex 模块基于 `regex-lite` 库实现，提供了高效的正则表达式处理能力。所有函数都支持字符串和 `Regex` 类型的模式参数，自动处理类型转换。捕获组功能特别强大，支持位置捕获和命名捕获两种方式。参数类型说明中，`<>` 表示必需参数，`[]` 表示可选参数。

建议使用原始字符串（`r'pattern'`）来避免转义字符的复杂性。

实际使用时，支持链式调用，如：
```bash
let rg = r'[,;]\s*'
rg.split("apple, banana; cherry")
```
在示例中，为方便理解，仅使用了类型名称调用。
