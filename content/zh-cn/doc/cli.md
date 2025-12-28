---
title: 交互命令
date: 2025-12-11 19:16:45
highlight: true
weight: 2
tags:
 - install
categories:
 - wiki
 - install
---

命令行支持多种执行模式，如同您看到的：

```shell
Lumesh scripting language runtime

Usage: lume [OPTIONS] [FILE_N_ARGS]... [-- [CMD_ARGV]...]

Arguments:
  [FILE_N_ARGS]...  script file and args to execute
  [CMD_ARGV]...     args for cmd

Options:
  -p, --profile      config file
  -s, --strict       strict mode
  -i, --interactive  force interactive mode
  -m, --cfmoff       NO command first mode
  -a, --aioff        NO ai mode
  -n, --nohistory    NO history (private) mode
  -c, --cmd <CMD>    command to eval
  -h, --help         Print help
  -V, --version      Print version
```

## 交互模式

- 严格模式(s)
变量必须定义；
变量不能重复定义；
变量使用必须加`$`前缀；

- 交互模式(i)
无论是否指定了cmd，都进入交互模式，让用户可以键入命令。

- 命令优先模式(默认)
在交互命令模式中，如果输入只有一行，则使用命令优先模式。
此模式下，优先解析为命令而非编程，如`.` `+` 等将作为普通字符处理。例如
`ping 1.1.1.1` `chmod +x a.b` 都被解析为命令而无须添加引号。
要跳过此模式，有两种方法：
  1. 在启动lume时添加`-m`选项；
  2. 在命令行首添加`:`
  
- 无AI模式
- 无历史记录模式

### 参数传递
`lume -c cmd -- arg1 -arg2 --arg3`
`lume -c cmd arg1 -arg2 --arg3`

### 自动完成
按`Tab`键

## 运行脚本
`lume script.lm arg1 -arg2 --arg3`

`script.lm arg1 -arg2 --arg3`  需要shebang行和可执行权限(chmod +x)
