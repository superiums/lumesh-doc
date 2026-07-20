---
title: 配置文件
date: 2025-07-20 15:16:45
highlight: True
weight: 3
tags:
 - syntax
categories:
 - wiki
 - syntax
---

## 配置文件位置

lumesh 启动时自动加载配置文件，查找顺序如下：

1. 命令行参数 `-p <path>` 指定的路径
2. 环境变量 `LUME_PROFILE` 指定的路径
3. 系统配置目录：`$config_dir/lumesh/config.lm`（Linux: `~/.config/lumesh/config.lm`，macOS: `~/Library/Application Support/lumesh/config.lm`）
4. 当前目录下的 `.lume_config`

配置文件是标准的 lumesh 脚本，支持条件判断：

```bash
if IS_LOGIN {
    # 仅登录 shell 执行
}
if IS_INTERACTIVE {
    # 仅交互模式执行
}
# 所有模式均执行
``` 

---

## 变量速查表

| 变量 | 类型 | 默认值 | 作用域 |
|------|------|--------|--------|
| `LUME_KNOCK_VALIDATOR` | Function | 无 | 登录 |
| `LUME_WELCOME` | String | 内置欢迎语 | 交互 |
| `LUME_PROMPT_SETTINGS` | Map | `{MODE:0, TTL_SECS:2}` | 交互 |
| `LUME_PROMPT_TEMPLATE` | String/Lambda | 无 | 交互 |
| `LUME_THEME` | String | `"one_dark"` | 交互 |
| `LUME_THEME_CONFIG` | Map | 无 | 交互 |
| `LUME_STRICT` | Boolean | `false` | 全局 |
| `LUME_CFM` | Boolean | `false` | 全局 |
| `LUME_NO_HISTORY` | Boolean | `false` | 交互 |
| `LUME_HISTORY_FILE` | String | 系统缓存目录 | 交互 |
| `LUME_COMPLETION_DIR` | String | 系统数据目录 | 交互 |
| `LUME_SUDO_CMD` | String | `"sudo"` | 交互 |
| `LUME_HOT_BINDINGS` | Map | 无 | 交互 |
| `LUME_ABBREVIATIONS` | Map | 无 | 交互 |
| `LUME_AI_CONFIG` | Map | 无 | 交互 |
| `LUME_SLASH_BINDINGS` | Map | 无 | 交互 |
| `LUME_SLASH_MENU` | Any | 无 | 交互 |
| `LUME_EDITOR_THEME` | Map | 无 | 交互 |
| `LUME_CONTINUATION_PROMPT` | String | `"... "` | 交互 |
| `LUME_PRINT_DIRECT` | Boolean | `true` | 全局 |
| `LUME_IFS_MODE` | Integer | `2` | 全局 |
| `LUME_MODULES_PATH` | String | 系统数据目录 | 全局 |
| `LUME_MAX_SYNTAX_RECURSION` | Integer | `100` | 全局 |
| `LUME_MAX_RUNTIME_RECURSION` | Integer | `800` | 全局 |

---

## 详细说明

### 外观与提示符

#### `LUME_WELCOME`
- **类型**：String
- **说明**：启动时显示的欢迎消息。若未设置，显示默认的版本欢迎语。

```bash
set LUME_WELCOME = 'Welcome to my shell!'
```

---

