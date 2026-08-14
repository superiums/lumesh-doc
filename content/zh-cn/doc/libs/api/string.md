---
title: 内置库 string
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: string

- `black <string>`
	黑色前景色

- `blink <string>`
	闪烁

- `blue <string>`
	蓝色前景色

- `bold <string>`
	粗体

- `center <string> <length> [pad_char=' ']`
	两端填充

- `chars <string>`
	转换为字符列表

- `clr <string> <0..256>`
	256色前景色，代码 0-255

- `clr_bg <string> <0..256>`
	256色背景色，代码 0-255

- `color <string> <#hex|name|r,g,b>`
	真彩色前景色。例如 #ff0000, red, 255,0,0

- `color_bg <string> <#hex|name|r,g,b>`
	真彩色背景色。例如 #ff0000, red, 255,0,0

- `colors [swatches?]`
	列出颜色名称，或显示色块

- `concat <string>...`
	连接字符串

- `contains <string> <substring>`
	包含？

- `cyan <string>`
	青色前景色

- `dim <string>`
	变暗

- `ends_with <string> <substring>`
	以...结尾？

- `escape <string>`
	转义控制字符为 \n \t \xNN 等

- `get <string> <index>`
	索引处的字符。负索引从末尾计数

- `green <string>`
	绿色前景色

- `grep <string> <substring>`
	匹配子串的行

- `href <url> <text>`
	终端超链接

- `insert <string> <index> <string>`
	在索引处插入字符串

- `invert <string>`
	反转前景/背景

- `is_alpha <string>`
	是字母？

- `is_alphanumeric <string>`
	是字母数字？

- `is_ascii <string>`
	是 ASCII？

- `is_ascii_control <string>`
	是 ASCII 控制字符？

- `is_ascii_digit <string>`
	是 ASCII 数字？

- `is_ascii_hexdigit <string>`
	是 ASCII 十六进制数字？

- `is_ascii_punctuation <string>`
	是 ASCII 标点符号？

- `is_empty <string>`
	是否为空？

- `is_lower <string>`
	是小写？

- `is_numeric <string>`
	是数字？

- `is_title <string>`
	是标题大写？

- `is_upper <string>`
	是大写？

- `is_whitespace <string>`
	是空白字符？

- `italic <string>`
	斜体

- `len <string>`
	字符计数

- `lines <string>`
	转换为行列表

- `lower <string>`
	转换为小写

- `magenta <string>`
	洋红色前景色

- `max_len <string>`
	最长行长度

- `pad_end <string> <length> [pad_char=' ']`
	右填充

- `pad_start <string> <length> [pad_char=' ']`
	左填充

- `paragraphs <string>`
	转换为段落列表

- `position <string> <substring> [start]`
	子串的索引，或 None。从 [start] 开始搜索

- `red <string>`
	红色前景色

- `repeat <string> <count>`
	重复 n 次

- `replace <string> <old> <new>`
	替换所有匹配项

- `rev <string>`
	反转

- `slice <string> <start> [end]`
	子字符串 [start,end)

- `sort <string> ['+'|'-'|key_fn]`
	对行排序

- `split <string> [delimiter]`
	按分隔符/空白字符分割

- `split_at <string> <index>`
	在索引处分割

- `starts_with <string> <substring>`
	以...开头？

- `strike <string>`
	删除线

- `strip_ansi <string>`
	移除 ANSI 转义码

- `strip_prefix <string> <prefix>`
	移除前缀

- `strip_suffix <string> <suffix>`
	移除后缀

- `title <string>`
	转换为标题大写

- `to_filesize <size_str>`
	转换为文件大小。例如 1.5GB, 500K

- `to_float <value>`
	转换为浮点数。% 作为除以100，_ 作为分隔符。例如 12.5%

- `to_int <value>`
	转换为整数。支持进制前缀(0x/0o/0b)，_ 作为分隔符。例如 0xff_80

- `to_safe <str>`
	包装字符串，永不求值

- `to_table <output> [regex] [headers...]`
	将命令输出解析为表格

- `to_time <str> [fmt]`
	转换为日期时间

- `trim <string>`
	修剪两端

- `trim_end <string>`
	修剪末尾

- `trim_start <string>`
	修剪开头

- `underline <string>`
	下划线

- `unescape <string>`
	转义的逆操作，解析 \n \t \xNN \uXXXX

- `upper <string>`
	转换为大写

- `white <string>`
	白色前景色

- `words <string>`
	转换为单词列表

- `words_quoted <string>`
	转换为单词列表，作为一项引用

- `wrap <string> <width>`
	在指定宽度处换行

- `yellow <string>`
	黄色前景色
