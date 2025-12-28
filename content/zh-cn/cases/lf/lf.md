---
title: lf文件管理器配置对比(lumesh vs bash)
date: 2025-07-15 10:16:45
highlight: true
tags:
 - case
categories:
 - wiki
 - case
---

# lf filemanager 配置对比 A

**lumesh版本**使用现代化的语法和内置函数，而**bash版本**使用传统的shell语法和外部工具。
- [完整的配置文件 lume](/zh-cn/cases/lfrc_lm)
- [完整的配置文件 dash](/zh-cn/cases/lfrc_sh)

## 概览


### 通用功能命令
- `all-cmd`, `history-cmd`, `history-dir` - 命令历史和选择
- `toggle-preview`, `toggle-selmode`, `toggle-super` - 界面切换
- `zox/z`, `zoxide-query`, `cd-usermedia` - 目录导航

### 文件操作命令
- `select-files` 系列 - 文件选择和过滤
- `yank-path`, `yank-name`, `yank-basename` - 复制操作
- `delete`, `trash`, `paste/mpaste`, `link` - 文件管理
- `rename-to`, `bulk-rename` - 重命名操作
- `chmod`, `chown`, `mkfile`, `mkdirs` - 权限和创建

### 搜索和预览命令
- `fzf-edit`, `fzf-file`, `fzf-folder`, `fzf-content` - 模糊搜索
- `filter` 系列 - 文件过滤

### 压缩和挂载命令
- `extract-to`, `compress-to`, `archive-mount` - 压缩包处理
- `mount-dev`, `umount-dev` - 设备挂载

### 比较和校验命令
- `diff`, `delta`, `diff-md5`, `check-sum` - 文件比较

### 外部程序集成
- `cmus-play`, `open-handlr`, `open-with-gui/cli` - 程序启动
- `drag-in`, `drag-out` - 拖拽操作
- 编辑器启动命令 (`En`, `Ec`, `Ep`等)

### 系统命令
- `on-cd` - 自动触发命令


两种实现在功能上完全等价，按键绑定完全一致，选择主要取决于用户对现代化语法的偏好程度和对传统Unix工具的依赖程度。

## 在lf中启用shell

- 在lf中启用Lumesh
```bash
set shell lumesh      # 必须
set shellopts '-s'    # 可选
set ifs "\n"          # 可选
set filesep "\n"      # 可选
```

- 在lf中启用bash
```bash
set shell bash        # 必须
set shellopts '-eu'   # 可选
set ifs "\n"          # 可选
set filesep "\n"      # 可选
```

## 主要命令对比A

### 1. all-cmd 命令
**按键绑定**: `<c-e>`

**写法对比**:
- **lumesh**: 使用链式管道方法和内置函数
```bash
let cmd = lf -remote `query $id cmds` | .lines() | .sort() | .drop(1) | .map(x -> {x.split("\t\t") | .first()}) | Ui.pick "select cmd:"
```

- **bash**: 使用管道和外部工具
```bash
cmd=$( lf -remote "query $id cmds" | awk -F'\t' 'NR > 1 { print $NF}' | sort -u | fzf --reverse --prompt='Execute command: ' --preview='' )
```

**优势说明**:
- **lumesh**: 语法更直观，链式调用易读，完全省去外部应用的启动时间和数据转换时间，内置`Ui.pick`提供统一交互体验
- **bash**: 使用标准Unix工具，兼容性好，`awk`处理文本更灵活

### 2. history-cmd 命令
**按键绑定**: `<backspace>`, `<backspace2>`

**写法对比**:
- **lumesh**:
```bash
let cmd = lf -remote `query $id history` | .lines() | .sort() | Ui.pick "history command:" | .split("\t\t") | .last()
```

- **bash**:
```bash
cmd=$( lf -remote "query $id history" | awk -F'\t' 'NR > 1 { print $NF}' | sort -u | fzf --reverse --prompt='Execute command: ' --preview='' )
```

