---
title: "Syntax: Module Import"
date: 2025-06-11 19:16:45
highlight: true
weight: 90
tags:
 - syntax
categories:
 - wiki
 - syntax
---

## Module Import
When multiple script files need to work together to complete complex tasks, you can import other modules.
Syntax:
  ```bash
  # import
  use <module_path>
  use <module_path> as <name>
  # use
  <module_name>::<func_name>
  ```

**Please Note**

- Modules should be used as utility functions:
    When importing a module, only `fn` definitions and `use` statements are read; statements outside function definitions are ignored. To avoid accidental code execution.

**Search Path**
`use mymod`

["./mods/mymod.lm",

 "./mods/mymod/main.lm",

 "./mymod.lm",

 "./mymod/main.lm",

 "~/.local/share/lumesh/mods/mymod.lm",

 "~/.local/share/lumesh/mods/mymod/main.lm"]


## Other Import Methods
- import statement
Executes script in new environment
  ```bash
  # import
  import <script_path>
  ```
- include statement
Executes script in current environment
  ```bash
  # import
  include <script_path>
  ```

Both of these execute the script, while the `use` method does not execute the script, only imports functions.
In most cases, it's recommended to only use the `use` method.
