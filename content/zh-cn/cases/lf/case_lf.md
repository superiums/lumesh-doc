---
title: Lumesh 实用举例之 lf文件管理器
date: 2025-07-05 19:16:45
highlight: true
tags:
 - glance
categories:
 - wiki
 - why
 - syntax
---

## 使用Lumesh编写lf配置文件的语法演示

[lf 文件管理器](https://github.com/gokcehan/lf)是一个非常强大的TUI文件管理器，支持高度灵活的自定义操作。是我最喜欢的文件管理器。以下是用Lumesh为lf编写的命令配置的部分演示。

[完整的配置文件](/data/lfrc_lm)

### 1. 变量定义和管道操作

```bash
cmd all-cmd ${{
    let cmd = lf -remote `query $id cmds` | .lines() | .drop(1) | \
        .map(x -> {x.split("\t\t", $x) | .first()}) | Ui.pick "select cmd:"
    lf -remote `send $id :$cmd`
}}
```

- ` `lf -remote `query $id cmds`` ` 获取lf可用命令
- `let` 变量定义
- 管道操作符 `|` 用于数据流处理
- `.lines()` 字符串处理方法
- `.drop(1)` 列表操作，跳过第一个元素
- `.map()` 函数式编程，使用lambda表达式 `x -> {...}`
- `Ui.pick` 交互式选择器

### 2. 字符串处理和表格操作

```bash
cmd history-dir ${{
  let hist = lf -remote `query $id jumps` | Into.table('jump','path') | .drop(1) | Ui.pick "choose history:"
  lf --remote `send $id cd ${$hist.path}`
}}
```

- ` lf -remote `query $id jumps` ` 获取lf的历史目录
- `Into.table()` 将数据转换为表格结构
- 字符串插值 `${$hist.path}` 语法
- 对象属性访问 `$hist.path`

### 3. 条件语句和模式匹配

```bash
cmd toggle-preview %{{
    match $lf_preview {
        true => lf -remote `send $id :set nopreview; set ratios 1:5`
        _ => lf -remote `send $id :set preview; set ratios 1:2:3`
    }
}}
```

Lumesh的`match`模式匹配语法，类似Rust的match表达式。
这里匹配的是`true`字符串，如需bool值应大写首字母`True`

### 4. 条件表达式和三元操作

```bash
cmd select-files &{{
    let htag= $lf_hidden ? '-H' : ''
    let r=fd --exact-depth 1 $argv $htag -c never -j 1 -0 |  xargs -0 printf ' %q'
    lf -remote `send $id :unselect; toggle $r`
}}
```


- 三元条件操作符 `condition ? value1 : value2`
- 变量在命令行中的使用： `$argv`为函数接收到的参数

### 5. 函数式编程和列表操作

```bash
cmd yank-name &{{
    $fx.lines() | .map(Fs.base_name) | .join("\n") | wl-copy
}}
```

- 方法链式调用 `.lines().map().join()`
- `Fs.base_name` 文件系统模块函数
- 管道操作将结果传递给外部命令

### 6. 用户交互

```bash
cmd delete ${{
  println '=====DELETE=====' $fx '================'
  if Ui.confirm('Delete these files [y/n]:'){
    $lf_user_wheel rm -rf $fx.lines()
  }
}}
```

- `println` 输出函数
- `Ui.confirm` 确认对话框
- `if` 条件语句

### 7. 复杂的数据处理和循环

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

- `Fs.read` 文件读取
- `len()` 长度函数
- `read` 用户输入函数
- `for` 循环语句
- 复杂的match模式匹配

### 8. 正则表达式和字符串操作

```bash
cmd extract-to %{{
    let dest = $argv[0] ?: {print 'Cancelled'; exit 0}
    if (Regex.match '\.([gb7xs]z|t[gbx]z|zip|zst|bz2|lz4|lzma|tar|rar|br)$' $f) {
        $lf_user_wheel ouch -q decompress --dir $dest $f
        let base_name = Fs.base_name($f)
        lf -remote `send $id :cd $dest; select $base_name; tag '^'`
    }else{
        print 'Unsupported file extention'
    }
}}
```


- 空值合并操作符 `?:`
- `Regex.match` 正则表达式匹配
- 块表达式 `{print 'Cancelled'; exit 0}`

## Notes

这个配置文件完美展示了Lumesh作为shell脚本语言的强大功能：结合了现代编程语言的语法特性（如模式匹配、函数式编程、错误处理）与传统shell的命令执行能力。Lumesh的设计理念"像Python/JS一样编写，像bash一样工作"在这个实例中得到了充分体现。

Wiki pages you might want to explore:
- [完整的lf配置文件](/data/lfrc)
- [语法概览 (superiums/lumesh)](/zh-cn/glance)
- [特性概览 (superiums/lumesh)](/zh-cn/feature)
- [应用举例 (superiums/lumesh)](/zh-cn/cases)
- [语法手册 (superiums/lumesh)](/zh-cn/doc/syntax)
- [内置函数 (superiums/lumesh)](/zh-cn/doc/libs/)
