---
title: lf File Manager Configuration Comparison B  
date: 2025-07-15 10:16:45  
highlight: true  
tags:  
 - case  
categories:  
 - wiki  
 - case  
---

# lf File Manager Configuration Comparison B

### 10. extract-to Command  
**Key Binding**: `ah` (current directory), `ax` (/tmp/), `aX` (custom path)

**Code Comparison**:  
- **Lumesh**: Uses regex matching and built-in file functions  
```bash
if (Regex.match '\.([gb7xs]z|t[gbx]z|zip|zst|bz2|lz4|lzma|tar|rar|br)$' $f) {
    let base_name = Fs.base_name(True,$f).first()
    let npath = Fs.join($dest,$base_name)
    $lf_user_wheel ouch -q decompress --dir $npath $f
}
```

- **Bash**: Uses case statement and string processing  
```bash
case "$f" in
*.[gb7xs]z|*.t[gbx]z|*.zip|*.tar|*.bz2|*.lzma|*.lz4|*.zst|*.rar)
    $lf_user_wheel ouch d -qd $1 $f && \
    fn=$(basename "$f" | cut -d. -f1) && \
    lf -remote "send $id :cd $1; select $fn && tag ^"
    ;;
esac
```

**Advantages**:  
- **Lumesh**: Regex is more flexible, built-in path operation functions are type-safe  
- **Bash**: Case statement has better performance, pattern matching is concise and intuitive  

### 11. compress-to Command  
**Key Binding**: `ac` (/tmp/compress)

**Code Comparison**:  
- **Lumesh**: Uses string methods and conditional expressions  
```bash
if $dest.ends_with('/'){
    let base_name = Fs.base_name($sources.first())
    let dest_file = Fs.join($dest, $base_name)
}else{
    let base_name = Fs.base_name($dest)
    let dest_file = $dest
}
```

- **Bash**: Uses test command and string operations  
```bash
if test "$(echo $1 | grep '/$')" -o -d "$1" ; then
    name="$(basename -a $fx | head -n1)"
    dir=$(dirname $1$name)
else
    name=$(basename "$1")
    dir=$(dirname $1)
fi
```

**Advantages**:  
- **Lumesh**: String methods are more intuitive, conditional expressions are concise  
- **Bash**: Test command is standardized, dirname/basename tools are mature  

### 12. diff Series Commands  
**Key Binding**: `df` (diff), `dt` (delta), `dm` (md5 comparison)

**Code Comparison**:  
- **Lumesh**: Uses array indexing and ternary operator  
```bash
let files = $fs.lines()
if len($files)>1 {
    let lines = $lf_user_wheel md5sum $files[0] $files[1] | .lines()
    let s1 = $lines[0] | .words() | .at(0)
    let s2 = $lines[1] | .words() | .at(0)
    print $s1==$s2 ? 'Same' : 'Differ'
}
```

- **Bash**: Uses positional parameters and conditional checks  
```bash
set -- $fs
if [ "$#" -gt 1 ]; then
    sum1=$(md5sum $1 |cut -d' ' -f1)
    sum2=$(md5sum $2 |cut -d' ' -f1)
    if [ "$sum1" = "$sum2" ]; then
        echo 'Same'
    else
        echo 'Differ'
    fi
fi
```

**Advantages**:  
- **Lumesh**: Array operations are intuitive, method chaining is concise, ternary operator is elegant, conditional statements are more straightforward  
- **Bash**: Positional parameters are flexible, cut command is efficient, conditional checks are clear  

### 13. check-sum Command  
**Key Binding**: `dc`

**Code Comparison**:  
- **Lumesh**: Uses pattern matching  
```bash
let ext_name = Fs.base_name(True, $fx).last()
match $ext_name {
    sha512 => sha512sum -c $fx
    sha256 => sha256sum -c $fx
    sha1 => sha1sum -c $fx
    md5 => md5sum -c $fx
    _ => shasum $fx
}
```

- **Bash**: Uses case statement  
```bash
case "$fx" in
*.sha256) sha256sum -c "$fx" ;;
*.sha512) sha512sum -c "$fx" ;;
*.sha1) sha1sum -c "$fx" ;;
*.md5) md5sum -c "$fx" ;;
*) sha256sum "$fx" ;;
esac
```

