---
title: Configuration Files
date: 2026-07-20 15:16:45
highlight: true
weight: 13
tags:
  - syntax
categories:
  - wiki
  - syntax
---

## Configuration File Location

Lumesh automatically loads configuration files on startup, in the following order:

1. Path specified via command-line argument `-p <path>`
2. Path specified by environment variable `LUME_PROFILE`
3. System config directory: `$config_dir/lumesh/config.lm` (Linux: `~/.config/lumesh/config.lm`, macOS: `~/Library/Application Support/lumesh/config.lm`)
4. `.lume_config` in current directory

Configuration files are standard lumesh scripts that support conditional statements:

```bash
if IS_LOGIN {
    # Execute only in login shell
}
if IS_INTERACTIVE {
    # Execute only in interactive mode
}
# Execute in all modes
```

---

## Variable Quick Reference

| Variable | Type | Default Value | Scope |
|----------|------|---------------|-------|
| `LUME_KNOCK_VALIDATOR` | Function | None | Login |
| `LUME_WELCOME` | String | Built-in welcome message | Interactive |
| `LUME_PROMPT_SETTINGS` | Map | `{starship:0, ttl:2}` | Interactive |
| `LUME_THEME` | String | `"one_dark"` | Interactive |
| `LUME_THEME_CONFIG` | Map | None | Interactive |
| `LUME_STRICT` | Boolean | `false` | Global |
| `LUME_CFM` | Boolean | `false` | Global |
| `LUME_NO_HISTORY` | Boolean | `false` | Interactive |
| `LUME_HISTORY_FILE` | String | System cache directory | Interactive |
| `LUME_COMPLETION_DIR` | String | System data directory | Interactive |
| `LUME_SUDO_CMD` | String | `"sudo"` | Interactive |
| `LUME_HOT_BINDINGS` | Map | None | Interactive |
| `LUME_ABBREVIATIONS` | Map | None | Interactive |
| `LUME_AI_CONFIG` | Map | None | Interactive |
| `LUME_SLASH_BINDINGS` | Map | None | Interactive |
| `LUME_SLASH_MENU` | Any | None | Interactive |
| `LUME_EDITOR_THEME` | Map | None | Interactive |
| `LUME_CONTINUATION_PROMPT` | String | `"..."` | Interactive |
| `LUME_PRINT_DIRECT` | Boolean | `true` | Global |
| `LUME_IFS_MODE` | Integer | `2` | Global |
| `LUME_MODULES_PATH` | String | System data directory | Global |
| `LUME_MAX_SYNTAX_RECURSION` | Integer | `100` | Global |
| `LUME_MAX_RUNTIME_RECURSION` | Integer | `800` | Global |

---

## Detailed Description

### Appearance and Prompt

#### `LUME_WELCOME`
- **Type**: String
- **Description**: Welcome message displayed on startup. Displays default version welcome if not set.

```bash
set LUME_WELCOME = 'Welcome to my shell!'
```

---

