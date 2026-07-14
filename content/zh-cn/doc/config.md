---
title: 配置文件
date: 2025-06-11 19:16:45
highlight: True
weight: 3
tags:
 - syntax
categories:
 - wiki
 - syntax
---

## 配置文件路径
- 查看配置文件路径
键入`About` 查看其中的prelude项。
或`:About.prelude`。

- 默认路径:

如果没有在命令行指定配置文件路径，lume会读取默认配置文件，在默认路径下的子路径 `lumesh/config.lm`.

|平台 | 路径                                 | 举例                                  |
| ------- | ------------------------------------- | ---------------------------------------- |
| Linux   | `$XDG_CONFIG_HOME` or `$HOME`/.config | /home/alice/.config                      |
| macOS   | `$HOME`/Library/Application Support   | /Users/Alice/Library/Application Support |
| Windows | `{FOLDERID_RoamingAppData}`           | C:\Users\Alice\AppData\Roaming           |

执行 `fs.dirs()` 可以查看您的默认路径。


## 历史记录路径
如果没有在配置文件中指定，将被保存在默认路径下。

linux/macos平台的文件名为 `.lume_history`.
windows平台的文件名为 `lume_history.log`.

默认路径 :

|平台 | 路径                               | 举例                      |
| ------- | ----------------------------------- | ---------------------------- |
| Linux   | `$XDG_CACHE_HOME` or `$HOME`/.cache | /home/alice/.cache           |
| macOS   | `$HOME`/Library/Caches              | /Users/Alice/Library/Caches  |
| Windows | `{FOLDERID_LocalAppData}`           | C:\Users\Alice\AppData\Local |

执行 `Into.dirs()` 可以查看您的默认路径。


## 配置项

 1. 支持对不同的模式分别配置。
 通过检查`IS_LOGIN`，`IS_INTERACTIVE`实现对不同模式使用不同的配置。

 2. AI接口配置。
 ```bash
 # ====== default AI Helper settings. following is default.
 let LUME_AI_CONFIG = {
     host: "localhost:11434",
     complete_url: "/completion",
     chat_url: "/v1/chat/completions",
     complete_max_tokens: 10,
     chat_max_tokens: 100,
     model: "",
     system_prompt: "you're a lumesh shell script helper",
 }
 ```
 3. 快捷键绑定命令配置。
 ```bash
 # ====== key bindings
 # NONE:0, SHIFT:2, ALT:4, CTRL:8,
 # ALT_SHIFT:6, CTRL_SHIFT: 10, CTRL_ALT:12, CTRL_ALT_SHIFT:14
 let LUME_HOT_MODIFIER = 4
 let LUME_HOT_KEYS = {
     q: "exit",
     c: "clear",
     h: "fs.read ~/.cache/.lume_history | string.lines() | ui.pick('select history:') ?! | exec_str()",
     x: "fs.read ~/.cache/bookmark | string.lines() | ui.pick('select bookmark:') ?! | exec_str()",
     m: 'let cmd := "$CMD_CURRENT";let s = Into.str(cmd); if s {s+"\n" >> /tmp/bookmark;println "\t[MARKED]"}',
 }
 ```
 4. 命令缩写配置。
 ```bash
 # ====== abbreviations
 let LUME_ABBREVIATIONS = {
     xi: 'doas pacman -S',
     xup: 'doas pacman -Syu',
     xq: 'pacman -Q',
     xs: 'pacman -Ss',
     xr: 'doas pacman -Rs',
 }
 ```
 5. 命令别名配置。
 ```bash
 # ====== alias
 alias int = Into.int()
 alias str = Into.str()
 alias each = list.map()
 alias sort = list.sort()
 alias group = list.group()
 alias table = Into.table()
 alias format = string.format()
 alias ll = fs.ls -l
 alias lsx = ls -l --time-style=long-iso
 alias join = list.join()
 alias chars = string.chars()
 alias open = fs.read()
 ```
 6. 历史记录路径配置。
 ```bash
 # ====== history file
 let LUME_HISTORY_FILE = "/tmp/lume_histroy"

 ```
 7. prompt配置。
    ```bash
        # MODE: 1=use template; 2=use starship; 0=use default.
        let LUME_PROMPT_SETTINGS = {
            MODE: 1,
            TTL_SECS: 2
        }
    ```
    命令行提示符，可以工作在如下模式：
    - 0，默认模式
    - 1, 模板模式, 模板可以是普通表达式，或函数。

      TTL用于控制缓存更新频率，以秒为单位。

      通常，普通表达式更节省资源；
      ```bash
      # 模板支持如下变量: $CWD, $CWD_SHORT
      let LUME_PROMPT_TEMPLATE = (string.blue($CWD_SHORT) + string.yellow(string.bold(">> ")))
      ```
      函数则可以用来渲染更复杂的提示符，比如git分支显示。函数模板会比普通模板消耗稍微多一点的资源。

      ```bash
      let LUME_PROMPT_TEMPLATE := x -> {
          string.format "{} {}{}{} " string.blue(x) string.green(string.bold("|")) \
          (if (Into.exists '.git') {git branch --show-current | string.cyan()} else "") \
          string.green(string.bold(">"))
      }

      ```
    - 2, starship 模式

 8. 欢迎信息
 ```bash
 # ====== welcome msg
 let LUME_WELCOME = "Welcome to Lumesh!"
 ```

 9. 启用VI模式
 ```bash
 LUME_VI_MODE = True
 ```

 10. 启用严格模式
 ```bash
    # ====== default strict mode
    let STRICT = True
 ```
 该设置具有最低优先级，可能被命令参数`-s`覆盖，或脚本指定模式覆盖。

 11. 控制直接打印模式
 ```bash
    # ====== default strict mode
    let STRICT = True
 ```
 该模式控制是否直接打印算术通道的运算结果和类型。默认为启用。例如，输入`5`回车后您将看到：
  ```bash
  >> [Integer] <<
  5
  ```

 12. sudo命令补全
 ```bash
    # ====== default strict mode
    let LUME_SUDO_CMD = "doas"
 ```
 按下`Alt+s`会自动添加sudo命令，如果您使用`doas`或其他sudo命令，可配置此项。默认值为`sudo`

 13. 配置`PATH`环境变量
 ```bash
 PATH = "~/.local/bin:" + $PATH
 ```

 14. 配置`IFS`
 ```bash
 IFS = "\n"
 # IFS affect: 0:never; 2:cmd args; 4:for;  8:string.split; 16:csv; 32:pick; 62:all
 let LUME_IFS_MODE=2
 ```
 后者用于细化控制IFS在哪些地方启用。

 15. 配置全局模块路径
 ```bash
 let LUME_MODULES_PATH=/opt/mods
 ```