**Advantages**:  
- **Lumesh**: Powerful pattern matching, type-safe file extension extraction. Also supports regex and wildcard patterns.  
- **Bash**: Case statement has excellent performance, wildcard matching is simple and direct  

### 14. cmus-play Command  
**Key Binding**: `Om`

**Code Comparison**:  
- **Lumesh**: Uses conditional expressions and error handling  
```bash
pgrep -x cmus ?: foot cmus
cmus-remote -c -q $fx
cmus-remote -p -q
```

- **Bash**: Uses conditional checks and logical operators  
```bash
if [ -z "$(pgrep -x cmus)" ]; then
    foot cmus && cmus-remote -c -q "$fx" && cmus-remote -p
else
    cmus-remote -c -q "$fx"
    cmus-remote -p
fi
```

**Advantages**:  
- **Lumesh**: Error handling operator `?:` is concise, code is more compact  
- **Bash**: Conditional logic is clear, logical operator `&&` allows for standard chaining  

### 15. umount-dev Command  
**Key Binding**: `mu`

**Code Comparison**:  
- **Lumesh**: Uses structured data and optional chaining  
```bash
let sel = lsblk -rno 'name,type,size,mountpoint,label,fstype' | Into.table([name,'type',size,mountpoint,label,fstype]) \
| where(mountpoint != None)
| Ui.pick() ?.
if sel {
    if $PWD ~: $sel.mountpoint {
        lf -remote `send $id cd /tmp`
    }
    $lf_user_wheel umount $sel.mountpoint
}
```

- **Bash**: Uses awk and string processing  
```bash
x=$(mount | awk '$1 ~ /^\/dev/ && $3 !~/^\/(home|boot|var)?$/ {sub(/^\/dev\//, "", $1); print $1,$5,$3}' | fzf --prompt='choose to UMount: ' --preview='' | awk '{print $3}')
if [ -n "$x" ]; then
    [ -n $(echo $PWD | grep "^$x/") ] && dir=$(dirname $x) && lf -remote "send $id cd $dir"
    $lf_user_wheel umount "$x"
fi
```

**Advantages**:  
- **Lumesh**: Powerful structured data processing, `?.` elegantly ignores unselected errors, field access is type-safe  
- **Bash**: Awk regex processing is flexible, pipeline combinations are efficient, string matching is mature  

### 16. on-cd Command  
**Key Binding**: None (automatically triggered)

**Code Comparison**:  
- **Lumesh**: Uses system functions  
```bash
Sys.print_tty `\033]0;lf $PWD\007`
```

- **Bash**: Uses printf redirection  
```bash
printf "\033]0;lf $PWD\007" > /dev/tty
```

**Advantages**:  
- **Lumesh**: System function encapsulation is safer, API is clearer  
- **Bash**: Directly operating device files allows for more precise control  

### 17. drag Series Commands  
**Key Binding**: `di` (drag in), `do` (drag out)

**Code Comparison**:  
- **Lumesh**: Uses built-in file functions  
```bash
dest=dragon-drop --target -x -p
cp $dest .
let base_name = Fs.base_name($dest)
lf -remote `send $id :select ${base_name}; tag =`
```

- **Bash**: Uses basename command  
```bash
dest=$(dragon-drop --target -x -p)
cp $dest .
lf -remote "send $id :select $(basename "$dest"); tag ="
```

**Advantages**:  
- **Lumesh**: Built-in file functions are type-safe, variable scope is clear  
- **Bash**: Basename command is standardized, command substitution is direct  

## Complete Comparison Summary

### Syntax Feature Comparison

| Feature | Lumesh | Bash |
|---------|--------|------|
| Conditional Expression | `condition ? true_val : false_val` | `[ condition ] && true_cmd || false_cmd` |
| Pattern Matching | `match expr { pattern => action }` | `case expr in pattern) action ;;` |
| Array Operations | `.lines()`, `.map()`, `.filter()` | `awk`, `cut`, `sed` |
| Error Handling | `cmd ?: default` | `cmd || default` |
| String Methods | `.split()`, `.join()`, `.ends_with()` | `cut`, `grep`, `test` |
| File Operations | `Fs.base_name()`, `Fs.join()` | `basename`, `dirname` |

Continue reading:  
- [lf Configuration File Comparison (lumesh vs bash) C](lf_3)
