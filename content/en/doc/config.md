---
title: Configuration Files
date: 2025-06-11 19:16:45
highlight: true
weight: 3
tags:
 - syntax
categories:
 - wiki
 - syntax
---

## Configuration File Paths
- To view the configuration file path, type `About` and check the prelude item, or use `:About.prelude`.

- **Default Path:**

If no configuration file path is specified in the command line, Lume will read the default configuration file located at `lumesh/config.lm` under the default path.

| Platform | Path                                 | Example                                  |
|----------|--------------------------------------|------------------------------------------|
| Linux    | `$XDG_CONFIG_HOME` or `$HOME/.config` | /home/alice/.config                      |
| macOS    | `$HOME/Library/Application Support`   | /Users/Alice/Library/Application Support |
| Windows  | `{FOLDERID_RoamingAppData}`           | C:\Users\Alice\AppData\Roaming           |

You can execute `fs.dirs()` to view your default paths.


## History File Paths
If not specified in the configuration file, history will be saved in the default path.

On Linux/macOS, the filename is `.lume_history`. On Windows, the filename is `lume_history.log`.

Default paths:

| Platform | Path                               | Example                      |
|----------|------------------------------------|------------------------------|
| Linux    | `$XDG_CACHE_HOME` or `$HOME/.cache` | /home/alice/.cache           |
| macOS    | `$HOME/Library/Caches`              | /Users/Alice/Library/Caches  |
| Windows  | `{FOLDERID_LocalAppData}`           | C:\Users\Alice\AppData\Local |

You can execute `Into.dirs()` to view your default paths.


## Configuration Items

1. Support for configuring different modes separately.
   By checking `IS_LOGIN` and `IS_INTERACTIVE`, different configurations can be applied for different modes.

2. AI Interface Configuration.
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

3. Key Binding Command Configuration.
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

4. Command Abbreviation Configuration.
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

5. Command Alias Configuration.
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

6. History File Path Configuration.
 ```bash
 # ====== history file
 let LUME_HISTORY_FILE = "/tmp/lume_history"
 ```

7. Prompt Configuration.
    ```bash
        # MODE: 1=use template; 2=use starship; 0=use default.
        let LUME_PROMPT_SETTINGS = {
            MODE: 1,
            TTL_SECS: 2
        }
    ```
    The command line prompt can operate in the following modes:
    - 0, default mode
    - 1, template mode, where the template can be a regular expression or function.

      TTL is used to control the cache update frequency, in seconds.

      Generally, regular expressions are more resource-efficient;
      ```bash
      # Templates support the following variables: $CWD, $CWD_SHORT
      let LUME_PROMPT_TEMPLATE = (string.blue($CWD_SHORT) + string.yellow(string.bold(">> ")))
      ```
      Function templates can be used to render more complex prompts, such as displaying the git branch. Function templates consume slightly more resources than regular templates.

      ```bash
      let LUME_PROMPT_TEMPLATE := x -> {
          string.format "{} {}{}{} " string.blue(x) string.green(string.bold("|")) \
          (if (Into.exists '.git') {git branch --show-current | string.cyan()} else "") \
          string.green(string.bold(">"))
      }
      ```

    - 2, starship mode

8. Welcome Message
 ```bash
 # ====== welcome msg
 let LUME_WELCOME = "Welcome to Lumesh!"
 ```

9. Enable VI Mode
 ```bash
 LUME_VI_MODE = True
 ```

10. Enable Strict Mode
 ```bash
    # ====== default strict mode
    let STRICT = True
 ```
 This setting has the lowest priority and may be overridden by the command parameter `-s` or the script-specified mode.

11. Control Direct Print Mode
 ```bash
    # ====== default strict mode
    let STRICT = True
 ```
 This mode controls whether to directly print the results and types of arithmetic channels. It is enabled by default. For example, after entering `5`, you will see:
  ```bash
  >> [Integer] <<
  5
  ```

12. Sudo Command Completion
 ```bash
    # ====== default strict mode
    let LUME_SUDO_CMD = "doas"
 ```
 Pressing `Alt+s` will automatically add the sudo command. If you use `doas` or another sudo command, you can configure this option. The default value is `sudo`.

13. Configure `PATH` Environment Variable
 ```bash
 PATH = "~/.local/bin:" + $PATH
 ```

14. Configure `IFS`
 ```bash
 IFS = "\n"
 # IFS affect: 0:never; 2:cmd args; 4:for;  8:string.split; 16:csv; 32:pick; 62:all
 let LUME_IFS_MODE=2
 ```
 The latter is used to refine control over where IFS is enabled.

15. Configure Global Module Path
 ```bash
 let LUME_MODULES_PATH=/opt/mods
 ```
When importing modules, it will first search in the current path's `./mods/`, then `./`, and finally in the global path.

16. Configure Maximum Recursion Depth
 ```bash
 let LUME_MAX_SYNTAX_RECURSION = 100   # Syntax nesting depth
 let LUME_MAX_RUNTIME_RECURSION = 800  # Execution depth
 ```
 This setting does not affect the execution of loop statements. It should only be set when you see a system prompt indicating that the depth needs to be increased.

17. Highlight Theme Configuration
 ```bash
 # ====== base theme: 'one_dark', 'ayu_dark', 'light'
 LUME_THEME = 'ayu_dark'

 # ====== theme modify
 LUME_THEME_CONFIG = {
    keyword: "\x1b[38;5;170m",      # Purple (#C678DD)
    #    ...
 }

 ```
 `LUME_THEME` is used to set the base theme;

 `LUME_THEME_CONFIG` is used to modify the base theme, where configurable items include:
 ```bash
 # One Dark core syntax colors - each using a different color
  keyword,
  value_symbol,
  operator,
  operator_prefix,
  operator_infix,
  operator_postfix,

  # String-related colors
  string_raw,
  string_template,
  string_literal,
  string_error,

  # Numbers and literals
  number_literal,
  number_error,
  integer_literal,
  float_literal,

  # Symbols and identifiers
  symbol_none,
  builtin_cmd,
  symbol,

  # Comments and punctuation
  comment,
  punctuation,

  # REPL and interaction-related
  command_valid,
  hint,
  completion_cmd,
  completion_ai,

  # Time token colors
  time,

  # Regex token colors
  regex,
 ```
