---
title: Lumesh 快捷键
date: 2025-06-11 19:16:45
highlight: true
weight: 50
tags:
 - hotkey
categories:
 - wiki
 - hotkey
---

默认绑定:

- Ctrl+ J     - 接受建议
- Alt + J     - 接受单个建议单词
- Ctrl+ O     - 清空多行输入
- Alt + S     - 插入sudo

另外, 自定义快捷键可以通过在配置文件里设置 `LUME_HOT_KEYS` 绑定自定义命令.


| **功能分类**       | **快捷键**                     | **描述**                                      |
|------------------|------------------------------|---------------------------------------------|
| **光标移动**       | Home, Ctrl + A               | 移动光标到行首                                 |
|                  | End, Ctrl + E                | 移动光标到行尾                                 |
|                  | Left, Ctrl + B               | 光标向左移动一个字符                            |
|                  | Right, Ctrl + F              | 光标向右移动一个字符                            |
|                  | Ctrl + I, Tab                | 下一个补全                                    |
|                  | Meta + B, Alt + Left         | 向左移动到前一个单词                           |
|                  | Meta + F, Alt + Right        | 向右移动到下一个单词                           |
| **文本编辑**       | Ctrl + C                      | 中断当前命令                                  |
|                  | Ctrl + D, Del                | 删除光标下的字符（如果行不为空）               |
|                  | Ctrl + H, Backspace          | 删除光标前的字符                               |
|                  | Ctrl + W, Meta + Backspace   | 删除光标前的一个单词                           |
|                  | Meta + D                     | 删除光标之后的一个单词                               |
|                  | Ctrl + U                      | 删除光标之前的所有字符                         |
|                  | Ctrl + K                      | 删除光标之后的所有字符                         |
|                  | Ctrl + Y, Meta + Y           | 粘贴之前删除的内容                             |
| **命令控制**       | Ctrl + Z                      | 挂起当前命令                                  |
|                  | Ctrl + L                      | 清屏                                         |
|                  | Ctrl + R                      | 反向搜索历史命令                              |
|                  | Ctrl + P                      | 上一条命令                                    |
|                  | Ctrl + N                      | 下一条命令                                    |
|                  | Ctrl + M, Enter    | 完成行输入                                    |
|                  | **Ctrl + J**                  | **接受hint建议**                              |
|                  | Meta + <                     | 移动到历史列表的第一项                         |
|                  | Meta + >                     | 移动到历史列表的最后一项                       |
| **撤销与重做**     | Ctrl + _                      | 撤销上一步操作                                 |
|                  | Ctrl + X Ctrl + U            | 撤销上一步操作                                 |
| **特殊功能**       | Ctrl + V                      | 插入特殊字符                                  |
|                  | Meta + C                     | 将当前单词首字母大写                           |
|                  | Meta + L                     | 将当前单词转换为小写                           |
|                  | Meta + U                     | 将当前单词转换为大写                           |
|                  | Ctrl + T                      | 交换光标前后的两个字符                         |
|                  | Meta + T                     | 交换当前单词与前一个单词的位置                |
|                  | Meta + 0, 1, ..., -          | 重复n次接下来的操作，- 负参数表示反向操作             |