当导入模块时，会优先从当前路径的`./mods/`,`./`下搜寻，最后从全局路径搜寻。

 16. 配置最大循环深度
 ```bash
 let LUME_MAX_SYNTAX_RECURSION = 100   # 语法嵌套深度
 let LUME_MAX_RUNTIME_RECURSION = 800  # 执行深度
 ```
 该设置不会影响循环语句的执行。仅当您看到系统提示需要增加深度时才需要设置。

 13. 高亮主题配置
 ```bash
 # ====== base theme: 'one_dark', 'ayu_dark', 'light'
 LUME_THEME = 'ayu_dark'

 # ====== theme modify
 LUME_THEME_CONFIG = {
    keyword: "\x1b[38;5;170m",      # 紫色 (#C678DD)
    #    ...
 }

 ```
 `LUME_THEME`用于设置基础主题；

 `LUME_THEME_CONFIG` 用于对基础主题做修改，其中
 可配置的项有：
 ```bash
 # One Dark 核心语法颜色 - 每种都使用不同的颜色
  keyword,
  value_symbol,
  operator,
  operator_prefix,
  operator_infix,
  operator_postfix,

  # 字符串相关颜色
  string_raw,
  string_template,
  string_literal,
  string_error,

  # 数字和字面量
  number_literal,
  number_error,
  integer_literal,
  float_literal,

  # 符号和标识符
  symbol_none,
  builtin_cmd,
  symbol,

  # 注释和标点
  comment,
  punctuation,

  # REPL 和交互相关
  command_valid,
  hint,
  completion_cmd,
  completion_ai,

  # Time token 颜色
  time,

  # Regex token 颜色
  regex,
 ```

详细配置项可查看默认配置。
