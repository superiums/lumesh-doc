---
title: Startup and Execution
date: 2025-06-11 19:16:45
highlight: true
weight: 12
tags:
  - install
categories:
  - wiki
  - install
---

> Interactive mode is started via `lume` or `lume -i` command

## Startup Parameters

Command-line supports multiple execution modes as you see:

```bash
> lume -h
Lumesh scripting language runtime

Usage: lume [OPTIONS] [FILE_N_ARGS]... [-- [CMD_ARGV]...]

Arguments:
  [FILE_N_ARGS]...  Script file and args to execute
  [CMD_ARGV]...     args for cmd

Options:
  -p, --profile      Config file
  -s, --strict       Strict mode
  -i, --interactive  Force interactive mode
  -m, --cfmoff       NO command first mode
  -a, --aioff        NO ai mode
  -n, --nohistory    NO history (private) mode
  -c, --cmd <CMD>    Command to eval
  -h, --help         Print help
  -V, --version      Print version
```

## Execution Modes

### 1. REPL Interactive Mode
> `lume`

User interactive mode is the default startup mode, handling user input/output.

Supports code highlighting, auto-completion.

**Auto-hinting**

- Type partial words, lumesh will automatically hint matching built-in functions, third-party commands, aliases, built-in syntax, history commands

- Type `module_name.+.`: `into.` will hint all available functions in that module.

**Auto-completion**:

- Press `Tab` to trigger auto-completion.

Completion mode:

  + First word, triggers *command completion*, including system executable commands, built-in functions, aliases, built-in syntax.

  + Words with path symbols, like `.`, `/` (win then `\`), triggers *path completion*.

  + More than two words, and not a path, then triggers *AI auto-completion*.

  *To use AI completion, please configure AI interface first*. Default is ollama default configuration, just run ollama and connect.

**Common Keys**:

- `Ctrl+J`: Accept completion suggestion (supports paths and history commands).
- `Alt+J`: Accept single word completion suggestion.
- `Ctrl+D`: Terminate input.
- `Ctrl+C`: Terminate current operation.
- `Ctrl+U`/`Ctrl+K`: Clear input before/after current line
- `Ctrl+O`: Clear multi-line input
- `Ctrl+L`: Clear screen

For more shortcuts, see [Shortcuts](keys)

**Custom Hotkey Bindings**:
Can be configured in config file.
Please see [Configuration](config) section.

**History**: Saved in default config directory, can be configured via config file.
- `UP/DOWN` or `Ctrl+N/P`: Switch history records.


### 2. Script Parsing Mode
> `lume [filename]`
Run scripts:

```bash
lume ./my.lm
lume ./my.lm arg1 arg2
```

If you particularly care about performance, you can use the command parsing exclusive lite version: `lumesh`

### 3. Command Execution Mode
> `lume -c <command> <args>...`
If you need to keep REPL window after command execution for continued interaction, add `-i` parameter
> `lume -ic <command> <args>...`

### 4. Login-Shell Mode
> First shell at system startup

To start shell, first configure system environment variables in config file, similar to .bashrc content.
When ready, execute `set_as_login_shell()` or the following script:

```bash
  let p = (About | .get('bin'))
  if !(fs.read /etc/shells | .contains($p)) {
      sudo lume -c `fs.append /etc/shells "\n$p"` ?: doas lume -c `fs.append /etc/shells "\n$p"`
  }
  chsh -s $p
```

### 5. Strict Mode

**Strict**

In strict mode, variables must be declared first and cannot be re-declared.

When using variables, use `$` prefix. This is to improve script parsing speed.

**Non-Strict**

In non-strict mode, allow accessing variables without `$` prefix.

Both modes allow implicit type conversion.


#### Mode Switching
- Startup Mode
  Depends on factor 1: Setting in config file: `set LUME_STRICT = true` enables strict mode.
  
  Depends on factor 2: Startup lume parameters: If `-s` parameter exists, it will override config file settings.

- Command Switching
  ```bash
  sys.set_strict(true)     # Enable strict mode
  sys.set_strict(false)    # Disable strict mode
  ```

### 6. Command First Mode

#### Mode Description

- Normal Mode
  Symbols parsed first as operators.

- Command First Mode (CFM)
  Parsed first as commands, especially parameters position's `.` `+` `-` `=` operators treated as regular characters first.


#### Mode Switching
- Startup Mode
  Depends on factor 1: Setting in config file: `set LUME_CFM = true` enables Command First Mode.
  Depends on factor 2: Startup lume parameters: If `-m` and `-M` parameters exist, they will override config file settings.

- Command Switching
  ```bash
  sys.set_cfm(true)     # Enable CFM mode
  sys.set_cfm(false)    # Disable CFM mode
  ```

- Temporary Switching
  Type `:` at line beginning to switch to Normal mode
  
  Type `>` at line beginning to switch to Command First mode

- Auto Mode
  If not set, single line input uses CFM mode, multi-line input uses Normal mode.

More information can be found in [Syntax Rules](syntax/symbo)

### 6. No AI Mode
### 7. No History Mode