#### `LUME_PROMPT_SETTINGS`
- **类型**：Map
- **字段**：
  - `MODE`：提示符模式
    - `0`：默认内置提示符
    - `1`：使用 `LUME_PROMPT_TEMPLATE`
    - `2`：使用 [starship](https://starship.rs/) 提示符
  - `TTL_SECS`：提示符缓存时间（秒），默认 `2`

```bash
set LUME_PROMPT_SETTINGS = {
    MODE: 1,
    TTL_SECS: 2
}
```
---

#### `LUME_PROMPT_TEMPLATE`
- **类型**：String 或 Lambda
- **说明**：当 `LUME_PROMPT_SETTINGS.MODE = 1` 时生效。
  - **字符串模式**：支持以下占位符：
    - `$CWD`：当前完整路径
    - `$CWD_SHORT`：缩短的路径
    - `$CFM_TAG`：CFM 模式标记（`"CFM"` 或空）
    - `$STRICT_TAG`：严格模式标记（`"S"` 或空）
  - **Lambda 模式**：接收 `(dir, ctx)` 两个参数，`ctx` 包含 `ctx.cfm` 和 `ctx.strict` 布尔字段，每次渲染提示符时都会调用。

```bash
# 字符串模板
set LUME_PROMPT_TEMPLATE = '$CWD_SHORT|$CFM_TAG> '

# Lambda 模板（动态，支持 git 分支等）
set LUME_PROMPT_TEMPLATE = (dir, ctx) -> {
    string.blue($dir) + ' |'.green().bold()
    + ($ctx.cfm ? 'CFM'.green() + '|' : '')
    + (if (fs.exists '.git') { git branch --show-current | .cyan() } else '')
    + '> '.green().bold()
}
```
---

#### `LUME_CONTINUATION_PROMPT`
- **类型**：String
- **说明**：多行输入时的续行提示符，默认为 `"... "`。

```bash
set LUME_CONTINUATION_PROMPT = '... '
```

---

#### `LUME_THEME`
- **类型**：String
- **可选值**：`'one_dark'`、`'ayu_dark'`、`'light'`
- **默认值**：`'one_dark'`（未设置时）
- **说明**：语法高亮的基础主题。

```bash
set LUME_THEME = 'ayu_dark'
```
---

#### `LUME_THEME_CONFIG`
- **类型**：Map（键为主题元素名，值为 ANSI 颜色字符串）
- **说明**：在基础主题之上覆盖特定颜色。可覆盖的键包括：

| 键 | 说明 |
|----|------|
| `mode` | 模式提示符（`>` `:`） |
| `keyword` | 关键字 |
| `value_symbol` | 值符号（`$var`） |
| `operator` | 运算符 |
| `operator_prefix` | 前缀运算符 |
| `operator_infix` | 中缀运算符 |
| `operator_postfix` | 后缀运算符 |
| `string_raw` | 原始字符串 |
| `string_template` | 模板字符串 |
| `string_literal` | 字符串字面量 |
| `number_literal` | 数字字面量 |
| `integer_literal` | 整数字面量 |
| `float_literal` | 浮点字面量 |
| `symbol` | 普通符号 |
| `builtin_cmd` | 内置命令 |
| `builtin_lib` | 内置库 |
| `comment` | 注释 |
| `punctuation` | 标点 |
| `command_valid` | 有效命令 |
| `regex` | 正则表达式 |
| `time` | 时间字面量 |

```bash
set LUME_THEME_CONFIG = {
    keyword: "\x1b[38;5;82m",   # 亮绿色
    comment: "\x1b[38;5;244m",  # 灰色
}
```

---

#### `LUME_EDITOR_THEME`
- **类型**：Map
- **说明**：编辑器（自动完成和提示）的颜色主题，与语法高亮主题独立。
可用颜色如下：
```
"reset"
"black"
"dark_grey"
"red"
"dark_red"
"green"
"dark_green"
"yellow"
"dark_yellow"
"blue"
"dark_blue"
"magenta"
"dark_magenta"
"cyan"
"dark_cyan"
"white"
"grey"
```

```bash
set LUME_EDITOR_THEME = {
   hint : "grey",
   completion_bg : "black",
   completion_fg : "dark_yellow",
   completion_selected_bg : "red",
   completion_selected_fg : "white"
}
```
---

### 交互模式

#### `LUME_CFM`
- **类型**：Boolean
- **默认值**：`false`
- **说明**：启用 Command First Mode（命令优先模式），输入行优先解析为外部命令而非表达式。也可通过命令行 `-m` 参数启用。

```bash
set LUME_CFM = true
```

---

### 历史记录

#### `LUME_NO_HISTORY`
- **类型**：Boolean
- **默认值**：`false`
- **说明**：禁用历史记录的读取和写入（私密模式）。也可通过命令行 `-H` 参数启用。

```bash
set LUME_NO_HISTORY = true
```

---

#### `LUME_HISTORY_FILE`
- **类型**：String
- **默认值**：
  `~/.cache/lume/history`
- **说明**：历史记录文件路径。

```bash
set LUME_HISTORY_FILE = '/tmp/lume_history'
```

---

### 补全

#### `LUME_COMPLETION_DIR`
- **类型**：String
- **默认值**：
  - Linux：`/usr/share/lumesh/completions`
  - macOS：`~/Library/Application Support/lumesh/completions`
  - Windows：`C:\Program Files\lumesh\completions`
- **说明**：参数补全数据文件（CSV 格式）所在目录。

```bash
set LUME_COMPLETION_DIR = '~/.local/share/lumesh/completions'
```
---

### 快捷键

#### `LUME_SUDO_CMD`
- **类型**：String
- **默认值**：`"sudo"`
- **说明**：`Alt+s` 快捷键在行首插入的提权命令。

```bash
set LUME_SUDO_CMD = 'doas'
```
---

#### `LUME_ABBREVIATIONS`
- **类型**：Map（键为缩写，值为展开字符串）
- **说明**：输入缩写后按空格自动展开为完整命令。

```bash
set LUME_ABBREVIATIONS = {
    xi:  'doas pacman -S',
    xup: 'doas pacman -Syu',
    xq:  'pacman -Q',
}
```
---

### Slash Commands（斜杠命令）

#### `LUME_SLASH_BINDINGS`
- **类型**：Map（键为斜杠命令名，值为**接收一个参数的** Function或lambda）
- **说明**：自定义斜杠命令（`/xxx`）的绑定。内置斜杠命令（`/history`、`/h`、`/hh`、`/hm`、`/cds`、`/q` 等）不受此变量影响。

```bash
let open_file = (b) -> {fd -t file | ui.pick('select file:') ?! | handlr open -- _}
set LUME_SLASH_BINDINGS = {
    o: open_file,
}
```

---

#### `LUME_SLASH_MENU`
- **类型**：Function (不接收参数)
- **说明**：斜杠命令菜单的数据源，用于 `/` 触发的交互式选择菜单。
```bash
fn LUME_SLASH_MENU(){
    let m = {
        open_file,
        edit_file,
    }
    let ex = { ui.pick $m.keys() 'Fuzzy Execute' ?! | $m.at() }
    if ex {ex('')}
} ?: e -> print e.msg


```
---

#### `LUME_HOT_BINDINGS`
- **类型**：Map
- **说明**：
> Key format: "MODIFIER_key" (order CTRL_ALT_SHIFT: e.g., "CTRL_ALT_k", "CTRL_q", "ALT_h")
> Value type:
>   String -> inserts text into current line
>   lambda/function -> executes immediately with environment access
>         fn receive one arg as current buffer.


与SLASH_BINDINGS类似，但作用于当前输入行结束之前；

键值可以是字符串或函数，函数将在隔离的环境变量中执行，可以访问当前环境变量的副本，但任何env修改不会影响现有环境。

函数同样接收一个参数（用户在当前行已经键入的文本），函数的返回值如果是字符串则被用于替换当前行的输入。

```bash
set LUME_HOT_BINDINGS = {
    CTRL_q: 'exit',
    ALT_m: save_cmdmark,
    'CTRL_/': menu,
}

```


---

### AI 集成

#### `LUME_AI_CONFIG`
- **类型**：Map
- **说明**：配置本地 AI 助手（如 Ollama）。`Alt+i` 触发 AI 提示，`Alt+Enter` / `Alt+o` 触发 AI 生成。

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `host` | String | `"localhost:11434"` | AI 服务地址 |
| `chat_url` | String | `"/v1/chat/completions"` | 对话接口路径 |
| `complete_max_tokens` | Integer | `10` | 补全最大 token 数 |
| `chat_max_tokens` | Integer | `100` | 对话最大 token 数 |
| `model` | String | `""` | 模型名称 |
| `api_key` | String | `""` | 访问密钥 |
| `chat_prompt` | String | `"you're a lumesh shell helper"` | 提示词 |
| `syntax` | String | `""` | lumesh语法规则 |

```bash
let base = fs.dir_name($SCRIPT)
let syntax = fs.read($base + '/syntax.md') + fs.read($base + '/libs.md') ?: ''

set LUME_AI_CONFIG = {
    host: 'localhost:11434',
    complete_url: '/completion',
    chat_url: '/v1/chat/completions',
    complete_max_tokens: 10,
    chat_max_tokens: 100,
    model: 'qwen2.5-coder:7b',
    chat_prompt: "you're a lumesh shell script helper",
    syntax,
}
```

---

### 安全

#### `LUME_KNOCK_VALIDATOR`
- **类型**：Function 或 Lambda（无参数，返回 Boolean）
- **说明**：仅在登录模式（`IS_LOGIN`）下生效。lumesh 启动时调用此函数，若返回 `false` 则立即退出（`exit(1)`）。可用于在服务器环境下的加强验证。

```bash
if IS_LOGIN {
    set LUME_KNOCK_VALIDATOR = () -> {
        let ans = read "Password: "
        ans == 'secret'
    }
}
```

---

### 全局行为

#### `LUME_PRINT_DIRECT`
- **类型**：Boolean
- **默认值**：`true`
- **说明**：是否自动打印表达式的计算结果（类型和值）。设为 `false` 可关闭此行为，适合脚本模式。

```bash
set LUME_PRINT_DIRECT = false
```

---

#### `LUME_IFS_MODE`
- **类型**：Integer（位掩码）
- **默认值**：`2`
- **说明**：控制 `IFS`（内部字段分隔符）在哪些场景下生效。各位含义：

| 位值 | 场景 |
|------|------|
| `2` | 命令字符串参数拆分（cmd str_arg） |
| `4` | `for` 循环迭代字符串 |
| `8` | `string.split` |
| `16` | CSV 解析 |
| `32` | `ui.pick` 字符串分割 |
| `62` | 全部启用 |
| `0` | 全部禁用 |

```bash
# 仅对命令参数生效（默认）
set LUME_IFS_MODE = 2

# 全部启用
set LUME_IFS_MODE = 62
``` [20](#8-19) [21](#8-20) 

---

#### `LUME_MODULES_PATH`
- **类型**：String
- **默认值**：系统数据目录下的 `lumesh/mods`
- **说明**：`use` 语句查找模块的根目录。全局模块应放在这里。单个项目模块可以放在脚本的同级的mods目录下。

```bash
set LUME_MODULES_PATH = '~/.local/share/lumesh/mods'
```

---

#### `LUME_MAX_SYNTAX_RECURSION`
- **类型**：Integer
- **默认值**：`100`
- **说明**：语法解析的最大递归深度。脚本嵌套层次过深时可适当调大。

```bash
set LUME_MAX_SYNTAX_RECURSION = 200
```

---

#### `LUME_MAX_RUNTIME_RECURSION`
- **类型**：Integer
- **默认值**：`800`
- **说明**：运行时（函数调用）的最大递归深度。递归函数层次过深时可适当调大。

```bash
set LUME_MAX_RUNTIME_RECURSION = 1600
``` [22](#8-21) 

---

## 完整配置示例

```bash
# ===== 登录模式 =====
if IS_LOGIN {
    set PATH = '/usr/local/bin:/usr/bin:/bin'
    # 可选：登录验证
    # set LUME_KNOCK_VALIDATOR = () -> { read "Password: " == 'secret' }
}

# ===== 交互模式 =====
if IS_INTERACTIVE {

    # --- 外观 ---
    set LUME_PROMPT_SETTINGS = { MODE: 1, TTL_SECS: 2 }
    set LUME_PROMPT_TEMPLATE = (dir, ctx) -> {
        string.blue($dir)
        + ($ctx.cfm ? ' CFM'.green() : '')
        + '> '.bold()
    }
    LUME_THEME = 'ayu_dark'

    # --- 交互行为 ---
    # set LUME_VI_MODE = true
    # set LUME_STRICT = true
    # set LUME_CFM = true

    # --- 历史 ---
    # set LUME_NO_HISTORY = true
    # set LUME_HISTORY_FILE = '/tmp/lume_history'

    # --- 补全 ---
    set LUME_COMPLETION_CYCLE = false
    # set LUME_COMPLETION_DIR = '~/.local/share/lumesh/completions'

    # --- 快捷键 ---
    set LUME_SUDO_CMD = 'doas'
    # --- 缩写 ---
    set LUME_ABBREVIATIONS = {
        g: 'git',
        gs: 'git status',
    }

    # --- AI ---
    # set LUME_AI_CONFIG = {
    #     host: 'localhost:11434',
    #     model: 'qwen2.5-coder:7b',
    # }
}

# ===== 全局设置 =====
PATH = '~/.local/bin:' + $PATH ?.
set LUME_IFS_MODE = 2
set LUME_PRINT_DIRECT = true
# set LUME_MODULES_PATH = '~/.local/share/lumesh/mods'
# set LUME_MAX_SYNTAX_RECURSION = 100
# set LUME_MAX_RUNTIME_RECURSION = 800
```

---
