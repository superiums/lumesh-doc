---
title: 内置库 regex
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: regex

- `capture <pattern> <text>`
	第一个匹配的分组 [完整匹配,g1,g2,...]

- `captures <pattern> <text>`
	所有匹配的分组 [[完整匹配,g1,...],...]

- `find <pattern> <text>`
	第一个匹配，返回 {start,end,found}

- `find_all <pattern> <text>`
	所有匹配，返回 {start,end,found} 列表

- `from <pattern_string> [i|m|s|x|R|U]`
	从字符串模式构建正则表达式

- `is_match <pattern> <text>`
	包含匹配？

- `named_captures <pattern> <text>`
	命名分组映射。例如 g'(?<y>\d+)'

- `replace <text> <pattern> <replacement>`
	替换第一个匹配

- `replace_all <text> <pattern> <replacement>`
	替换所有匹配

- `split <pattern> <text>`
	按模式分割
