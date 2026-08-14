---
title: 内置库 字节
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: bytes

- `concat <bytes> <bytes>`
	连接两个字节

- `contains <bytes> <bytes>`
	包含子序列吗？

- `ends_with <bytes> <bytes>`
	以指定后缀结尾吗？

- `from <string>`
	从utf8字符串获取字节

- `from_base64 <base64_string>`
	从base64字符串获取字节

- `from_escaped <string>`
	从转义文本获取字节。例如 '\n\x41'

- `from_hex <hex_string>`
	从十六进制字符串获取字节，支持'0x'前缀

- `from_list <list>`
	从整数(0-255)列表获取字节

- `is_empty <bytes>`
	是否为空？

- `len <bytes>`
	字节长度

- `pop <bytes>`
	删除最后一个字节，返回新的字节串

- `position <bytes> <bytes>`
	子序列的索引，不存在则返回-1

- `push <bytes> <int>`
	追加字节(0-255)，返回新的字节串

- `repeat <bytes> <n>`
	重复n次

- `reverse <bytes>`
	反转字节

- `slice <bytes> <start> <end>`
	子字节串 [start,end)

- `split <bytes> <int>`
	按字节值(0-255)分割

- `starts_with <bytes> <bytes>`
	以指定前缀开头吗？

- `to_base64 <bytes>`
	转换为base64字符串

- `to_hex <bytes>`
	转换为十六进制字符串

- `to_list <bytes>`
	转换为整数(0-255)列表

- `to_string <bytes>`
	转换为utf8字符串（有损转换）
