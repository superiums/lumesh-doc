---
title: 内置库 from
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: from

- `cmd <output> [split_regex] [headers...]`
	将命令输出解析为表格

- `csv <csv_string>`
	解析 CSV 字符串，必须包含表头行

- `jq <json_string> <query_string>`
	在 JSON 字符串上执行类似 jq 的查询。例如 '.a|.[]|select(.n>1)'

- `json <json_string>`
	解析 JSON 字符串

- `script <script_string>`
	将脚本文本解析为表达式（未求值）

- `toml <toml_string>`
	解析 TOML 字符串
