---
title: Lumesh From 模块
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - parse
 - from
categories:
 - wiki
 - libs
---

From 模块提供了数据格式解析和转换功能，支持 JSON、TOML、CSV 等常见数据格式的解析与序列化，以及表达式解析、语法高亮和数据查询等高级功能。  所有函数都支持管道操作，并提供统一的错误处理机制。

## 功能概览

| 功能类别 | 主要函数 | 用途 |
|---------|---------|------|
| **数据格式解析** | `json`, `toml`, `csv` | 解析各种数据格式为 Lumesh 表达式 |
| **数据格式序列化** | `to_json`, `to_toml`, `to_csv` | 将 Lumesh 表达式转换为各种数据格式 |
| **表达式解析** | `script`  | 解析脚本字符串 |
| **命令输出解析** | `cmd` | 解析命令输出为结构化数据 |
| **数据查询** | `jq` | 对 JSON/TOML 数据进行类 jq 查询 |

## 数据格式解析函数

这些函数将各种数据格式解析为 Lumesh 表达式：

**`json <json_string>`** - 解析 JSON 为 Lumesh 表达式
- **参数**：`json_string` (必需): `String` - 要解析的 JSON 字符串
- **返回**：`Expression` - 解析后的表达式
- **示例**：
  ```bash
  From.json '{"name": "Alice", "age": 30}'
  # 返回: {name: "Alice", age: 30}

  '{"items": [1, 2, 3]}' | From.json()
  # 返回: {items: [1, 2, 3]}
  ```

**`toml <toml_string>`** - 解析 TOML 为 Lumesh 表达式
- **参数**：`toml_string` (必需): `String` - 要解析的 TOML 字符串
- **返回**：`Expression` - 解析后的表达式
- **示例**：
  ```bash
  From.toml 'name = "Alice"\nage = 30'
  # 返回: {name: "Alice", age: 30}

  Fs.read "config.toml" | From.toml()
  # 解析 TOML 配置文件
  ```

**`csv <csv_string>`** - 解析 CSV 为 Lumesh 表达式
- **参数**：`csv_string` (必需): `String` - 要解析的 CSV 字符串
- **返回**：`List[Map]` - 解析后的表格数据
- **示例**：
  ```bash
  From.csv "name,age\nAlice,30\nBob,25"
  # 返回: [{name: "Alice", age: "30"}, {name: "Bob", age: "25"}]

  # 支持自定义分隔符（通过 IFS 环境变量）
  IFS = ";"
  From.csv "name;age\nAlice;30"
  ```


## 表达式解析函数

这些函数用于解析和处理 Lumesh 脚本：

**`script <script_string>`** - 解析脚本字符串为 Lumesh 表达式
- **参数**：`script_string` (必需): `String` - 要解析的脚本字符串
- **返回**：`Expression` - 解析后的表达式
- **示例**：
  ```bash
  From.script "1 + 2 * 3"
  # 返回: 7

  From.script "let x = 10; x * 2" | eval()
  # 动态执行脚本代码
  ```


## 命令输出解析函数

**`cmd [headers|header...] <cmd_output_string>`** - 解析命令输出为结构化数据
- **参数**：`headers` (可选): `List` - 自定义表头，`cmd_output_string` (必需): `String` - 命令输出字符串
- **返回**：`List[Map]` - 结构化数据表
- **示例**：
  ```bash
  ls -l | From.cmd()
  # 自动检测表头，解析 ls 输出

  ps aux | From.cmd(USER,PID,CPU,MEM,COMMAND)
  # 使用自定义表头解析 ps 输出

  df -h | From.cmd() | .drop(1) | where(C2.to_filesize()>10G) | pprint
  # 解析磁盘使用情况并过滤
  ```

## 数据查询函数

**`jq <query_string> <json_data>`** - 对 JSON 或 TOML 数据应用类 jq 查询
- **参数**：`query_string` (必需): `String` - 查询字符串，`json_data` (必需): `Expression` - JSON 数据
- **返回**：`Expression` - 查询结果
- **示例**：
  ```bash
  From.jq ".name" '{"name": "Alice", "age": 30}'
  # 返回: "Alice"

  From.jq ".[]" '[1, 2, 3]'
  # 返回数组中的所有元素

  From.jq "select(.age > 25)" '[{"name":"Alice","age":30},{"name":"Bob","age":20}]'
  # 过滤年龄大于 25 的记录
  ```

## Notes

From 模块是 Lumesh 内置模块系统中的重要组件，在模块注册表中注册为 "From"。

该模块与 Into 模块有部分功能重叠，Into 模块也提供了一些序列化功能的别名。  CSV


解析支持通过环境变量 `IFS` 自定义分隔符，默认使用逗号分隔。
