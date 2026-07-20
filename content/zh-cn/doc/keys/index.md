---
title: 按键绑定
date: 2026-07-05 09:36:45
highlight: true
weight: 50
tags:
 - hotkey
 - bindings
 - slash cmd
categories:
 - wiki
 - hotkey
---

## 快捷键绑定总览
![hot-key](images/key_cn.svg)

### 默认 Emacs 风格绑定

#### 字符操作
快捷键	功能

- Ctrl+A	移动到段首
- Ctrl+E	移动到段尾
- Ctrl+B	左移一个字符
- Ctrl+F	右移一个字符
- Ctrl+H	删除前一个字符
- Ctrl+W	删除前一个词
- Alt+D	删除后一个词
- Ctrl+U	删除到行首
- Ctrl+K	删除到行尾
- Ctrl+L	清屏
- Ctrl+N	补全选择：下一条
- Ctrl+P	补全选择：上一条
- Ctrl+T	交换字符
- Ctrl+Y	粘贴
- Alt+B	左移一个词
- Alt+F	右移一个词

#### 功能操作
快捷键	功能
- Ctrl+C	中断（SIGINT）
- Ctrl+D	EOF（空行时）/删除字符
- Tab	触发补全
- Ctrl+R	历史搜索
- BackTab	反向补全
- Escape	取消补全/退出多行模式
- Enter	接受行/换行

### Lumesh特色功能绑定
- Alt+S	切换 sudo 命令（可配置为doas等）
- Space	触发缩写展开
- **Alt+O** / **Alt+Enter** | **AI 替换**
- **Alt+I** | **AI 提示**

#### Hint操作：
- Ctrl+J	接受提示的第一个词（到特殊字符）
- Alt+J	接受提示的第一个词（到空格或斜杠）

### 补全模式绑定
当补全列表显示时进入补全选择模式：

快捷键	功能
- Up / Ctrl+P	上一个补全项
- Down / Ctrl+N / Tab	下一个补全项
- BackTab	上一个补全项
- Enter / Space	接受当前补全
- Escape	取消补全
- 字符键	继续输入并过滤补全(支持fuzzy模糊搜索)
- Backspace	删除字符并过滤补全


### 历史搜索模式绑定
快捷键	功能
Ctrl+R	触发历史搜索

补全模式下的快捷键在历史搜索模式下均可用。

#### 多行编辑模式绑定

当输入包含换行符时进入多行编辑模式：

快捷键	功能
- Up	上移一行
- Down	下移一行
- Home	移动到当前行首
- End	移动到当前行尾
- Escape	退出多行模式
- Enter	在空行时接受行，否则换行并保持缩进

- Ctrl+A	移动到段首
- Ctrl+E	移动到段尾
- **Ctrl+O**  清空输入


### 自定义编辑器绑定

自定义热键
> 热键通过 LUME_HOT_BINDINGS 配置：

快捷键	功能
- Alt+q	退出 shell
- Alt+h	历史选择器
- Alt+x	书签选择器
- Alt+m	标记当前命令到书签
- Alt+e	在外部编辑器中编辑命令

更多参看配置文件...
> 配置中，每一个按键对应一个函数，该函数必须有一个形参，以接收当前行的已输入内容。返回值如果是字符串，将被用于替换当前输入行。

### 缩写展开
输入缩写后按空格触发展开：

> 缩写展开通过 LUME_ABBREVIATIONS 来自定义。

例如：

- xi	doas pacman -S
- xup	doas pacman -Syu
- xq	pacman -Q
- xs	pacman -Ss
- xr	doas pacman -Rs

### /命令


以下是内置的/命令

不接收参数：
- `/` 菜单，会启动 `LUME_SLASH_MENU` 定义的函数
- `/q` 退出

接收一个参数
- `/ <some_query>` 快速模糊目录跳转，类似zoxide的z跳转
- `/history [prefix]` 打印历史记录（按输入顺序）
- `/h [prefix]` 选择历史命令并执行（按权重顺序）
- `/hh [prefix]` 选择当前目录专属的历史命令并执行
- `/hm [prefix]` 选择多目录适用的历史命令并执行

> /命令是在回车后执行，因此，可以接收一个字符串参数，返回值将被丢弃。

> 自定义命令可通过 LUME_SLASH_BINDINGS 配置
> 每一个绑定对应一个函数，该函数必须有一个形参，返回值将被忽略。
