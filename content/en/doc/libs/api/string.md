---
title: Builtin Lib STRING
date: 2026-08-14 11:26:54
---
	
## Builtin Functions for Lib: string

- `black <string>`
	black fg

- `blink <string>`
	blink

- `blue <string>`
	blue fg

- `bold <string>`
	bold

- `center <string> <length> [pad_char=' ']`
	pad both ends

- `chars <string>`
	to char list

- `clr <string> <0..256>`
	256-color fg, code 0-255

- `clr_bg <string> <0..256>`
	256-color bg, code 0-255

- `color <string> <#hex|name|r,g,b>`
	true color fg. e.g. #ff0000, red, 255,0,0

- `color_bg <string> <#hex|name|r,g,b>`
	true color bg. e.g. #ff0000, red, 255,0,0

- `colors [swatches?]`
	list color names, or with swatches

- `concat <string>...`
	join strings

- `contains <string> <substring>`
	contains?

- `cyan <string>`
	cyan fg

- `dim <string>`
	dim

- `ends_with <string> <substring>`
	ends with?

- `escape <string>`
	escape control chars to \n \t \xNN etc.

- `get <string> <index>`
	char at index. negative counts from end

- `green <string>`
	green fg

- `grep <string> <substring>`
	lines matching substring

- `href <url> <text>`
	terminal hyperlink

- `insert <string> <index> <string>`
	insert string at index

- `invert <string>`
	invert fg/bg

- `is_alpha <string>`
	is alphabetic?

- `is_alphanumeric <string>`
	is alphanumeric?

- `is_ascii <string>`
	is ascii?

- `is_ascii_control <string>`
	is ascii control char?

- `is_ascii_digit <string>`
	is ascii digit?

- `is_ascii_hexdigit <string>`
	is ascii hexdigit?

- `is_ascii_punctuation <string>`
	is ascii punctuation?

- `is_empty <string>`
	is empty?

- `is_lower <string>`
	is lowercase?

- `is_numeric <string>`
	is numeric?

- `is_title <string>`
	is title case?

- `is_upper <string>`
	is uppercase?

- `is_whitespace <string>`
	is whitespace?

- `italic <string>`
	italic

- `len <string>`
	char count

- `lines <string>`
	to line list

- `lower <string>`
	to lowercase

- `magenta <string>`
	magenta fg

- `max_len <string>`
	max line length

- `pad_end <string> <length> [pad_char=' ']`
	pad at end

- `pad_start <string> <length> [pad_char=' ']`
	pad at start

- `paragraphs <string>`
	to paragraph list

- `position <string> <substring> [start]`
	index of substring, or None. search from [start]

- `red <string>`
	red fg

- `repeat <string> <count>`
	repeat n times

- `replace <string> <old> <new>`
	replace all matches

- `rev <string>`
	reverse

- `slice <string> <start> [end]`
	substring [start,end)

- `sort <string> ['+'|'-'|key_fn]`
	sort lines

- `split <string> [delimiter]`
	split by delimiter/whitespace

- `split_at <string> <index>`
	split at index

- `starts_with <string> <substring>`
	starts with?

- `strike <string>`
	strikethrough

- `strip_ansi <string>`
	remove ANSI codes

- `strip_prefix <string> <prefix>`
	remove prefix

- `strip_suffix <string> <suffix>`
	remove suffix

- `title <string>`
	to title case

- `to_filesize <size_str>`
	to filesize. e.g. 1.5GB, 500K

- `to_float <value>`
	to float. % as /100, _ as sep. e.g. 12.5%

- `to_int <value>`
	to int. radix ok(0x/0o/0b), _ as sep. e.g. 0xff_80

- `to_safe <str>`
	wrap str, never eval

- `to_table <output> [regex] [headers...]`
	parse cmd output to table

- `to_time <str> [fmt]`
	to datetime

- `trim <string>`
	trim both ends

- `trim_end <string>`
	trim end

- `trim_start <string>`
	trim start

- `underline <string>`
	underline

- `unescape <string>`
	reverse of escape, parses \n \t \xNN \uXXXX

- `upper <string>`
	to uppercase

- `white <string>`
	white fg

- `words <string>`
	to word list

- `words_quoted <string>`
	to word list, quoted as one

- `wrap <string> <width>`
	wrap to width

- `yellow <string>`
	yellow fg

