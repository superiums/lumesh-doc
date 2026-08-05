---
title: 快速开始
date: 2026-03-16 16:16:00
highlight: true
weight: 1
tags:
 - install
categories:
 - wiki
 - install
---

# Lumesh 用户教程 快速开始


### 1 安装

一键安装：
```bash
curl -fsSL https://bun.sh/install | bash
```

### 2 启动
安装完成后，启动交互式 shell：
```bash
lume          # 完整交互式
```

### 3 交互命令

像bash一样，你可以尝试键入日常命令：
```bash
ls -l
cd /tmp
thunar &
# 看起来和bash一样，但多了一些东西，比如语法高亮

jobs
fs.ls -lh | where(size>5K)
# 好像和bash有点不一样了，支持结构化管道

ls --       #按下<Tab>
# 自动补全也有哦

3+18/6
0b100 + 0b101    # 二进制也不在话下
# 数学运算也比bash简单多了

0...10 |> _ + 100
# 循环派发，真方便

0..8 | ui.pick()
# 什么？还可以选择？

let a = 0..8
if a ~: 5 {
    print 'include'
}
# 多行编辑也支持呢

list.from(a).sum()
# 还能链式调用？比管道还方便！

5/0 ?: 0
# 错误处理太自然啦！

'lume'.upper().green()
help
help libs
help string
# 内置库丰富噢

help doc
# 在线文档在这里噢

```

你已经找到家了，还有更多惊喜等你发现噢，比如`/命令`，比如`可编程热键`，比如`ai提示和生成`...
