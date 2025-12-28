---
title: Interactive Commands  
date: 2025-12-11 19:16:45  
highlight: true  
weight: 20
tags:  
 - cli  
categories:  
 - wiki  
 - cli  
---

The command line supports various execution modes, as you can see:

```shell
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

## Interactive Mode

- Strict Mode (s)  
Variables must be defined;  
Variables cannot be redefined;  
Variables must be prefixed with `$` when used;  

- Interactive Mode (i)  
Regardless of whether a command is specified, it enters interactive mode, allowing the user to type commands.

- Command Priority Mode (default)  
In interactive command mode, if the input is only one line, it uses command priority mode.  
In this mode, it prioritizes parsing as a command rather than programming, treating characters like `.` and `+` as ordinary characters. For example, `ping 1.1.1.1` and `chmod +x a.b` are parsed as commands without needing quotes.  
To bypass this mode, there are two methods:  
  1. Add the `-m` option when starting lume;  
  2. Add `:` at the beginning of the command line.  

- No AI Mode  
- No History Mode  

### Parameter Passing  
`lume -c cmd -- arg1 -arg2 --arg3`  
`lume -c cmd arg1 -arg2 --arg3`  

### Completition
press `Tab`


## Running Scripts  
`lume script.lm arg1 -arg2 --arg3`  

`script.lm arg1 -arg2 --arg3` requires a shebang line and executable permissions (chmod +x).
