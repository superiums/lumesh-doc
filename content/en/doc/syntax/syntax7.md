---
title: Module Import
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
When multiple script files need to work together to accomplish complex tasks, other modules can be imported.
Syntax:
  ```bash
  # Import
  use <module_path>
  use <module_path> as <name>
  # Usage
  <module_name>.<func_name>
  ```

**Please Note**
- Module names must be unique:
    All imported modules (including descendant modules) cannot have the same name. If there are duplicates, please rename them using `as name`.

    This is a trade-off between performance and convenience; for higher performance, we have decided to accept this minor inconvenience.

- Modules should be used as utility functions:
    When importing a module, only `fn` definitions and `use` statements will be read; statements outside of function definitions will be ignored to avoid unintended code execution.

## Other Import Methods
- **import Statement**
Execute a script in a new environment:
  ```bash
  # Import
  import <script_path>
  ```
- **include Statement**
Execute a script in the current environment:
  ```bash
  # Import
  include <script_path>
  ```

Both of these methods will execute the script, while the `use` method will not execute the script but only import functions. In most cases, it is recommended to use the `use` method only.