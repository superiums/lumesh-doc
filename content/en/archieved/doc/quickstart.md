---
title: Quick Start
date: 2026-03-16 16:16:00
highlight: true
weight: 1
tags:
 - install
categories:
 - wiki
 - install
---

Lumesh User Tutorial - Getting Started
======================================

1 Quick Start Guide
---------------------

### 1.1 Installation

*   Execute the installation command

    curl -fsSL https://raw.githubusercontent.com/superiums/lumesh/main/docs/install.sh | bash
    

*   Or manually download and run:  
    [https://github.com/superiums/lumesh/releases](https://github.com/superiums/lumesh/releases)

    `bash install.sh`
    

### 1.2 Start

After installation, start the interactive shell:

```bash
    lume              # Full interactive shell (REPL, completion, highlighting)
    lumesh script.lm  # Execute a script file
```

### 1.3 CFM Mode Quick Start (Command First Mode)

*   Why do we need CFM mode?
    
    > The contradiction between programming and commands
    
    *   Operators and numbers are strings?
        
        To provide more modern programming features, Lumesh can recognize numbers, operators, and more types, for example:
        
        You can directly execute: `1+2` as a command.
        
        However, some traditional commands treat numbers and operators as regular strings, such as `ping 1.1.1.1`, `chmod +x script.sh`.
        
        You can add quotes to let Lumesh correctly identify them as strings: e.g., `ping '1.1.1.1'`, `chmod '+x' script.sh`.
        
        But for convenience, we have provided the CFM mode.
        
    *   Is a single word a command or an expression?
        
        In a programming experience, usually a single symbol is a variable; if it's used as a command, it's very dangerous.
        
        In Bash, it's usually directly used as a command, like `ls`, which is convenient.
        
        In Lumesh, we want both safety and convenience, so we have the CFM mode.
        
    
    > CFM (Command First Mode) is a mode designed by Lumesh for daily command line use, to provide better command compatibility.
    

#### 1.3.1 What is CFM

*   When entering a single line, the first word is prioritized as a command.
*   No need to quote IP addresses, operators, etc.
*   Example:
```bash
        ls                    # Directly execute the command
        ping 1.1.1.1          # Directly execute, no need for quotes
        chmod +x script.sh    # Write operators directly 
```

#### 1.3.2 Temporarily Switching Modes

In the interactive REPL, you can temporarily switch parsing modes with a line prefix:

*   `:` prefix: Temporarily switch to normal mode (write expressions)
*   `>` prefix: Temporarily force into CFM (even if global is off)

```bash
    : 1 + 2                # Normal mode, calculate the expression
    > ls                   # Force CFM
```

#### 1.3.3 Globally Enable/Disable CFM

Control with the `sys.set_cfm` function or configuration variable `set LUME_CFM = true`:

```bash
    sys.set_cfm(true)      # Enable CFM (default)
    sys.set_cfm(false)     # Disable CFM
```

#### 1.3.4 Differences Between CFM and Normal Mode

*   CFM disables numeric literals and mathematical operations (to avoid conflicts with command parameters).
*   Normal mode supports full expression syntax (numbers, ranges, comparisons, etc.).
*   Script execution does not enable CFM by default, but you can use the `>` prefix to force it.

### 1.4 Brief Explanation of Other Modes

#### 1.4.1 Strict Mode

*   Variables must be declared before use, prohibiting implicit declaration.
*   Operators require space separation.
*   Enabled via `sys.set_strict(true)` or configuration `LUME_STRICT=true`.

#### 1.4.2 Print Direct Mode

*   Controls whether to print expression results directly in the REPL.
*   Switch using `sys.set_pdm`, or configure `LUME_PRINT_DIRECT`.

2 Language Reference Manual
-----------------------------

### 2.1 Command Syntax

*   Under Command Priority (CFM) mode, commands are basically consistent with Bash, but with some enhanced syntax support.  
    For example:

```bash
    ls
    ls ~ | string.red
```

*   Under Normal (NM) mode, commands differ slightly from Bash. As mentioned above:

1.  If there is a single symbol, add a placeholder to let lume recognize it as a command, such as:

    ls _
    

> The right side of the pipe can be omitted: `'/opt' | ls`

2.  If there are operators or numbers as parameters, they should be quoted

```bash
    ping '1.1.1.1'
    chmod '+x' .
```

*   Mode switching: see the previous section

### 2.2 Syntax Design Features

#### 2.2.1 JavaScript-like Syntax

Supports destructuring, lambdas, and chained calls:

```bash
    let user = {name: "Alice", age: 25}
    let {name, age} = user
    let numbers = 1..10 | list.filter(x -> x > 5)
 ```

#### 2.2.2 Modern Programming Language Features

*   Function decorators:

```bash
    fn my() {}
```

*   Module imports: `use module as alias; alias::func()`
*   Error handling operators: `?.` `?:` `?+` etc.

#### 2.2.3 Expression Type System

All structures are expressions, supporting over 50 expression types.

### 2.3 Overview of Data Types

#### 2.3.1 Basic Types

*   Strings (double quotes with escape, single quotes for raw)
*   Numbers (integers, floats)
*   Booleans (`true`/`false`)

#### 2.3.2 Composite Types

*   Lists: `[1, "a", true]` `1...10`
*   Maps: `{key: "value"}`
*   Ranges: `1..10` (left-closed, right-open)

Enhanced types:

*   Sets: `S{a,b,c}`
*   Maps: `M{a,b,c}` `H{a,b,c}`

#### 2.3.3 Special Types

*   Regular expressions: `r'^\w+'`
*   Time: `t'2025-5-20'`
*   File size: supports suffixes like `5K`, `1M`.
*   Percentages: `5%`

### 2.4 Overview of Control Flow Structures

#### 2.4.1 Conditional Statements

```bash
    let res = if cond { "A" } else { "B" }
    
```

#### 2.4.2 Loop Statements

```bash
    for i in 0..3 { print i }
    while x < 10 { x += 1 }
    loop { break }
    
```
#### 2.4.2 match Statements

```bash
    match x {
        a => {}
        b => {}
    }
    
```
#### 2.4.3 Exception Handling Mechanism

Use error handling operators:

```bash
    command ?.         # Ignore errors
    command ?: default # Return default value on error
    command ?: x -> {} # Error handling function
    
```

### 2.5 Pipeline

*   Functionality is similar to Bash pipeline, supports byte stream
*   **Enhancement**: Supports structured data flow
*   **Enhancement**: Supports placeholder `_`, thus changing the pipeline target position


```bash
    'b' | print 'a' _ 'c'
    
```
*   **Enhancement**: Supports loop dispatch

```bash
    0..6 |> print 
    
```
### 2.6 Overview of Built-in Modules

> Module list: View through `help libs`.

#### 2.6.1 Built-in Constants

| COLOR | STYLE | MATH |
|--------------|-------------------|---------------------|

Usage:  
`COLOR.RED + 'lume'`

#### 2.6.2 Built-in Library Functions

| Module | Function | Example |
|--------------|-------------------|---------------------|
| string/regex | String processing | `string.split`, `string.join` |
| list/set | Set operation | `list.map`, `list.filter` |
| map/hmap | Map operation | `map.keys`, `map.merge` |
| fs | File System | `fs.read`, `fs.write`, `fs.exists` |
| time | Time Operation | `time.now`, `time.format` |
| math/rand | Math Calculation | `math.abs`, `math.pow` |
| ui | User Interface | `ui.pick`, `ui.confirm` |
| sys/console | System Functions | `sys.env`, `sys.set_cfm` |
| into/from/log | Data Conversion and logging | `into.str`, `from.json`, `log.info` |  

*   Loading mechanism: Dynamic lazy loading, initialized on first call.
*   Namespace: `lib.function()` call.

#### 2.7 Scope

*   Statement blocks declared with `%{ }` have independent scope
*   Functions and loop statements automatically have independent scope
*   Variables declared with `let` are only valid within their own scope
*   To change parent scope variables, use `set`

3 Overview of Basic Configuration
-----------------------------------

### 3.1 config.lm Structure Explanation

The configuration file is located at `~/.config/lumesh/config.lm` (Unix) or corresponding platform path, including:

*   Appearance: logo, theme, welcome message.
*   Prompt: `LUME_PROMPT_SETTINGS` and `LUME_PROMPT_TEMPLATE`.
*   Interaction: Vi mode, completion, history.
*   Key bindings: Hotkeys and abbreviations.

### 3.2 Environment Variable Settings

Common variables:

*   `LUME_CFM`: Whether to enable CFM by default.
*   `LUME_STRICT`: Whether to enable strict mode by default.
*   `LUME_THEME`: Syntax highlighting theme.
*   `LUME_HOT_MODIFIER`/`LUME_HOT_KEYS`: Custom hotkeys.
*   `LUME_ABBREVIATIONS`: Text abbreviation mapping.

### 3.3 Initial Configuration Optimization Suggestions

*   Enable CFM to improve daily command line experience.
*   Enable strict mode as needed for writing scripts.
*   Customize hotkeys (e.g., Alt+H for history, Alt+X for bookmarks).
*   Configure the prompt template to display Git branches and mode tags.
