---
title: Lumesh for LF
date: 2025-07-05 19:16:45  
highlight: true  
tags:  
 - glance  
categories:  
 - wiki  
 - why  
 - syntax
---

Syntax Demonstration of Using Lumesh to Write lf Configuration Files

The [lf file manager](https://github.com/gokcehan/lf) is a powerful TUI file manager that supports highly flexible custom operations. It is my favorite file manager. Below are some demonstrations of command configurations written for lf using Lumesh.

[Complete Configuration File](/zh-cn/cases/lfrc_lm)

### 1. Variable Definition and Pipeline Operations

```bash
cmd all-cmd ${{
    let cmd = lf -remote `query $id cmds` | .lines() | .drop(1) | \
        .map(x -> {x.split("\t\t", $x) | .first()}) | Ui.pick "select cmd:"
    lf -remote `send $id :$cmd`
}}
```

- `lf -remote `query $id cmds`` retrieves available commands in lf.
- `let` defines a variable.
- The pipeline operator `|` is used for data stream processing.
- `.lines()` is a string processing method.
- `.drop(1)` is a list operation that skips the first element.
- `.map()` is functional programming using a lambda expression `x -> {...}`.
- `Ui.pick` is an interactive selector.

### 2. String Processing and Table Operations

```bash
cmd history-dir ${{
  let hist = lf -remote `query $id jumps` | Into.table('jump','path') | .drop(1) | Ui.pick "choose history:"
  lf --remote `send $id cd ${$hist.path}`
}}
```

- `lf -remote `query $id jumps`` retrieves the history directories in lf.
- `Into.table()` converts data into a table structure.
- String interpolation `${$hist.path}` syntax.
- Object property access `$hist.path`.

### 3. Conditional Statements and Pattern Matching

```bash
cmd toggle-preview %{{
    match $lf_preview {
        true => lf -remote `send $id :set nopreview; set ratios 1:5`
        _ => lf -remote `send $id :set preview; set ratios 1:2:3`
    }
}}
```

Lumesh's `match` pattern matching syntax is similar to Rust's match expression. Here, it matches the string `true`; if a boolean value is needed, it should be capitalized as `True`.

### 4. Conditional Expressions and Ternary Operations

```bash
cmd select-files &{{
    let htag= $lf_hidden ? '-H' : ''
    let r=fd --exact-depth 1 $argv $htag -c never -j 1 -0 |  xargs -0 printf ' %q'
    lf -remote `send $id :unselect; toggle $r`
}}
```

- Ternary conditional operator `condition ? value1 : value2`.
- Variable usage in command line: `$argv` represents the parameters received by the function.

### 5. Functional Programming and List Operations

```bash
cmd yank-name &{{
    $fx.lines() | .map(Fs.base_name) | .join("\n") | wl-copy
}}
```

- Method chaining `.lines().map().join()`.
- `Fs.base_name` is a filesystem module function.
- Pipeline operations pass results to external commands.

### 6. User Interaction

```bash
cmd delete ${{
  println '=====DELETE=====' $fx '================'
  if Ui.confirm('Delete these files [y/n]:'){
    $lf_user_wheel rm -rf $fx.lines()
  }
}}
```

- `println` is an output function.
- `Ui.confirm` is a confirmation dialog.
- `if` is a conditional statement.

### 7. Complex Data Processing and Loops

```bash
cmd mpaste %{{
    let load=Fs.read ~/.local/share/lf/files | .lines()
    let files = $load.drop(1)
    let file_count = len($files)
    if $file_count==0 {
        print 'No files yanked'
        exit 0
    }
    let mode=$load.at(0)
    let base_names = $files.map(Fs.base_name)
    let ans = read `$mode $file_count files? [y/N]`
    if $ans == 'y' {
        match $mode {
            copy => {
                $lf_user_wheel cp -r $argv -- $files '.'
                let tg='='
            }
            move => {
                $lf_user_wheel mv -- $files '.'
                let tg='>'
            }
        }
        for file in $base_names {
            lf -remote `send $id :select "$file"; tag $tg`
        }
    }
}}
```

- `Fs.read` reads a file.
- `len()` is a length function.
- `read` is a user input function.
- `for` is a loop statement.
- Complex `match` pattern matching.

### 8. Regular Expressions and String Operations

```bash
cmd extract-to %{{
    let dest = $argv[0] ?: {print 'Cancelled'; exit 0}
    if (Regex.match '\.([gb7xs]z|t[gbx]z|zip|zst|bz2|lz4|lzma|tar|rar|br)$' $f) {
        $lf_user_wheel ouch -q decompress --dir $dest $f
        let base_name = Fs.base_name($f)
        lf -remote `send $id :cd $dest; select $base_name; tag '^'`
    }else{
        print 'Unsupported file extension'
    }
}}
```

- The null coalescing operator `?:`.
- `Regex.match` for regular expression matching.
- Block expression `{print 'Cancelled'; exit 0}`.

## Notes

This configuration file perfectly showcases the powerful capabilities of Lumesh as a shell scripting language: it combines the syntactic features of modern programming languages (such as pattern matching, functional programming, and error handling) with the command execution capabilities of traditional shells. The design philosophy of Lumesh, "write like Python/JS, work like bash," is fully reflected in this example.

Wiki pages you might want to explore:
- [Complete lf Configuration File](/data/lfrc)
- [Syntax Overview (superiums/lumesh)](/glance)
- [Feature Overview (superiums/lumesh)](/feature)
- [Application Examples (superiums/lumesh)](/cases)
- [Syntax Manual (superiums/lumesh)](/doc/syntax)
- [Built-in Functions (superiums/lumesh)](/doc/libs/)
