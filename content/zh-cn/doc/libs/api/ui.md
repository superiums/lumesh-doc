---
title: 内置库 ui
date: 2026-08-07 16:42:00
---
	
## Builtin Functions for Lib: ui

- `confirm <msg>`
	ask yes/no

- `date_pick [msg|cfg_map]`
	pick a date.
	cfg: {msg,starting_date,min_date,max_date,week_start,formatter,validator}

- `editor [msg|cfg_map]`
	open external editor for multiline text.
	cfg: {msg,predefined_text,editor_command,validators...}

- `float <msg> [decimal_places=2]`
	read a float

- `int <msg>`
	read an int

- `join_flow <max_width> <widgets...>`
	flow-wrap widgets into rows

- `joinx <widget1> <widget2>`
	join two widgets side by side

- `joiny <widget1> <widget2>`
	stack two widgets vertically

- `multi_pick <options> [msg|cfg_map]`
	select multi, same options/cfg as pick.
	cfg Adds: {all_selected_by_default,keep_filter,validator}

- `password <msg> [confirm=false]`
	read password, masked

- `pick <options> [msg|cfg_map]`
	select one from list/set/range/map/table/glob/string.
	cfg: {msg,page_size,vim_mode,formatter,scorer,sorter...}

- `text <msg> [init_value]`
	read text

- `widget <content> <title> [width] [height]`
	draw a bordered text box

