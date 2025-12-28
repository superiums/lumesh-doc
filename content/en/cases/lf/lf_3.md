---
title: lf File Manager Configuration Comparison C  
date: 2025-07-15 10:16:45  
highlight: true  
tags:  
 - case  
categories:  
 - wiki  
 - case  
---

# lf File Manager Configuration Comparison C

### 18. profile Command  
**Key Binding**: `z2` (extra), `z3` (disk), `z4` (convert), `z5` (develop), `z6` (auto-redraw), `z7` (tarzip)

**Code Comparison**:  
- **Lumesh**: Uses string interpolation and array access  
```bash
  lf -remote `send $id source ~/.config/lf/profiles/${$argv[0]}`
```

- **Bash**:  
```bash
  lf -remote "send $id source ~/.config/lf/profiles/$1"
```

**Advantages**:  
- **Lumesh**: Supports configuration file switching, string interpolation syntax is concise  
- **Bash**: String interpolation syntax is straightforward  

### 19. zox/z Command (zoxide Integration)  
**Key Binding**: `;` (push :zox<space>), `gz` (push :zox<space>)

**Code Comparison**:  
- **Lumesh**: Uses conditional expressions and function calls  
```bash
if len($argv) {
    let select=zoxide query --exclude (pwd()) $argv ?: lf -remote "send $id echo Cancelled."
    lf -remote `send $id cd $select`
}
```

- **Bash**: Uses conditional checks and command substitution  
```bash
if [ -n "$@" ]; then
    select="$(zoxide query --exclude $PWD $@)" \
    && lf -remote "send $id cd $select" \
    || lf -remote "send $id echo Cancelled."
fi
```

**Advantages**:  
- **Lumesh**: `len()` function is clear in semantics, `pwd()` function call is intuitive  
- **Bash**: Logical operators allow for standard chaining, error handling is explicit  

### 20. cd-usermedia Command  
**Key Binding**: `gi`

**Code Comparison**:  
- **Lumesh**: Uses environment variables  
```bash
lf -remote `send $id cd $XDG_RUNTIME_DIR/media`
# or
lf -remote `send $id cd /run/user/${id -u}/media`
```

- **Bash**: Uses command substitution  
```bash
lf -remote "send $id cd /run/user/$(id -u)/media"
```

**Advantages**:  
- **Lumesh**: Directly uses environment variables, more compliant with XDG specifications  
- **Bash**: Dynamically retrieves user ID, better compatibility  

### 21. follow-link Command  
**Key Binding**: `gL`

**Code Comparison**:  
- **Lumesh**: Uses variable assignment  
```bash
lf -remote `send $id select ${readlink $f}`
```

- **Bash**: Uses command substitution  
```bash
lf -remote "send ${id} select '$(readlink $f)'"
```

**Advantages**:  
- **Lumesh**: Variable assignment syntax is clear and readable  
- **Bash**: Inline command substitution is compact, reducing variable usage  

### 22. fzf-edit Command  
**Key Binding**: `fe`

**Code Comparison**:  
- **Lumesh**: Simple command invocation  
```bash
hx (fzf)
```

- **Bash**: Uses command substitution  
```bash
hx $(fzf)
```

**Advantages**:  
- **Lumesh**: Parentheses syntax is more modern  
- **Bash**: Traditional command substitution syntax is standard  

### 23. fzf-file Command  
**Key Binding**: `ff<space>`, `fft` (txt), `ffg` (gz), `ffm` (md), `ffs` (sh), `ffy` (py)

**Code Comparison**:  
- **Lumesh**: Uses ternary operator and string concatenation  
```bash
let ext = len($argv) ? '-e'+$argv[0] : ''
let selected = fd --type 'file' $ext '-S-50k' | fzf --preview "~/.config/lf/previewer {} 30 18"
```

- **Bash**: Uses conditional checks and variable assignment  
```bash
[ -n ${1:-''} ] && E="-e$1" || E='-S-50k'
select=$($lf_user_wheel fd --type f $E | fzf --preview "~/.config/lf/previewer {} 30 18")
```

