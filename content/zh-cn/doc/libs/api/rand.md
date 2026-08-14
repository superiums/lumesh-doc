---
title: 内置库 rand
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: rand

- `alpha [len=1]`
	随机字母字符

- `alphanum [len=1]`
	随机字母数字字符

- `chance [p=0.5]`
	概率为 p 的随机布尔值

- `choose <list>`
	随机选取一项

- `float [min] [max]`
	随机浮点数。无参数：[0,1)；两个参数：[min,max]

- `int [min] [max]`
	随机整数。无参数：任意 i64；一个参数：[0,max]；两个参数：[min,max]

- `ratio <num> <den>`
	概率为 num/den 的随机布尔值

- `sample <list> <n>`
	随机选取 n 个不同项，不重复

- `seed <integer>`
	设置种子以生成可重现的序列

- `shuffle <list>`
	随机打乱顺序，返回新列表
