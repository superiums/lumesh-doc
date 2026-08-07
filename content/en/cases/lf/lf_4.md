---
title: LF File Manager Configuration Comparison D
date: 2025-07-15 10:16:45
highlight: true
tags:
 - case
categories:
 - wiki
 - case
---

LF filemanager configuration comparison D

### 31. rename-to Command
**Key Binding**: `mv`

**Comparison**:
- **Lumesh**: Uses built-in file functions and string interpolation
```bash
let base_name = fs.base_name($fx)
let new_name = read `rename "$base_name" to:`
if $new_name {
    $lf_user_wheel mv -- $base_name $new_name
    lf -remote `send $id :select $new_name`
}
```

- **Bash**: Uses basename and printf
```bash
fn=$(basename "$fx")
printf "rename $fn to:"
read ans
[ -n "$ans" ] && $lf_user_wheel mv -- $fn $ans
```

**Advantages**:
- **Lumesh**: Built-in file functions are type-safe, string interpolation is more intuitive
- **Bash**: basename command is standard, conditional statements are concise

### 32. chmod Command
**Key Binding**: `cm`

**Comparison**:
- **Lumesh**: Uses pipe operator and loops
```bash
let ans = read "Mode Bits:"
if $ans {
    $fx |> $lf_user_wheel chmod $ans _
    lf -remote 'send reload'
}
```

- **Bash**: Uses xargs for parallel processing
```bash
printf "\nMode Bits: "
read ans
if [ -n "$ans" ]; then
    set -f
    printf "%s\n" $fx |xargs -P 4 -i $lf_user_wheel chmod $ans {}
    lf -remote 'send reload'
fi
```

**Advantages**:
- **Lumesh**: Pipe operator `|>` syntax is modern, loop processing is intuitive
- **Bash**: xargs parallel processing performs better, `-P 4` supports multiple processes

### 33. chown Command
**Key Binding**: `co`, `cO` (recursive)

**Comparison**:
- **Lumesh**: Uses pipe operator
```bash
let ans = read "new Owner:Group :"
if $ans {
    $fx |> $lf_user_wheel chown $argv $ans -- _
    lf -remote 'send reload'
}
```

- **Bash**: Uses traditional for loops
```bash
printf "\nnew Owner:Group : "
read ans
if [ -n "$ans" ]; then
    set -f
    for file in "$fx"
    do
        $lf_user_wheel chown $@ $ans $file
    done
    lf -remote 'send reload'
fi
```

**Advantages**:
- **Lumesh**: Pipe operator uniformly processes, syntax is consistent
- **Bash**: for loop control is precise, error handling is more granular

### 34. mkfile Command
**Key Binding**: `mf`

**Comparison**:
- **Lumesh**: Uses conditional statements and loops
```bash
if len($argv)>0 {
    $lf_user_wheel touch -- $argv
    for file in $argv{
        lf -remote `send $id select $file; tag '+'`
    }
}
```

- **Bash**: Uses parameter checking
```bash
if [ -n "$@" ];then
    $lf_user_wheel touch -- "$@";
    lf -remote "send $id select $@"
fi
```

**Advantages**:
- **Lumesh**: `len()` function semantics are clear, loop processes each file
- **Bash**: Parameter checking is concise, batch selection is efficient

### 35. mkdirs Command
**Key Binding**: `mk`

**Comparison**:
- **Lumesh**: Uses string methods and conditional statements
```bash
if $argv {
    $lf_user_wheel mkdir -p -- $argv
    let name = ""
    for file in $argv{
        if !$file.starts_with('/'){
            name = fs.base_name($file)
            lf -remote `send $id :select $name; tag '+'`
        }
    }
}
```

- **Bash**: Uses cut command to extract directory names
```bash
set -f
$lf_user_wheel mkdir -p -- "$@"
for file in "$@";do
    lf -remote "send $id :select $(echo $file| cut -d'/' -f1); tag +"
done
```

**Advantages**:
- **Lumesh**: String method `.starts_with()` is intuitive, path processing is type-safe
- **Bash**: cut command efficiently handles path splitting, set -f safely disables wildcard expansion

### 36. folder-selected Command
**Key Binding**: `ms`

**Comparison**:
- **Lumesh**: Uses built-in functions and error checking
```bash
let dest = read "Fold to :"
if $dest {
    if fs.exists($dest){
        eprint 'Dest already Exists'
        exit 0
    }
    $lf_user_wheel mkdir -- $dest
    let files = $fx | .lines()
    $lf_user_wheel mv -- $files $dest
    lf -remote `send $id select '$dest'`
}
```

- **Bash**: Uses printf and traditional tools
```bash
set -f
printf "Directory name: "
read newd
$lf_user_wheel mkdir -- "$newd"
$lf_user_wheel mv -- $fx "$newd"
lf -remote "send $id select \"$newd\""
```

**Advantages**:
- **Lumesh**: Built-in existence checking, more complete error handling
- **Bash**: Direct directory creation, simpler flow

### 37. Editor Launch Commands
**Key Binding**: `En` (geany), `Ec` (code), `Ep` (lapce), `Eg` (geany), `Ee` (gedit), `Ea` (apostrophe), `El` (lite-xl), `Em` (marker), `Er` (retext), `Ev` (vi), `Ez` (zed)

**Comparison**:
- **Lumesh**: Direct variable usage
```bash
&geany $fx
&code $fx
&lapce $fx
```

- **Bash**: Uses quotes for protection
```bash
&geany "$fx"
&code "$fx"
&lapce "$fx"
```

**Advantages**:
- **Lumesh**: Variable expansion automatically handles spaces
- **Bash**: Quote protection prevents argument splitting, safer

