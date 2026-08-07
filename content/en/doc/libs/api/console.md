---
title: Builtin Lib CONSOLE
date: 2026-08-07 12:34:52
---
	
## Builtin Functions for Lib: console

- alt_screen <bool>
	enter/leave alternate screen

- bell 
	ring terminal bell

- clear 
	clear console

- cursor_down <n>
	move cursor down n rows

- cursor_hide 
	hide cursor

- cursor_left <n>
	move cursor left n cols

- cursor_restore 
	restore cursor position

- cursor_right <n>
	move cursor right n cols

- cursor_save 
	save cursor position

- cursor_show 
	show cursor

- cursor_to <x> <y>
	move cursor to position

- cursor_up <n>
	move cursor up n rows

- discard <args>...
	no-op, discards args

- flush 
	flush stdout

- height 
	console height

- keys 
	list special key names

- line_wrap <bool>
	enable/disable line wrap

- print_tty <text>
	write raw text directly to tty, bypass pipes

- raw_mode [bool]
	get/set raw mode

- read_key 
	read one key, enters raw mode temporarily. e.g. 'enter','f1','a'

- read_line [prompt]
	read line from stdin

- read_password [prompt]
	read password, masked

- title <string>
	set console title

- width 
	console width

- write <text> <x> <y>
	write text at position

