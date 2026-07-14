---
title: 脚本编写
date: 2025-06-11 19:16:45
highlight: true
weight: 70
tags:
 - syntax
categories:
 - wiki
 - syntax
---

## 解释器声明
脚本文件的首行，一般书写解释器声明。
建议的shebang是`#!/usr/bin/en lumesh`

> 下载的二进制文件有两个：`lume`或`lume-se`,您可以根据爱好链接其中一个到`lumesh`
```bash
ln -sf /usr/bin/lume /usr/bin/lumesh     # 或
ln -sf /usr/bin/lume-se /usr/bin/lumesh
```

> 无shebang行的脚本只能通过 `lume my.lm`运行
> 有shebang行的脚本可直接通过`my.lm` 运行

## 扩展名
建议的扩展名是`.lm`

## 示例
```bash
#!/usr/bin/en lumesh

fn add(msg, *salaries){
    println msg.green()
    println salaries.sum()
}

add('wang fang', 4500, 5000, 6100)

if argv.len() {
    println 'Your args:' argv
}
```
