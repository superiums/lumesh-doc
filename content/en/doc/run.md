---
title: Starting and Running
date: 2025-06-11 19:16:45
highlight: true
weight: 2
tags:
 - install
categories:
 - wiki
 - install
---

> The interactive mode is started with the `lume` or `lume -i` command.

## Startup Parameters

The command line supports various execution modes, as you can see:

```bash
> lume -h
Lumesh scripting language runtime

Usage: lume [OPTIONS] [FILE_N_ARGS]... [-- [CMD_ARGV]...]

Arguments:
  [FILE_N_ARGS]...  script file and args to execute
  [CMD_ARGV]...     args for cmd

Options:
  -p, --profile      config file
  -s, --strict       strict mode
  -i, --interactive  force interactive mode
  -m, --cfmoff       NO command first mode
  -a, --aioff        NO ai mode
  -n, --nohistory    NO history (private) mode
  -c, --cmd <CMD>    command to eval
  -h, --help         Print help
  -V, --version      Print version
```

## Running Modes

### 1. REPL Interactive Mode
> `lume`

The user interactive mode is the default startup mode, handling user input and output.

It supports syntax highlighting and auto-completion.

**Auto Suggestions**

- Typing part of a word will prompt Lumesh to suggest matching built-in functions, third-party commands, aliases, built-in syntax, and historical commands.

- Typing `module_name + dot + space`: `Into. ` will suggest all available functions within that module.

**Auto Completion**:

- Pressing `Tab` will trigger auto-completion.

Completion modes:

  + The first word triggers *command completion*, including system executable commands, built-in functions, aliases, and built-in syntax.

  + Words with path symbols like `.`, `/` (or `\` on Windows) will trigger *path completion*.

  + More than two words that are not paths will trigger *AI auto-completion*.

  _To use AI completion, please configure the AI interface first._ The default is the Ollama configuration, which connects after running Ollama.

**Common Keys**:

- `Right` or `Ctrl+J`: Accept completion suggestions (supports paths and historical commands).
- `Alt+J`: Accept a single word completion suggestion.
- `Ctrl+D`: Terminate input.
- `Ctrl+C`: Terminate the current operation.
- `Ctrl+L`: Clear the screen.

For other shortcuts, please refer to [Shortcuts](keys).

**Custom Key Bindings**:
Custom bindings can be configured in the configuration file. Please refer to the [Configuration Files](config) section.

**History**: Saved in the default configuration directory, configurable via the configuration file.
- `UP/DOWN` or `Ctrl+N/P`: Switch between history records.


### 2. Script Parsing Mode
> `lume [filename]`
Run a script:

```bash
lume ./my.lm
lume ./my.lm arg1 arg2
```

If you are particularly concerned about performance, you can use the command parsing dedicated lite version: `lumesh`.

### 3. Command Execution Mode
> `lume -c <command> <parameters>...`
If you need to keep the REPL window open after executing a command for continued interaction, add the `-i` parameter:
> `lume -ic <command> <parameters>...`

### 4. LOGIN-SHELL Mode
> The first shell at system startup.

To start the shell, you should first configure the system environment variables in the config file, similar to `.bashrc`. Once prepared, you can execute `set_as_login_shell()` or the following script:

```bash
    let p = (About | .get('bin'))
    if !(fs.read /etc/shells | .contains($p)) {
        sudo lume -c `fs.append /etc/shells "\n$p"` ?: doas lume -c `fs.append /etc/shells "\n$p"`
    }
    chsh -s $p
```

### 5. Strict Mode
> `lume -s`

In strict mode, variables must be declared first and cannot be redeclared.

When using variables, the `$` prefix should be used. This is to enhance script parsing speed.

In non-strict mode, direct access to variables without `$` is allowed.

Both modes allow implicit type conversion.

### 6. Command Priority Mode

#### Default CFM Mode
> `lume`
In interactive command mode and command mode (`lume -c`), if the input is only one line, command priority mode is used.

In this mode, it prioritizes parsing as a command rather than programming, treating characters like `.` and `+` as ordinary characters.

*To temporarily skip this mode, add `:` at the beginning of the command line.*
```bash
ping 1.1.1.1     # Correct, in CFM mode `.` is an ordinary character
ping '1.1.1.1'   # Correct
3+5^2            # Incorrect, in CFM mode `+` is an ordinary character
:3+5^2           # Correct, temporarily skip CFM mode
```

*If the input has multiple lines, it will not enter CFM mode.*

#### Skip CFM Mode
> `lume -m`

```bash
ping 1.1.1.1     # Incorrect, `.` is an operator
ping '1.1.1.1'   # Correct
3+5^2            # Correct, `+` is an operator
```

### 7. No AI Mode
### 8. No History Mode
