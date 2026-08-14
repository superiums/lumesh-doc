---
title: 顶层内置库
date: 2026-08-14 11:29:46
---

## 顶层内置函数


- `assert <expr> [expr] [message]`
	如果不相等或为假则抛出错误

- `cd [path=~]`
	切换目录。'-' 表示上一个目录

- `cwd `
	当前目录

- `ddebug <args>...`
	求值并显示表达式、类型、值（美化格式）

- `debug <args>...`
	求值并显示表达式、类型、值（调试格式）

- `dig <map|list|set|range|table> <path>`
	通过点路径获取嵌套值。例如 dig m 'a.b.0'

- `eprint <args>...`
	打印到标准错误，红色，无换行

- `eprintln <args>...`
	打印到标准错误，红色，带换行

- `eval <expr>`
	在当前环境中求值表达式

- `eval_str <string>`
	在当前环境中解析并求值字符串

- `exec <expr>`
	在派生环境中求值表达式

- `exec_str <string>`
	在派生环境中解析并求值字符串

- `exit [status=0]`
	退出 shell

- `flatten <collection>`
	将嵌套列表/映射扁平化为平铺列表

- `format <template> <args>...`
	格式化字符串。{name}/{} 用于命名/位置参数，:spec 用于对齐。
	例如 format '{:0>5}' 3 -> 00003

- `get_env <var>`
	从环境获取变量

- `get_local <var>`
	获取局部变量值

- `get_var <var>`
	获取变量，先查局部再查环境

- `help [libs|tops|doc|<lib>|<lib>.<func>|<top_func>]`
	显示帮助。例如 help string.split

- `import <path>`
	在派生环境中求值文件

- `include <path>`
	在当前环境中求值文件

- `is_empty <expr>`
	是否为空？

- `jobs [-k id]`
	列出/终止作业

- `len <list|set|map|table|range|string|bytes>`
	集合大小

- `not <boolean>...`
	逻辑非

- `pprint <value>...`
	美化打印表格/列表/映射

- `print <args>...`
	打印，空格分隔，无换行

- `println <args>...`
	打印，空格分隔，带换行

- `quote <expr>`
	引用表达式，稍后求值

- `read [-p prompt] [-n max_chars] [-s silent] [-t timeout_secs]`
	读取输入

- `repeat <expr> <n>`
	求值表达式 n 次，收集非 None 结果

- `rev <string|list|table|bytes>`
	反转

- `select <table> <columns...>`
	从表格选择列

- `set_root <var> <val>`
	在根环境中定义变量

- `symof <value>`
	求值前显示类型名称

- `tap <args>...`
	打印然后返回值

- `throw <msg>`
	抛出运行时错误

- `typeof <value>`
	求值后显示类型名称

- `unset_root <var>`
	在根环境中取消定义变量

- `when <condition> <execute>`
	条件执行

- `where <table> <condition>`
	过滤表格行。注入了 `NR/<列名>`。例如 `where t (NR>1 and col>0)`