**优势说明**:
- **lumesh**: 内置方法链更简洁，`.last()`语义清晰
- **bash**: `awk`的`$NF`高效处理最后一列

### 3. toggle-preview 命令
**按键绑定**: `zp`

**写法对比**:
- **lumesh**: 使用模式匹配
```bash
match $lf_preview {
    'true' => lf -remote `send $id :set nopreview; set ratios 1:5`
    _ => lf -remote `send $id :set preview; set ratios 1:2:3`
}
```

- **bash**: 使用条件判断
```bash
if [ "$lf_preview" = "true" ]; then
    lf -remote "send $id :set nopreview; set ratios 1:5"
else
    lf -remote "send $id :set preview; set ratios 1:2:3"
fi
```

**优势说明**:
- **lumesh**: `match`语法更现代化，模式匹配功能强大; 变量无须引号
- **bash**: 传统`if-else`结构清晰，调试容易

### 4. select-files 命令
**按键绑定**: `Sf` (文件), `Sd` (目录), `SF` (空文件), `SD` (空目录), `Sl` (符号链接), `Sx` (可执行文件)

**写法对比**:
- **lumesh**: 使用三元运算符和内置函数
```bash
let htag= $lf_hidden ? '-H' : ''
let r=fd --exact-depth 1 $argv $htag -c never -j 1 | .lines() | .join(' ')
```

- **bash**: 使用函数和条件判断
```bash
get_files() {
    if [ "$lf_hidden" = 'false' ]; then
        fd --exact-depth 1 $@ -c never -j 1 -0
    else
        fd --exact-depth 1 $@ -H -c never -j 1 -0
    fi | xargs -0 printf ' %q'
}
```

**优势说明**:
- **lumesh**: 三元运算符简洁，变量作用域清晰
- **bash**: 函数封装逻辑清晰，可重用性好

### 5. fzf-content 命令
**按键绑定**: `fc<space>`, `fct` (txt), `fcm` (md), `fcs` (sh), `fcy` (py), `fcj` (js)

**写法对比**:
- **lumesh**: 使用现代语法和字符串插值
```bash
let file_type = len($argv)>0 ? $argv[0] : 'sh'
let RG_PREFIX = `$lf_user_wheel rg --type $file_type --column --line-number --no-heading --color=always --smart-case --max-filesize 50K`
if $res {
    let a = $res.split(':').take(3).join(':')
    $lf_user_wheel hx $a
}
```

- **bash**: 使用传统shell语法
```bash
RG_PREFIX="$lf_user_wheel rg --column --line-number --no-heading --color=always --smart-case --max-filesize 50K"
[ -n ${1:-''} ] && RG_PREFIX="$RG_PREFIX --type $1"
[ -n "$res" ] && $lf_user_wheel hx $(echo $res|cut -d: -f1) +$(echo $res|cut -d: -f2)
```

**优势说明**:
- **lumesh**: 字符串方法链更直观，条件表达式简洁
- **bash**: 参数展开灵活，`cut`命令处理分隔符高效

### 6. yank系列命令
**按键绑定**: `yp` (路径), `yn` (文件名), `yb` (基础名), `yu` (清除)

**写法对比**:
- **lumesh**: 使用函数式编程风格
```bash
$fx.lines() | .map(Fs.base_name) | .join("\n") | wl-copy
$fx.lines() | .map(x -> {Fs.base_name(True,$x) | .first()}) | .join("\n") | wl-copy
```

- **bash**: 使用传统Unix工具
```bash
basename -a -- "$fx" | head -c-1 | wl-copy
echo $fx | tr ' ' '\n' | wl-copy
basename -a -- "$fx" | cut -d. -f1 | head -c-1 | wl-copy
```

**优势说明**:
- **lumesh**: 函数式风格一致，内置文件系统函数功能丰富
- **bash**: Unix工具成熟稳定，`basename`、`cut`等工具专业高效

### 7. paste/mpaste 命令
**按键绑定**: `pp` (粘贴), `po` (强制), `pb` (备份), `pO` (强制覆盖)

