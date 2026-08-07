---
title: "Lumesh Examples: LF File Manager"
date: 2025-07-05 19:16:45
highlight: true
tags:
 - glance
categories:
 - wiki
 - why
 - syntax
---

Syntax demonstrations for writing LF configuration files using Lumesh

[LF File Manager](https://github.com/gokcehan/lf) is a very powerful TUI file manager that supports highly flexible custom operations. It's the author's favorite file manager. The following are partial demonstrations of commands configured with Lumesh for LF.

[Complete Configuration File](/data/lfrc_lm)

### 1. Variable Definition and Pipe Operations

```bash
cmd all-cmd ${{
    let cmd = lf -remote `query $id cmds` | .lines() | .skip(1) | \
        .map(x -> {x.split("\t\t", $x) | .first()}) | ui.pick "select cmd:"
    lf -remote `send $id :$cmd`
}}
```

- ` `lf -remote `query $id cmds`` ` retrieves available LF commands
- `let` variable definition
- Pipe operator `|` for data flow processing
- `.lines()` string processing method
- `.skip(1)` list operation to skip first element
- `.map()` functional programming using lambda expression `x -> {...}`
- `ui.pick` interactive selector

### 2. String Processing and Table Operations

```bash
cmd history-dir ${{
  let hist = lf -remote `query $id jumps` | into.table('jump','path') | .skip(1) | ui.pick "choose history:"
  lf --remote `send $id cd ${$hist.path}`
}}
```

- ` lf -remote `query $id jumps` ` gets LF's history directories
- `into.table()` converts data to table structure
- String interpolation `${$hist.path}` syntax
- Object property access `$hist.path`

### 3. Conditional Statements and Pattern Matching

```bash
cmd toggle-preview %{{
    match $lf_preview {
        true => lf -remote `send $id :set nopreview; set ratios 1:5`
        _ => lf -remote `send $id :set preview; set ratios 1:2:3`
    }
}}
```

Lumesh's `match` pattern matching syntax, similar to Rust's match expressions.
Here matching the `true` string; for bool values should capitalize first letter `true`

### 4. Conditional Expressions and Ternary Operators

```bash
cmd select-files &{{
    let htag= $lf_hidden ? '-H' : ''
    let r=fd --exact-depth 1 $argv $htag -c never -j 1 -0 |  xargs -0 printf ' %q'
    lf -remote `send $id :unselect; toggle $r`
}}
```


- Ternary conditional operator `condition ? value1 : value2`
- Variable usage in command line: `$argv` receives function parameters

### 5. Functional Programming and List Operations

```bash
cmd yank-name &{{
    $fx.lines() | .map(fs.base_name) | .join("\n") | wl-copy
}}
```

- Method chaining `.lines().map().join()`
- `fs.base_name` filesystem module function
- Pipe operation passes result to external command

### 6. User Interaction

```bash
cmd delete ${{
  println '=====DELETE=====' $fx '================'
  if ui.confirm('Delete these files [y/n]:'){
    $lf_user_wheel rm -rf $fx.lines()
  }
}}
```

- `println` output function
- `ui.confirm` confirmation dialog
- `if` conditional statement

### 7. Complex Data Processing and Loops

```bash
cmd mpaste %{{
    let load = fs.read ~/.local/share/lf/files | .lines()
    let files = $load.skip(1)
    let file_count = len($files)
    if $file_count==0 {
        print 'No files yanked'
        exit 0
    }
    let mode=$load.at(0)
    let base_names = $files.map(fs.base_name)
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

- `fs.read` file reading
- `len()` length function
- `read` user input function
- `for` loop statement
- Complex match pattern matching

### 8. Regular Expressions and String Operations

```bash
cmd extract-to %{{
    let dest = $argv[0] ?: {print 'Cancelled'; exit 0}
    if (regex.match '\.([gb7xs]z|t[gbx]z|zip|zst|bz2|lz4|lzma|tar|rar|br)$' $f) {
        $lf_user_wheel ouch -q decompress --dir $dest $f
        let base_name = fs.base_name($f)
        lf -remote `send $id :cd $dest; select $base_name; tag '^'`
    }else{
        print 'Unsupported file extention'
    }
}}
```


- Null coalescing operator `?:`
- `regex.match` regular expression matching
- Block expression `{print 'Cancelled'; exit 0}`

## Notes

This configuration file perfectly demonstrates Lumesh's powerful features as a shell scripting language: combining modern programming language syntax features (such as pattern matching, functional programming, error handling) with traditional shell command execution capabilities. Lumesh's design philosophy of "write like Python/JS, work like bash" is fully reflected in this example.

Wiki pages you might want to explore:
- [Complete LF configuration file](/data/lfrc)
- [Syntax overview (superiums/lumesh)](/zh-cn/glance)
- [Feature overview (superiums/lumesh)](/zh-cn/feature)
- [Application examples (superiums/lumesh)](/zh-cn/cases)
- [Syntax manual (superiums/lumesh)](/zh-cn/doc/syntax)
- [Built-in functions (superiums/lumesh)](/zh-cn/doc/libs/)
