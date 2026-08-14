---
title: 内置库 sys
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: sys

- `defined <var>`
	在作用域链中定义？

- `dirs `
	系统目录映射

- `env [var]`
	根环境映射，或变量值

- `error_codes `
	列出 lume 错误代码

- `has <var>`
	在当前作用域中定义？

- `info `
	操作系统信息

- `max_runtime [depth]`
	获取/设置最大运行时递归深度

- `max_syntax [depth]`
	获取/设置最大语法递归深度

- `max_usemode [depth]`
	获取/设置最大使用模式递归深度

- `modes `
	当前模式标志 {cfm,strict,pdm}

- `set_cfm <boolean|none>`
	设置命令优先模式

- `set_pdm <boolean>`
	启用/禁用直接打印模式

- `set_strict <boolean>`
	启用/禁用严格模式

- `vars `
	当前作用域中定义的变量