**Advantages**:  
- **Lumesh**: Ternary operator is concise, string concatenation is intuitive  
- **Bash**: Parameter expansion syntax is flexible, logical operators are standard  

### 24. fzf-folder Command  
**Key Binding**: `fd`

**Code Comparison**:  
- **Lumesh**: Uses variable assignment and conditional checks  
```bash
select = $lf_user_wheel fd --type d '.' -d 5 | fzf --preview 'ls {}'
if $select {
    lf -remote `send $id cd $select`
}
```

- **Bash**: Uses command substitution and logical operators  
```bash
select=$($lf_user_wheel fd --type d . -d 5 | fzf --preview 'ls {}') \
&& lf -remote "send $id cd $select" \
|| lf -remote "send $id echo Cancelled."
```

**Advantages**:  
- **Lumesh**: Variable assignment syntax is clear, no explicit error handling needed  
- **Bash**: Logical operators allow for standard chaining, error handling is explicit  

### 25. filter Series Commands  
**Key Binding**: `\\` (filter), `F<space>` (filter), `Ft` (.txt), `Fp` (.png), `Fj` (.jpg), `Fa` (.mp3), `Fv` (.mp4), `Fw` (.docx), `Fx` (.xlsx), `Fg` (.gz), `Fz` (.zip), `Fm` (.md), `Fs` (.sh), `Fy` (.py), `Fc` (clear)

**Code Comparison**:  
- **Code is consistent across both implementations.**

### 26. delete Command  
**Key Binding**: `dD`

**Code Comparison**:  
- **Lumesh**: Uses modern output and confirmation  
```bash
println '=====DELETE====='.red().bold() $fx '================'.red()
if Ui.confirm('Delete these files [y/n]:'){
    $lf_user_wheel rm -rf $fx.lines()
}
```

- **Bash**: Uses traditional echo and read  
```bash
echo -e "=====\033[31mDELETE\033[0m====="
echo "$fx"
echo "================"
printf "Delete %d files? [y/N]" $(wc -w <<< $fx)
read ans
if [ "$ans" = "y" ]; then
    echo $fx | xargs $lf_user_wheel rm -rf
fi
```

**Advantages**:  
- **Lumesh**: Built-in color methods and confirmation functions, syntax is more modern  
- **Bash**: ANSI escape sequences allow for direct control, `wc` accurately counts files  

### 27. trash Command  
**Key Binding**: `dd`

**Code Comparison**:  
- **Lumesh**: Uses built-in functions and string interpolation  
```bash
let files = $fx.lines() | List.map(Fs.base_name)
let ans = read `Trash: $files [y/N]`
if $ans == 'y' {
    mkdir -p /tmp/.trash
    $lf_user_wheel mv -- $fx.lines() /tmp/.trash/
    print 'Trash complete!'
}
```

- **Bash**: Uses traditional tools and command substitution  
```bash
printf "Temporary Trash %d files? [y/N]" $(wc -w <<< $fx)
read ans
if [ "$ans" = "y" ]; then
    [ -d "/tmp/.trash" ] || mkdir -p /tmp/.trash
    echo $fx | xargs $lf_user_wheel rm -rf
    echo "Trash complete!"
fi
```

**Advantages**:  
- **Lumesh**: Functional handling of file lists, string interpolation displays file names  
- **Bash**: Accurate file count statistics, conditional directory creation is concise  

### 28. link Command  
**Key Binding**: `pL`

**Code Comparison**:  
- **Lumesh**: Uses filesystem functions and loops  
```bash
let load= Fs.read ~/.local/share/lf/files | .lines()
let files=$load.drop(1)
let base_names = $files.map(Fs.base_name)
for filex in $base_names{
    if (Fs.exists Fs.join('.',$filex)) {
        eprint $filex 'Already exists!'
        exit 1
    }
}
match $mode {
    copy => $lf_user_wheel ln -s -- $files '.'
    move => $lf_user_wheel ln -- $files '.'
}
```

