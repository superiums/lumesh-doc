---
title: Lumesh 内置模块
date: 2025-12-25 19:16:45
weight: 40
---


| 模块  	|  目标     |   代表函数    |
|---------|-----------|----------------|
| [Fs](/zh-cn/libs/fs)     | 	文件系统操作    |	read, write, ls, dirs |
| [String](/zh-cn/libs/string)     | 	字符串操作	  | split, join, replace, lower, upper |
| [Regex](/zh-cn/libs/regex)    |	正则操作	| match, replace, search |
| [List](/zh-cn/libs/list)     | 	列表操作	  | first, last, map, filter |
| [Map](/zh-cn/libs/map)     | 	映射操作  	| keys, values, merge |
| [Math](/zh-cn/libs/math)     | 	数学函数 |	sin, cos, sqrt, rand |
| [Rand](/zh-cn/libs/rand)    |	随机数操作 | int, float, choice, shuffle |
| [Time](/zh-cn/libs/time)     | 	日期和时间操作 |	now, parse, fmt, stamp |
| [From](/zh-cn/libs/from)     | 	数据解析操作	  | json, toml, csv, script, cmd, jq |
| [Into](/zh-cn/libs/into)     | 	数据转换操作	  | str, int, float, boolean, filesize, time, table, toml, json, csv, highlighted |
| [Sys](/zh-cn/libs/sys)    |	环境变量操作	| env, define, defined |
| [Console](/zh-cn/libs/console)    | 	控制台操作	| color, table, prompt |
| [Ui](/zh-cn/libs/ui)    |	UI组件	| pick, confirm, widget, joinx, joiny, join_flow |
| [Log](/zh-cn/libs/log)    |	日志操作	| debug, info, warn, error, trace |
| [Boolean](/zh-cn/libs/boolean)     | 	布尔操作    |	and, or, not, xor |
| [Filesize](/zh-cn/libs/filesize)     | 	文件大小操作	  | to_bytes, to_kb, to_mb, format |
| [About](/zh-cn/libs/about)     | 	关于信息	  | version, build_info |

* * *


### 使用方法
[函数调用方式](calling)

### 获取帮助信息
- `help` 列出所有信息.

- type `help libs/modules`         列出模块.
- type `help <module-name>`        列出模块函数.
- type `help tops`                 列出顶层函数.
- type `help <func-name>`          列出函数细节.
- type `<module-name>.<func-name>` 列出指定函数.

* * *
