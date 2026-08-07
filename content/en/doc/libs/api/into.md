---
title: Builtin Lib INTO
date: 2026-08-07 12:34:52
---
	
## Builtin Functions for Lib: into

- boolean <value>
	to bool

- caesar <string> [shift=13]
	caesar cipher

- csv <expr>
	to CSV

- filesize <size_str|int>
	to filesize. e.g. 1.5GB, 500K

- float <str|num|bool>
	to float. % as /100, _ as sep. e.g. 12.5% -> 0.125

- highlight <script>
	ANSI highlight script

- int <str|num|bool>
	to int. radix ok(0x/0o/0b), _ as sep. e.g. 0xff_80

- json <expr>
	to JSON

- pretty <expr>
	to pretty string

- safe <str>
	wrap str, never eval

- string <value>
	to string

- strip <string>
	remove ANSI codes

- table <output> [split_regex] [headers...]
	parse cmd output to table

- time <str> [fmt]
	to datetime

- toml <expr>
	to TOML