#### `LUME_PROMPT_SETTINGS`
- **Type**: Map
- **Fields**:
  - `starship`: Whether to enable starship
    - false: Disable
    - true: Enable [starship](https://starship.rs/) prompt
  - `lazy`: Prompt refresh level, default `0`
  - `template`: Template rendering, can be function or string
  - `continuation`: Continuation symbol, string

- **Description**: 

  - `lazy` 
    - 0: always refresh while render
    - 1: refresh only status, refresh path only after cd
    - 2: refresh only after refresh

  - `template` takes effect when starship is disabled.
    - **String mode**: Supports the following placeholders:
      - `$CWD`: Full current path
      - `$CWD_SHORT`: Shortened path
      - `$CFM_TAG`: CFM mode marker (`"CFM"` or empty)
      - `$STRICT_TAG`: Strict mode marker (`"S"` or empty)
      - `$STATUS`: Status marker (`"OK"` or "Fail")
      - `$DUARATION`: Command execution duration (ms)
      - `$JOBS`: Number of background tasks
    - **Lambda mode**: Receives `(dir, ctx)` two parameters, `ctx` contains `cfm` and `strict` two boolean fields, and `status`/`duration`/`jobs` three integer fields, called every time a prompt is rendered.

```bash
# String template
set template = '$CWD_SHORT|$CFM_TAG> '

# Lambda template (dynamic, supports git branches, etc.)
set template = (dir, ctx) -> {
    string.blue($dir) + ' |'.green().bold()
    + ($ctx.cfm ? 'CFM'.green() + '|' : '')
    + (if (fs.exists '.git') { git branch --show-current | .cyan() } else '')
    + '> '.green().bold()
}

set LUME_PROMPT_SETTINGS = {
    starship: 0,
    lazy: 0,
    template,
    continuation: '... '
}
```

---

#### `LUME_THEME`
- **Type**: String
- **Optional values**: `'one_dark'`, `'ayu_dark'`, `'light'`
- **Default value**: `'one_dark'` (if not set)
- **Description**: Base theme for syntax highlighting.

```bash
set LUME_THEME = 'ayu_dark'
```

---

#### `LUME_THEME_CONFIG`
- **Type**: Map (key is theme element name, value is ANSI color string)
- **Description**: Override specific colors on top of base theme. Keys that can be overridden include:

| Key | Description |
|-----|-------------|
| `mode` | Mode prompt (`>` `:`) |
| `keyword` | Keywords |
| `value_symbol` | Value symbol (`$var`) |
| `operator` | Operators |
| `operator_prefix` | Prefix operators |
| `operator_infix` | Infix operators |
| `operator_postfix` | Postfix operators |
| `string_raw` | Raw string |
| `string_template` | Template string |
| `string_literal` | String literal |
| `number_literal` | Number literal |
| `integer_literal` | Integer literal |
| `float_literal` | Float literal |
| `symbol` | Regular symbol |
| `builtin_cmd` | Built-in command |
| `builtin_lib` | Built-in library |
| `comment` | Comments |
| `punctuation` | Punctuation |
| `command_valid` | Valid command |
| `regex` | Regular expressions |
| `time` | Time literal |

```bash
set LUME_THEME_CONFIG = {
    keyword: "\x1b[38;5;82m",   # Bright green
    comment: "\x1b[38;5;244m",  # Gray
    operator: COLOR.orange
}
```

---

#### `LUME_EDITOR_THEME`
- **Type**: Map
- **Description**: Editor (auto-completion and prompt) color theme, independent from syntax highlighting theme.
Available colors:
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

### Interactive Mode

#### `LUME_CFM`
- **Type**: Boolean
- **Default value**: `false`
- **Description**: Enables Command First Mode (CFM), where input lines are parsed as external commands rather than expressions. Can also be enabled via command-line `-m` parameter.

```bash
set LUME_CFM = true
```

---

### History

#### `LUME_NO_HISTORY`
- **Type**: Boolean
- **Default value**: `false`
- **Description**: Disables history reading and writing (private mode). Can also be enabled via command-line `-H` parameter.

```bash
set LUME_NO_HISTORY = true
```

---

#### `LUME_HISTORY_FILE`
- **Type**: String
- **Default value**:
  `~/.cache/lume/history`
- **Description**: History file path.

```bash
set LUME_HISTORY_FILE = '/tmp/lume_history'
```

---

### Auto-completion

#### `LUME_COMPLETION_DIR`
- **Type**: String
- **Default value**:
  - Linux: `/usr/share/lumesh/completions`
  - macOS: `~/Library/Application Support/lumesh/completions`
  - Windows: `C:\Program Files\lumesh\completions`
- **Description**: Directory containing auto-completion data files (CSV format).

```bash
set LUME_COMPLETION_DIR = '~/.local/share/lumesh/completions'
```

---

### Shortcuts

#### `LUME_SUDO_CMD`
- **Type**: String
- **Default value**: `"sudo"`
- **Description**: `Alt+s` shortcut inserts at line beginning.

```bash
set LUME_SUDO_CMD = 'doas'
```

---

#### `LUME_ABBREVIATIONS`
- **Type**: Map (key is abbreviation, value is expanded string)
- **Description**: Automatically expands to full command when pressing space after typing abbreviation.

```bash
set LUME_ABBREVIATIONS = {
    xi:  'doas pacman -S',
    xup: 'doas pacman -Syu',
    xq:  'pacman -Q',
}
```

---

### Slash Commands

#### `LUME_SLASH_BINDINGS`
- **Type**: Map (key is slash command name, value is **function that receives one parameter** or lambda)
- **Description**: Bindings for custom slash commands (`/xxx`). Built-in slash commands (`/history`, `/h`, `/hh`, `/hm`, `/cds`, `/q`, etc.) are not affected by this variable.

```bash
let open_file = (b) -> {fd -t file | ui.pick('select file:') ?! | handlr open -- _}
set LUME_SLASH_BINDINGS = {
    o: open_file,
}
```

---

#### `LUME_SLASH_MENU`
- **Type**: Function (receives no parameters)
- **Description**: Data source for slash command menu, used for interactive selection menu triggered by `/`.
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
- **Type**: Map
- **Description**:
> Key format: "MODIFIER_key" (order CTRL_ALT_SHIFT: e.g., "CTRL_ALT_k", "CTRL_q", "ALT_h")
> Value type:
>   String -> inserts text into current line
>   lambda/function -> executes immediately with environment access
>         fn receive one arg as current buffer.


Similar to SLASH_BINDINGS, but acts before the end of current input line;

Key-value can be string or function, function executes in isolated environment variables, can access copy of current environment variables, but any env modifications will not affect existing environment.

Function also receives one parameter (text user has already typed in current line), function's return value if string is used to replace current line input.

```bash
set LUME_HOT_BINDINGS = {
    CTRL_q: 'exit',
    ALT_m: save_cmdmark,
    'CTRL_/': menu,
}

```


---

### AI Integration

#### `LUME_AI_CONFIG`
- **Type**: Map
- **Description**: Configure local AI assistant (e.g., Ollama). `Alt+i` triggers AI prompt, `Alt+Enter` / `Alt+o` triggers AI generation.

| Field | Type | Default Value | Description |
|-------|------|---------------|-------------|
| `host` | String | `"localhost:11434"` | AI service address |
| `chat_url` | String | `"/v1/chat/completions"` | Chat interface path |
| `complete_max_tokens` | Integer | `10` | Maximum completion tokens |
| `chat_max_tokens` | Integer | `100` | Maximum chat tokens |
| `model` | String | `""` | Model name |
| `api_key` | String | `""` | Access key |
| `chat_prompt` | String | `"you're a lumesh shell helper"` | Prompt |
| `syntax` | String | `""` | Lumesh syntax rules |

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

### Security

#### `LUME_KNOCK_VALIDATOR`
- **Type**: Function or Lambda (no parameters, returns Boolean)
- **Description**: Only takes effect in login mode (`IS_LOGIN`). Called by lumesh on startup, if returns `false` then immediately exits (`exit(1)`). Can be used for stronger verification in server environments.

```bash
if IS_LOGIN {
    set LUME_KNOCK_VALIDATOR = () -> {
        let ans = read "Password: "
        ans == 'secret'
    }
}
```

---

### Global Behavior

#### `LUME_PRINT_DIRECT`
- **Type**: Boolean
- **Default value**: `true`
- **Description**: Whether to automatically print evaluation result of expression (type and value). Set to `false` to disable this behavior, suitable for script mode.

```bash
set LUME_PRINT_DIRECT = false
```

---

#### `LUME_IFS_MODE`
- **Type**: Integer (bitmask)
- **Default value**: `2`
- **Description**: Controls in which scenarios `IFS` (Internal Field Separator) takes effect. Bit meanings:

| Bit value | Scenario |
|-----------|----------|
| `2` | Command string argument splitting (cmd str_arg) |
| `4` | `for` loop string iteration |
| `8` | `string.split` |
| `16` | CSV parsing |
| `32` | `ui.pick` string splitting |
| `62` | Enable all |
| `0` | Disable all |

```bash
# Only for command arguments (default)
set LUME_IFS_MODE = 2

# Enable all
set LUME_IFS_MODE = 62
```

---

#### `LUME_MODULES_PATH`
- **Type**: String
- **Default value**: `lumesh/mod` in system data directory
- **Description**: Root directory for `use` statement to find modules. Global modules should be placed here. Single project modules can be placed in mods directory at same level as script.

```bash
set LUME_MODULES_PATH = '~/.local/share/lumesh/mods'
```

---

#### `LUME_MAX_SYNTAX_RECURSION`
- **Type**: Integer
- **Default value**: `100`
- **Description**: Maximum recursion depth for syntax parsing. Can be increased if script nesting depth is too deep.

```bash
set LUME_MAX_SYNTAX_RECURSION = 200
```

---

#### `LUME_MAX_RUNTIME_RECURSION`
- **Type**: Integer
- **Default value**: `800`
- **Description**: Maximum recursion depth for runtime (function calls). Can be increased if recursive function depth is too deep.

```bash
set LUME_MAX_RUNTIME_RECURSION = 1600
```

---

## Complete Configuration Example

```bash
# ===== Login Mode =====
if IS_LOGIN {
    set PATH = '/usr/local/bin:/usr/bin:/bin'
    # Optional: login verification
    # set LUME_KNOCK_VALIDATOR = () -> { read "Password: " == 'secret' }
}

# ===== Interactive Mode =====
if IS_INTERACTIVE {

    # --- Appearance ---
    let template = (dir, ctx) -> {
        string.blue($dir)
        + ($ctx.cfm ? ' CFM'.green() : '')
        + '> '.bold()
    }
    set LUME_PROMPT_SETTINGS = { starship: 0, lazy: 0, template }
    LUME_THEME = 'ayu_dark'

    # --- Interactive Behavior ---
    # set LUME_VI_MODE = true
    # set LUME_STRICT = true
    # set LUME_CFM = true

    # --- History ---
    # set LUME_NO_HISTORY = true
    # set LUME_HISTORY_FILE = '/tmp/lume_history'

    # --- Auto-completion ---
    set LUME_COMPLETION_CYCLE = false
    # set LUME_COMPLETION_DIR = '~/.local/share/lumesh/completions'

    # --- Shortcuts ---
    set LUME_SUDO_CMD = 'doas'
    # --- Abbreviations ---
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

# ===== Global Settings =====
PATH = '~/.local/bin:' + $PATH ?.
set LUME_IFS_MODE = 2
set LUME_PRINT_DIRECT = true
# set LUME_MODULES_PATH = '~/.local/share/lumesh/mods'
# set LUME_MAX_SYNTAX_RECURSION = 100
# set LUME_MAX_RUNTIME_RECURSION = 800
```
