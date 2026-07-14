---
title: 内置库
date: 2025-12-25 19:16:45
weight: 40
tags:
 - libs
categories:
 - wiki
 - libs
---

## 内置常数

| 模块  	|  目标     |   代表函数    |
|---------|-----------|----------------|
| [COLOR](COLOR)     | 	颜色    |	green,GREEN, |
| [STYLE](STYLE)     | 	样式    |	bold,italic |
| [MATH](MATH)     | 	数学    |	PI,E, |

用法：
```bash
COLOR.green + 'lume'
STYLE.bold + 'lume'
2 * MATH.PI
```


## 内置函数库

| 模块  	|  目标     |   代表函数    |
|---------|-----------|----------------|
| [fs](fs)     | 	文件系统操作    |	read, write, ls, dirs |
| [string](string)     | 	字符串操作	  | split, join, replace, lower, upper |
| [regex](regex)    |	正则操作	| match, replace, search |
| [list](list)     | 	列表操作	  | first, last, map, filter |
| [set](set)     | 	集合操作	  | first, last, map, filter |
| [map/hmap](map)     | 	映射操作  	| keys, values, merge |
| [math](math)     | 	数学函数 |	sin, cos, sqrt, rand |
| [rand](rand)    |	随机数操作 | int, float, choice, shuffle |
| [time](time)     | 	日期和时间操作 |	now, parse, fmt, stamp |
| [from](from)     | 	数据解析操作	  | json, toml, csv, script, cmd, jq |
| [into](into)     | 	数据转换操作	  | str, int, float, boolean, filesize, time, table, toml, json, csv, highlighted |
| [sys](sys)    |	环境变量操作	| env, define, defined |
| [console](console)    | 	控制台操作	| color, table, prompt |
| [ui](ui)    |	UI组件	| pick, confirm, widget, joinx, joiny, join_flow |
| [log](log)    |	日志操作	| debug, info, warn, error, trace |
| [boolean](boolean)     | 	布尔操作    |	and, or, not, xor |
| [filesize](filesize)     | 	文件大小操作	  | to_bytes, to_kb, to_mb, format |
| [about](about)     | 	关于信息	  | version, build_info |

* * *


### 使用方法
[函数调用方式](calling)

### 获取帮助信息
- `help` 列出所有信息.

- type `help libs`         列出模块.
- type `help <lib-name>`        列出内置库函数.
- type `help tops`                 列出顶层函数.
- type `help <func-name>`          列出函数细节.
- type `help <lib-name>.<func-name>` 列出指定的函数.

* * *

**请注意** 函数可能会更新，如遇与本文档描述不一致，请以`help`显示为准。