### 38. Terminal Launch Commands
**Key Binding**: `rr` (foot lf), `rt` (thunar), `rs` (spacefm), `rh` (hx), `rc` (code), `rp` (lapce), `rn` (geany), `rl` (lite-xl), `rz` (zed)

**Comparison**:
- **Lumesh**: Uses string literals
```bash
&$lf_user_wheel foot lf '.'
&thunar '.'
&hx '.'
```

- **Bash**: Also uses string literals
```bash
&$lf_user_wheel foot lf .
&thunar .
&hx .
```

**Advantages**:
- Both versions are basically the same, simple command calls

### 39. open-with-gui/cli Command
**Key Binding**: `Og` (GUI apps), `Oc` (CLI apps)

**Comparison**:
- **Lumesh**: Uses array indexing
```bash
&$argv[0] $fx    ## GUI app
$$argv[0] $fx    ## CLI app
```

- **Bash**: Uses positional parameters
```bash
&$@ $fx    ## GUI app
$$@ $fx    ## CLI app
```

**Advantages**:
- **Lumesh**: Array index explicitly specifies first parameter
- **Bash**: Positional parameter `$@` expands all parameters, more flexible

### 40. archive-mount Command
**Key Binding**: `am`

**Comparison**:
- **Lumesh**: Uses string interpolation and built-in functions
```bash
let base_name = fs.base_name($f)
let mntdir=`/tmp/lf/mount/$base_name`
mkdir -p $mntdir
$lf_user_wheel archivemount $f $mntdir -o nosave
lf -remote `send $id cd $mntdir`
```

- **Bash**: Uses command substitution
```bash
mntdir="/tmp/lf/mount/$(basename $f).mnt"
mkdir -p "$mntdir"
$lf_user_wheel archivemount "$f" "$mntdir" -o nosave
lf -remote "send $id cd $mntdir"
```

**Advantages**:
- **Lumesh**: Built-in file functions are type-safe, string interpolation is clear
- **Bash**: basename command is standard, adds .mnt extension to avoid conflicts

## Final Comparison Summary

### Syntax Modernization Level

| Syntax Feature | Lumesh | Bash | Modernization Level |
|--------|--------|------|------------|
| Conditional Expressions | `condition ? value : default` | `[ condition ] && value \|\| default` | Lumesh is more modern |
| String Methods | `.starts_with()`, `.ends_with()` | `grep`, `test` | Lumesh is more intuitive |
| File Operations | `fs.base_name()`, `fs.exists()` | `basename`, `test -e` | Lumesh is type-safe |
| Array Processing | `.lines()`, `.map()` | `awk`, `cut`, `sed` | Lumesh is functional |
| Error Handling | `?:`, `?.` | `&&`, `\|\|` | Lumesh is more concise |

### Performance and Resource Usage

| Aspect | Lumesh | Bash |
|--------|--------|------|
| Startup Speed | 7.40ms (lume version, includes repl and built-in libraries) | 4.78ms |
|      | 6.60ms (lumesh version, includes built-in libraries, excludes repl) |  |
| Memory Usage | 6MB | 8MB |
| Tool Dependency | Rich built-in functionality | Depends on system tools |

- String processing efficiency comparison
Taking the string processing in the all-cmds command as an example:

**Data Preparation**
```bash
lf -remote `query $id cmds` >> /tmp/cmds
```

**Lumesh Script**
```bash
# /tmp/cc.lm
for i in 0..100{
  fs.read /tmp/cmds | string.lines() | list.drop(1) | list.map(x -> {string.split("\t\t", $x) | list.first()}) | print
}
```

**Bash Script**
```bash
# /tmp/cc.lm
for ((i=0;i<=100;i++))
do
	cat /tmp/cmds | awk -F'\t' 'NR>1 {print $1}'
done
```

**Execution Time Comparison**
```bash
# Lume
> time lume /tmp/cc.lm
________________________________________________________
Executed in   32.25 millis    fish           external
   usr time   25.65 millis  859.00 micros   24.79 millis
   sys time    6.79 millis  588.00 micros    6.20 millis

# Bash
> time bash /tmp/cc.sh
________________________________________________________
Executed in  511.61 millis    fish           external
   usr time  230.11 millis    1.69 millis  228.42 millis
   sys time  454.95 millis    0.00 millis  454.95 millis

# Dash and Bash results are basically the same
```

### Maintainability and Readability

**Lumesh Advantages**:
- Unified API design and naming conventions
- Type safety reduces runtime errors
- Modern syntax improves code readability
- Built-in error handling mechanisms

**Bash Advantages**:
- Mature ecosystem and toolchain
- Extensive community support and documentation
- Standardized error handling patterns
- Excellent cross-platform compatibility

## Notes

Through complete comparative analysis of all LF configuration commands, it can be seen that:

1. **Functional Equivalence**: Both implementations can complete the same LF file manager tasks, with completely identical key bindings
2. **Syntax Differences**: Lumesh embodies the design philosophy of modern shell languages, while Bash maintains traditional Unix philosophy
3. **Selection Suggestions**:
   - Choose Lumesh: Pursue modern syntax, type safety, code readability
   - Choose Bash: Focus on compatibility, performance, mature tool ecosystem

Both implementations are excellent LF configuration solutions. The choice mainly depends on user's technical stack preference and specific needs.

Wiki pages you might want to explore:

- [Syntax overview (superiums/lumesh)](/zh-cn/glance)
- [Feature overview (superiums/lumesh)](/zh-cn/overview)
- [Daily usage examples (superiums/lumesh)](/zh-cn/cases)
- [Syntax demonstration for writing LF configuration files](/zh-cn/case_lf)
- [Syntax manual (superiums/lumesh)](/zh-cn/syntax)
- [Built-in functions (superiums/lumesh)](/zh-cn/doc/libs/)
