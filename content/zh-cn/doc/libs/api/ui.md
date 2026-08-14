---
title: 内置库 ui
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: ui

- `confirm <msg>`
	询问是/否

- `date_pick [msg|cfg_map]`
	选择一个日期。
	cfg: {msg,starting_date,min_date,max_date,week_start,formatter,validator}

- `editor [msg|cfg_map]`
	为多行文本打开外部编辑器。
	cfg: {msg,predefined_text,editor_command,validators...}

- `float <msg> [decimal_places=2]`
	读取浮点数

- `int <msg>`
	读取整数

- `join_flow <max_width> <widgets...>`
	将小部件流动换行到行中

- `joinx <widget1> <widget2>`
	水平连接两个小部件

- `joiny <widget1> <widget2>`
	垂直堆叠两个小部件

- `multi_pick <options> [msg|cfg_map]`
	多选，选项/配置与 pick 相同。
	cfg 新增: {all_selected_by_default,keep_filter,validator}

- `password <msg> [confirm=false]`
	读取密码，已屏蔽输入

- `pick <options> [msg|cfg_map]`
	从列表/集合/范围/映射/表格/通配符/字符串中选择一个。
	cfg: {msg,page_size,vim_mode,formatter,scorer,sorter...}

- `text <msg> [init_value]`
	读取文本

- `widget <content> <title> [width] [height]`
	绘制带边框的文本框