**写法对比**:
- **lumesh**: 使用现代集合操作
```bash
let load=Fs.read ~/.local/share/lf/files | .lines()
let mode=$load.at(0)
let files = $load.drop(1)
let base_names = $files.map(Fs.base_name)
match $mode {
    copy => { $lf_user_wheel cp -r $argv -- $files '.' }
    move => { $lf_user_wheel mv -- $files '.' }
}
```

- **bash**: 使用传统文本处理
```bash
load=$(cat ~/.local/share/lf/files)
mode=$(echo "$load" | sed -n '1p')
list=$(echo "$load" | sed '1d')
fn=$(basename -a -- $list)
if [ "$mode" = 'copy' ]; then
    $lf_user_wheel cp -r $@ -- $list .
elif [ "$mode" = 'move' ]; then
    $lf_user_wheel mv -- $list .
fi
```

**优势说明**:
- **lumesh**: 集合操作直观，模式匹配优雅，内置文件函数类型安全
- **bash**: `sed`文本处理强大，条件分支清晰

### 8. bulk-rename 命令
**按键绑定**: `cb`

**写法对比**:
- **lumesh**: 使用现代数据结构
```bash
let old_files = $fs.lines()
let new_files = Fs.read $new | .lines()
for pair in List.zip($old_files,$new_files){
    if $pair[0] != $pair[1]{
        $lf_user_wheel mv -- $pair[0] $pair[1]
    }
}
```

- **bash**: 使用传统文本处理
```bash
paste "$old" "$new" | while IFS= read -r names; do
    src="$(printf '%s' "$names" | cut -f1)"
    dst="$(printf '%s' "$names" | cut -f2)"
    if [ "$src" = "$dst" ] || [ -e "$dst" ]; then
        continue
    fi
    $lf_user_wheel mv -- "$src" "$dst"
done
```

**优势说明**:
- **lumesh**: `List.zip`函数式操作优雅，数组访问直观
- **bash**: `paste`命令专业，管道处理内存效率高

### 9. mount-dev 命令
**按键绑定**: `mm`

**写法对比**:
- **lumesh**: 使用结构化数据处理
```bash
let sel = lsblk -rno 'name,type,size,mountpoint,label,fstype' | Into.table([name,'type',size,mountpoint,label,fstype])
| where($type!='disk' && !$mountpoint && $fstype !~: 'member')
| Ui.pick "which to mount:"
let src = $sel.type == 'part' ? `/dev/${$sel.name}` : `/dev/mapper/${$sel.name}`
```

- **bash**: 使用文本处理和字段提取
```bash
sel=$(lsblk -rno 'name,type,size,label,mountpoint,fstype' |
awk -F'[ ]' '$2!="disk" && $5=="" && $6!~/member/ { print $1,$2,$3,$4 }' |
fzf --prompt='choose to Mount: ' --preview='')
x=$(echo "$sel" | cut -d' ' -f1)
typ=$(echo $sel | cut -d' ' -f2)
```

**优势说明**:
- **lumesh**: 结构化数据处理强大，字段访问类型安全，`where`过滤直观
- **bash**: `awk`文本处理灵活高效，字段提取成熟稳定

## 总体优势对比

**Lumesh优势**:
- 现代化语法，链式调用直观
- 内置函数丰富，类型安全
- 函数式编程风格一致
- 结构化数据处理能力强
- 调试和错误处理机制更完善
- 可尽量减少对三方工具的依赖，节省资源

**Bash优势**:
- Unix工具生态成熟
- 兼容性和可移植性好
- 三方文本处理工具专业高效

## Notes

两种实现都实现了相同的lf文件管理器功能，按键绑定完全一致。Lumesh版本展现了现代shell语言的优势，而bash版本体现了传统Unix哲学的稳定性。选择哪种主要取决于用户对语法风格的偏好和对工具生态的依赖程度。

阅读更多
- [lf配置文件对比(lumesh vs bash) B](/zh-cn/cases/lf_2)
