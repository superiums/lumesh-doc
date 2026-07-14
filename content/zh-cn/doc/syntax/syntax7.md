---
title: 模块导入
date: 2025-06-11 19:16:45
highlight: true
weight: 90
tags:
 - syntax
categories:
 - wiki
 - syntax
---

## 模块导入
当需要多个脚本文件协同完成复杂任务时，可导入其他模块。
语法：
  ```bash
  # 导入
  use <module_path>
  use <module_path> as <name>
  # 使用
  <module_name>::<func_name>
  ```

**请注意**

- 模块应作为工具函数使用：
    导入模块时，只会读取`fn`定义和`use`语句，函数定义之外的语句将被忽略。以避免意外的代码运行。

**查找路径**
`use mymod`

["./mods/mymod.lm",

 "./mods/mymod/main.lm",

 "./mymod.lm",

 "./mymod/main.lm",

 "~/.local/share/lumesh/mods/mymod.lm",

 "~/.local/share/lumesh/mods/mymod/main.lm"]


## 其他导入方式
- import 语句
在新环境执行脚本
  ```bash
  # 导入
  import <script_path>
  ```
- include 语句
在当前环境执行脚本
  ```bash
  # 导入
  include <script_path>
  ```
  
这两种方式都会执行脚本，而`use`方式不会执行脚本，只引入func。
多数情况下，建议只使用`use`方式。