- **Bash**: Uses sed and basename  
```bash
load=$(cat ~/.local/share/lf/files)
mode=$(echo "$load" | sed -n '1p')
list=$(echo "$load" | sed '1d')
if [ "$mode" = 'copy' ]; then
    $lf_user_wheel ln -s $list .
elif [ "$mode" = 'move' ]; then
    $lf_user_wheel ln $list .
fi
fn=$(basename -a -- $list)
for file in "$fn";do
    lf -remote "send $id :select $file; tag @"
done
```

**Advantages**:  
- **Lumesh**: Built-in filesystem functions are rich, existence checks are type-safe  
- **Bash**: Sed text processing is efficient, basename handles batch processing standard  

### 29. paste-rsync Command  
**Key Binding**: `pr`

**Code Comparison**:  
- **Lumesh**: Uses pattern matching  
```bash
let load= Fs.read ~/.local/share/lf/files | .lines()
let mode=$load.at(0)
let files=$load.drop(1)
match $mode{
    copy => {
        $lf_user_wheel rsync -ar --ignore-existing --info=progress2 -- $files '.'
    }
    move => {
        $lf_user_wheel rsync -ar --remove-source-files --ignore-existing --info=progress2 -- $files '.'
    }
}
```

- **Bash**: Uses case statement and pipeline processing  
```bash
set -- $(cat ~/.local/share/lf/files)
mode="${1:-}"
shift
case "$mode" in
copy) $lf_user_wheel rsync -av --ignore-existing --progress -- "$@" . | stdbuf -i0 -o0 -e0 tr '\r' '\n' | while IFS= read -r line; do
    lf -remote "send $id echo $line"
done ;;
move) $lf_user_wheel mv -n -- "$@" . ;;
esac
```

**Advantages**:  
- **Lumesh**: Modern syntax for pattern matching, array operations are intuitive  
- **Bash**: Positional parameter handling is flexible, real-time progress display is comprehensive  

### 30. paste-to/paste-from Command  
**Key Binding**: `pt` (paste-to), `pf` (paste-from)

**Code Comparison**:  
- **Lumesh**: Uses error handling operators and conditional checks  
```bash
let dest = $argv[0] ?: {print 'Cancelled';exit 0}
$lf_user_wheel cp -r --backup=numbered -i -- $fx $dest
if Fs.is_dir($dest){
    let base_names = $fx.lines() | .map(Fs.base_name) | .join("\n")
    lf -remote `send $id :unselect; cd $dest; select $base_names`
}
```

- **Bash**: Uses parameter expansion and conditional checks  
```bash
if [ -n "${1:-}" ]; then
    $lf_user_wheel cp -r --backup=numbered -i -- $fx $1
    [ -d "$1" ] \
    && lf -remote "send $id :unselect; cd $1; select $(basename $fx)" \
    || lf -remote "send $id :unselect; select $1; "
else
    echo "Cancelled."
fi
```

**Advantages**:  
- **Lumesh**: Error handling operators are concise, filesystem functions are type-safe  
- **Bash**: Parameter expansion is standard, logical operators allow for clear chaining  

## Complete Functionality Comparison Summary

### Advanced Feature Comparison

| Feature Category | Lumesh Features | Bash Features |
|------------------|-----------------|---------------|
| Error Handling    | `?:`, `?.`, `?!` operators | `&&`, `||` logical operators |
| Data Processing    | Chained calls, functional programming | Pipelines, text processing tools |
| File Operations    | Built-in Fs module, type safety | Standard Unix tools |
| User Interaction    | Ui.confirm, Ui.pick | read, fzf |
| String Processing    | Built-in methods, interpolation | cut, sed, awk |

### Code Maintainability

**Lumesh Advantages**:  
- Modern syntax improves readability  
- Type safety reduces runtime errors  
- Unified API design  
- Built-in error handling mechanisms  

**Bash Advantages**:  
- Extensive community support  
- Rich documentation resources  

## Notes

Through the complete comparison of all commands, it is evident that Lumesh has significant advantages in syntax modernization, type safety, and code readability, while Bash is more mature in compatibility. Both implementations can accomplish the same file management tasks, and the choice primarily depends on the user's preference for modern syntax versus reliance on traditional Unix tools.

Read more:  
- [lf Configuration File Comparison (lumesh vs bash) D](lf_4)
