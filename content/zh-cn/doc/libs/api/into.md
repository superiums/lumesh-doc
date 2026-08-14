---
title: 内置库 into
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: into

- `boolean <value>`
	转换为 bool

- `caesar <string> [shift=13]`
	凯撒密码

- `csv <expr>`
	转换为 CSV

- `filesize <size_str|int>`
	转换为文件大小。例如 1.5GB, 500K

- `float <str|num|bool>`
	转换为浮点数。% 作为除以100，_ 作为分隔符。例如 12.5% -> 0.125

- `highlight <script>`
	ANSI 高亮脚本

- `int <str|num|bool>`
	转换为整数。支持进制前缀(0x/0o/0b)，_ 作为分隔符。例如 0xff_80

- `json <expr>`
	转换为 JSON

- `pretty <expr>`
	转换为美观格式字符串

- `safe <str>`
	包装字符串，永不求值

- `string <value>`
	转换为字符串

- `strip <string>`
	移除 ANSI 转义码

- `table <output> [split_regex] [headers...]`
	将命令输出解析为表格

- `time <str> [fmt]`
	转换为日期时间

- `toml <expr>`
	转换为 TOML
