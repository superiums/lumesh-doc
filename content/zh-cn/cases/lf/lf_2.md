---
title: lf文件管理器配置对比 B
date: 2025-07-15 10:16:45
highlight: true
tags:
 - case
categories:
 - wiki
 - case
---

# lf filemanager 配置对比 B

### 10. extract-to 命令
**按键绑定**: `ah` (当前目录), `ax` (/tmp/), `aX` (自定义路径)

**写法对比**:
- **lumesh**: 使用正则匹配和内置文件函数
```bash
if (Regex.match '\.([gb7xs]z|t[gbx]z|zip|zst|bz2|lz4|lzma|tar|rar|br)$' $f) {
    let base_name = Fs.base_name(True,$f).first()
    let npath = Fs.join($dest,$base_name)
    $lf_user_wheel ouch -q decompress --dir $npath $f
}
```

- **bash**: 使用case语句和字符串处理
```bash
case "$f" in
*.[gb7xs]z|*.t[gbx]z|*.zip|*.tar|*.bz2|*.lzma|*.lz4|*.zst|*.rar)
    $lf_user_wheel ouch d -qd $1 $f && \
    fn=$(basename "$f" | cut -d. -f1) && \
    lf -remote "send $id :cd $1; select $fn && tag ^"
    ;;
esac
```

**优势说明**:
- **lumesh**: 正则表达式更灵活，内置路径操作函数类型安全
- **bash**: case语句性能更好，模式匹配简洁直观

### 11. compress-to 命令
**按键绑定**: `ac` (/tmp/压缩)

**写法对比**:
- **lumesh**: 使用字符串方法和条件表达式
```bash
if $dest.ends_with('/'){
    let base_name = Fs.base_name($sources.first())
    let dest_file = Fs.join($dest, $base_name)
}else{
    let base_name = Fs.base_name($dest)
    let dest_file = $dest
}
```

- **bash**: 使用test命令和字符串操作
```bash
if test "$(echo $1 | grep '/$')" -o -d "$1" ; then
    name="$(basename -a $fx | head -n1)"
    dir=$(dirname $1$name)
else
    name=$(basename "$1")
    dir=$(dirname $1)
fi
```

**优势说明**:
- **lumesh**: 字符串方法更直观，条件表达式简洁
- **bash**: test命令标准化，dirname/basename工具成熟

### 12. diff系列命令
**按键绑定**: `df` (diff), `dt` (delta), `dm` (md5对比)

**写法对比**:
- **lumesh**: 使用数组索引和三元运算符
```bash
let files = $fs.lines()
if len($files)>1 {
    let lines = $lf_user_wheel md5sum $files[0] $files[1] | .lines()
    let s1 = $lines[0] | .words() | .at(0)
    let s2 = $lines[1] | .words() | .at(0)
    print $s1==$s2 ? 'Same' : 'Differ'
}
```

- **bash**: 使用位置参数和条件判断
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

**优势说明**:
- **lumesh**: 数组操作直观，链式调用简洁，三元运算符优雅, 条件语句更直观简洁
- **bash**: 位置参数灵活，cut命令高效，条件判断清晰

### 13. check-sum 命令
**按键绑定**: `dc`

**写法对比**:
- **lumesh**: 使用模式匹配
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

- **bash**: 使用case语句
```bash
case "$fx" in
*.sha256) sha256sum -c "$fx" ;;
*.sha512) sha512sum -c "$fx" ;;
*.sha1) sha1sum -c "$fx" ;;
*.md5) md5sum -c "$fx" ;;
*) sha256sum "$fx" ;;
esac
```

**优势说明**:
- **lumesh**: 模式匹配功能强大，文件扩展名提取类型安全。也支持正则和通配符模式。
- **bash**: case语句性能优秀，通配符匹配简单直接

### 14. cmus-play 命令
**按键绑定**: `Om`

**写法对比**:
- **lumesh**: 使用条件表达式和错误处理
```bash
pgrep -x cmus ?: foot cmus
cmus-remote -c -q $fx
cmus-remote -p -q
```

- **bash**: 使用条件判断和逻辑运算符
```bash
if [ -z "$(pgrep -x cmus)" ]; then
    foot cmus && cmus-remote -c -q "$fx" && cmus-remote -p
else
    cmus-remote -c -q "$fx"
    cmus-remote -p
fi
```

**优势说明**:
- **lumesh**: 错误处理运算符`?:`简洁，代码更紧凑
- **bash**: 条件逻辑清晰，逻辑运算符`&&`链式调用标准

### 15. umount-dev 命令
**按键绑定**: `mu`

**写法对比**:
- **lumesh**: 使用结构化数据和可选链
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

- **bash**: 使用awk和字符串处理
```bash
x=$(mount | awk '$1 ~ /^\/dev/ && $3 !~/^\/(home|boot|var)?$/ {sub(/^\/dev\//, "", $1); print $1,$5,$3}' | fzf --prompt='choose to UMount: ' --preview='' | awk '{print $3}')
if [ -n "$x" ]; then
    [ -n $(echo $PWD | grep "^$x/") ] && dir=$(dirname $x) && lf -remote "send $id cd $dir"
    $lf_user_wheel umount "$x"
fi
```

**优势说明**:
- **lumesh**: 结构化数据处理强大，`?.`优雅忽略未选择错误，字段访问类型安全
- **bash**: awk正则处理灵活，管道组合高效，字符串匹配成熟

### 16. on-cd 命令
**按键绑定**: 无（自动触发）

**写法对比**:
- **lumesh**: 使用系统函数
```bash
Sys.print_tty `\033]0;lf $PWD\007`
```

- **bash**: 使用printf重定向
```bash
printf "\033]0;lf $PWD\007" > /dev/tty
```

**优势说明**:
- **lumesh**: 系统函数封装更安全，API更清晰
- **bash**: 直接操作设备文件，控制更精确

### 17. drag系列命令
**按键绑定**: `di` (拖入), `do` (拖出)

**写法对比**:
- **lumesh**: 使用内置文件函数
```bash
dest=dragon-drop --target -x -p
cp $dest .
let base_name = Fs.base_name($dest)
lf -remote `send $id :select ${base_name}; tag =`
```

- **bash**: 使用basename命令
```bash
dest=$(dragon-drop --target -x -p)
cp $dest .
lf -remote "send $id :select $(basename "$dest"); tag ="
```

**优势说明**:
- **lumesh**: 内置文件函数类型安全，变量作用域清晰
- **bash**: basename命令标准化，命令替换直接

## 完整对比总结

### 语法特性对比

| 特性 | Lumesh | Bash |
|------|--------|------|
| 条件表达式 | `condition ? true_val : false_val` | `[ condition ] && true_cmd \|\| false_cmd` |
| 模式匹配 | `match expr { pattern => action }` | `case expr in pattern) action ;;` |
| 数组操作 | `.lines()`, `.map()`, `.filter()` | `awk`, `cut`, `sed` |
| 错误处理 | `cmd ?: default` | `cmd \|\| default` |
| 字符串方法 | `.split()`, `.join()`, `.ends_with()` | `cut`, `grep`, `test` |
| 文件操作 | `Fs.base_name()`, `Fs.join()` | `basename`, `dirname` |

继续阅读：
- [lf配置文件对比(lumesh vs bash) C](/zh-cn/cases/lf_3)
