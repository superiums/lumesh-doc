---
title: 内置库 console
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: console

- `alt_screen <bool>`
	进入/离开备选屏幕

- `bell `
	发出终端响铃

- `clear `
	清空控制台

- `cursor_down <n>`
	将光标向下移动 n 行

- `cursor_hide `
	隐藏光标

- `cursor_left <n>`
	将光标向左移动 n 列

- `cursor_restore `
	恢复光标位置

- `cursor_right <n>`
	将光标向右移动 n 列

- `cursor_save `
	保存光标位置

- `cursor_show `
	显示光标

- `cursor_to <x> <y>`
	将光标移动到指定位置

- `cursor_up <n>`
	将光标向上移动 n 行

- `discard <args>...`
	无操作，丢弃参数

- `flush `
	刷新 stdout

- `height `
	控制台高度

- `keys `
	列出特殊键名

- `line_wrap <bool>`
	启用/禁用自动换行

- `print_tty <text>`
	直接将原始文本写入 tty，绕过管道

- `raw_mode [bool]`
	获取/设置原始模式

- `read_key `
	读取一个按键，临时进入原始模式。例如 'enter','f1','a'

- `read_line [prompt]`
	从标准输入读取一行

- `read_password [prompt]`
	读取密码，已屏蔽输入

- `title <string>`
	设置控制台标题

- `width `
	控制台宽度

- `write <text> <x> <y>`
	在指定位置写入文本
