---
title: Lumesh console 库
date: 2025-06-11 19:16:45
highlight: true
tags:
 - libs
 - console
categories:
 - wiki
 - libs
---

# Console 模块

Console 模块提供终端控制功能，包括终端信息查询、输出控制、模式切换、光标控制和输入读取。

---

## Console Information（终端信息）

| 函数 | 描述 | 参数 | 返回值 |
|------|------|------|--------|
| `width` | 获取终端宽度 | 无 | 整数（列数）或 `none` |
| `height` | 获取终端高度 | 无 | 整数（行数）或 `none` |

```bash
console.width()    # 返回终端列数
console.height()   # 返回终端行数
```

---

## Output Control（输出控制）

| 函数 | 描述 | 参数 | 返回值 |
|------|------|------|--------|
| `write` | 在指定位置写入文本 | `<text> <x> <y>` | `none` |
| `title` | 设置终端标题 | `<string>` | `none` |
| `clear` | 清屏 | 无 | `none` |
| `flush` | 刷新输出缓冲区 | 无 | `none` |
| `print_tty` | 向 tty 输出控制序列 | `<arg>` | `none` |
| `discard` | 丢弃数据（类似 /dev/null） | `<arg>` | `none` |

```bash
# write - text 在前，适应管道符操作
"Hello" | console.write 10 5    # 在 (10,5) 位置写入 "Hello"

console.title "My Terminal"     # 设置终端标题
console.clear()                   # 清屏
console.flush()                   # 刷新缓冲区

console.print_tty() "\x1b[31m"    # 向 tty 输出红色控制序列
data | console.discard()          # 丢弃数据
```

---

## Mode Control（模式控制）

| 函数 | 描述 | 参数 | 返回值 |
|------|------|------|--------|
| `mode_raw` | 启用 raw 模式 | 无 | `none` |
| `mode_normal` | 禁用 raw 模式 | 无 | `none` |
| `screen_alternate` | 启用备用屏幕 | 无 | `none` |
| `screen_normal` | 禁用备用屏幕 | 无 | `none` |

```bash
console.mode_raw()           # 启用 raw 模式（禁用行缓冲、回显）
console.mode_normal()        # 恢复正常模式
console.screen_alternate()   # 切换到备用屏幕（如 vim、htop）
console.screen_normal()      # 返回主屏幕
```

---

## Cursor Control（光标控制）

| 函数 | 描述 | 参数 | 返回值 |
|------|------|------|--------|
| `cursor_to` | 移动光标到指定位置 | `<x> <y>` | `none` |
| `cursor_up` | 光标向上移动 | `<n>` | `none` |
| `cursor_down` | 光标向下移动 | `<n>` | `none` |
| `cursor_left` | 光标向左移动 | `<n>` | `none` |
| `cursor_right` | 光标向右移动 | `<n>` | `none` |
| `cursor_save` | 保存光标位置 | 无 | `none` |
| `cursor_restore` | 恢复光标位置 | 无 | `none` |
| `cursor_hide` | 隐藏光标 | 无 | `none` |
| `cursor_show` | 显示光标 | 无 | `none` |

```bash
console.cursor_to 10 5      # 移动光标到 (10,5)
console.cursor_up 3         # 光标向上移动 3 行
console.cursor_down 2       # 光标向下移动 2 行
console.cursor_left 5       # 光标向左移动 5 列
console.cursor_right 1      # 光标向右移动 1 列

console.cursor_save()         # 保存当前位置
console.cursor_restore()      # 恢复到保存的位置
console.cursor_hide()         # 隐藏光标
console.cursor_show()         # 显示光标
```

---

## Input Control（输入控制）

| 函数 | 描述 | 参数 | 返回值 |
|------|------|------|--------|
| `read_line` | 从键盘读取一行 | `[prompt]` | 字符串 |
| `read_password` | 从键盘读取密码（不回显） | `[prompt]` | 字符串 |
| `read_key` | 从键盘读取单个按键 | 无 | 按键名称字符串 |
| `keys` | 列出所有可识别的特殊键名 | 无 | 名称列表 |

```bash
console.read_line "Enter name: "    # 带提示符读取
console.read_line()                   # 无提示符读取

console.read_password "Password: "  # 带提示符读取密码
console.read_password()               # 无提示符读取密码

console.read_key()                    # 读取单个按键，返回名称如 "left", "enter", "a"
console.keys()                        # 返回 ["enter", "backspace", "left", ...]
```

**`read_key` 返回的按键名称**：`enter`, `backspace`, `delete`, `left`, `right`, `up`, `down`, `home`, `end`, `page_up`, `page_down`, `tab`, `esc`, `insert`, `f1`–`f12`, `null`, `back_tab`，以及普通字符本身（如 `"a"`, `"A"`）。 

---

## Notes

- `write` 函数的参数顺序为 `<text> <x> <y>`，text 在前是为了适应管道符操作（`"text" | console.write() x y`）
- `read_key` 会忽略鼠标移动、窗口 resize 等非 Key 事件，持续等待直到收到按键事件  
- `print_tty` 和 `discard` 是从 `sys` 模块移至 `console` 模块的终端控制函数  
- 所有光标移动和模式切换函数使用 crossterm 库，自动处理跨平台差异
- 坐标系统使用 0-indexed（左上角为 0,0）
