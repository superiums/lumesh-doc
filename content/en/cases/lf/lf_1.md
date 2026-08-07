---
title: LF File Manager Configuration Comparison (Lumesh vs Bash)
date: 2025-07-15 10:16:45
highlight: true
tags:
 - case
categories:
 - wiki
 - case
---

LF filemanager configuration comparison A

**Lumesh version** uses modern syntax and built-in functions, while **Bash version** uses traditional shell syntax and external tools.
- [Complete config file lume](/zh-cn/cases/lfrc_lm)
- [Complete config file dash](/zh-cn/cases/lfrc_sh)

## Overview


### Common Command Functions
- `all-cmd`, `history-cmd`, `history-dir` - command history and selection
- `toggle-preview`, `toggle-selmode`, `toggle-super` - interface toggles
- `zox/z`, `zoxide-query`, `cd-usermedia` - directory navigation

### File Operation Commands
- `select-files` series - file selection and filtering
- `yank-path`, `yank-name`, `yank-basename` - copy operations
- `delete`, `trash`, `paste/mpaste`, `link` - file management
- `rename-to`, `bulk-rename` - rename operations
- `chmod`, `chown`, `mkfile`, `mkdirs` - permissions and creation

### Search and Preview Commands
- `fzf-edit`, `fzf-file`, `fzf-folder`, `fzf-content` - fuzzy search
- `filter` series - file filtering

### Compress and Mount Commands
- `extract-to`, `compress-to`, `archive-mount` - archive handling
- `mount-dev`, `umount-dev` - device mounting

### Compare and Verify Commands
- `diff`, `delta`, `diff-md5`, `check-sum` - file comparison

### External Program Integration
- `cmus-play`, `open-handlr`, `open-with-gui/cli` - program launching
- `drag-in`, `drag-out` - drag and drop operations
- Editor launch commands (`En`, `Ec`, `Ep`, etc.)

### System Commands
- `on-cd` - automatic trigger commands


Both implementations are functionally equivalent, with identical key bindings. The choice mainly depends on user preference for modern syntax and dependency on traditional Unix tools.

## Enable Shell in LF

- Enable Lumesh in LF
```bash
set shell lumesh      # required
set shellopts '-s'    # optional
set ifs "\n"          # optional
set filesep "\n"      # optional
```

- Enable bash in LF
```bash
set shell bash        # required
set shellopts '-eu'   # optional
set ifs "\n"          # optional
set filesep "\n"      # optional
```

## Main Command Comparison A

### 1. all-cmd Command
**Key Binding**: `<c-e>`

**Comparison**:
- **Lumesh**: Uses chain pipe methods and built-in functions
```bash
let cmd = lf -remote `query $id cmds` | .lines() | .sort() | .skip(1) | .map(x -> {x.split("\t\t") | .first()}) | ui.pick "select cmd:"
```

- **Bash**: Uses pipes and external tools
```bash
cmd=$( lf -remote "query $id cmds" | awk -F'\t' 'NR > 1 { print $NF}' | sort -u | fzf --reverse --prompt='Execute command: ' --preview='' )
```

**Advantages**:
- **Lumesh**: More intuitive syntax, chain calls are readable, completely avoids external application startup time and data conversion time, built-in `ui.pick` provides unified interaction experience
- **Bash**: Uses standard Unix tools, good compatibility, `awk` handles text more flexibly

### 2. history-cmd Command
**Key Binding**: `<backspace>`, `<backspace2>`

**Comparison**:
- **Lumesh**:
```bash
let cmd = lf -remote `query $id history` | .lines() | .sort() | ui.pick "history command:" | .split("\t\t") | .last()
```

- **Bash**:
```bash
cmd=$( lf -remote "query $id history" | awk -F'\t' 'NR > 1 { print $NF}' | sort -u | fzf --reverse --prompt='Execute command: ' --preview='' )
```

**Advantages**:
- **Lumesh**: Built-in method chain is more concise, `.last()` semantics are clear
- **Bash**: `awk`'s `$NF` efficiently handles the last column

### 3. toggle-preview Command
**Key Binding**: `zp`

**Comparison**:
- **Lumesh**: Uses pattern matching
```bash
match $lf_preview {
    'true' => lf -remote `send $id :set nopreview; set ratios 1:5`
    _ => lf -remote `send $id :set preview; set ratios 1:2:3`
}
```

- **Bash**: Uses conditional statements
```bash
if [ "$lf_preview" = "true" ]; then
    lf -remote "send $id :set nopreview; set ratios 1:5"
else
    lf -remote "send $id :set preview; set ratios 1:2:3"
fi
```

**Advantages**:
- **Lumesh**: `match` syntax is more modern, pattern matching is powerful; variables don't need quotes
- **Bash**: Traditional `if-else` structure is clear, easy to debug

### 4. select-files Command
**Key Binding**: `Sf` (files), `Sd` (directories), `SF` (empty files), `SD` (empty directories), `Sl` (symlinks), `Sx` (executables)

**Comparison**:
- **Lumesh**: Uses ternary operator and built-in functions
```bash
let htag= $lf_hidden ? '-H' : ''
let r=fd --exact-depth 1 $argv $htag -c never -j 1 | .lines() | .join(' ')
```

- **Bash**: Uses functions and conditional statements
```bash
get_files() {
    if [ "$lf_hidden" = 'false' ]; then
        fd --exact-depth 1 $@ -c never -j 1 -0
    else
        fd --exact-depth 1 $@ -H -c never -j 1 -0
    fi | xargs -0 printf ' %q'
}
```

**Advantages**:
- **Lumesh**: Ternary operator is concise, variable scope is clear
- **Bash**: Function encapsulates logic clearly, good reusability

### 5. fzf-content Command
**Key Binding**: `fc<space>`, `fct` (txt), `fcm` (md), `fcs` (sh), `fcy` (py), `fcj` (js)

**Comparison**:
- **Lumesh**: Uses modern syntax and string interpolation
```bash
let file_type = len($argv)>0 ? $argv[0] : 'sh'
let RG_PREFIX = `$lf_user_wheel rg --type $file_type --column --line-number --no-heading --color=always --smart-case --max-filesize 50K`
if $res {
    let a = $res.split(':').take(3).join(':')
    $lf_user_wheel hx $a
}
```

- **Bash**: Uses traditional shell syntax
```bash
RG_PREFIX="$lf_user_wheel rg --column --line-number --no-heading --color=always --smart-case --max-filesize 50K"
[ -n ${1:-''} ] && RG_PREFIX="$RG_PREFIX --type $1"
[ -n "$res" ] && $lf_user_wheel hx $(echo $res|cut -d: -f1) +$(echo $res|cut -d: -f2)
```

**Advantages**:
- **Lumesh**: String method chaining is more intuitive, conditional expressions are concise
- **Bash**: Parameter expansion is flexible, `cut` command handles delimiters efficiently

### 6. yank Series Commands
**Key Binding**: `yp` (paths), `yn` (filenames), `yb` (basenames), `yu` (clear)

**Comparison**:
- **Lumesh**: Uses functional programming style
```bash
$fx.lines() | .map(fs.base_name) | .join("\n") | wl-copy
$fx.lines() | .map(x -> {fs.base_name(true,$x) | .first()}) | .join("\n") | wl-copy
```

- **Bash**: Uses traditional Unix tools
```bash
basename -a -- "$fx" | head -c-1 | wl-copy
echo $fx | tr ' ' '\n' | wl-copy
basename -a -- "$fx" | cut -d. -f1 | head -c-1 | wl-copy
```

**Advantages**:
- **Lumesh**: Consistent functional style, built-in filesystem functions are feature-rich
- **Bash**: Unix tools are mature and stable, `basename`, `cut` etc. are professional and efficient

### 7. paste/mpaste Command
**Key Binding**: `pp` (paste), `po` (force), `pb` (backup), `pO` (force overwrite)

**Comparison**:
- **Lumesh**: Uses modern collection operations
```bash
let load=fs.read ~/.local/share/lf/files | .lines()
let mode=$load.at(0)
let files = $load.skip(1)
let base_names = $files.map(fs.base_name)
match $mode {
    copy => { $lf_user_wheel cp -r $argv -- $files '.' }
    move => { $lf_user_wheel mv -- $files '.' }
}
```

- **Bash**: Uses traditional text processing
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

**Advantages**:
- **Lumesh**: Collection operations are intuitive, pattern matching is elegant, built-in file functions are type-safe
- **Bash**: `sed` text processing is powerful, conditional branching is clear

### 8. bulk-rename Command
**Key Binding**: `cb`

**Comparison**:
- **Lumesh**: Uses modern data structures
```bash
let old_files = $fs.lines()
let new_files = fs.read $new | .lines()
for pair in list.zip($old_files,$new_files){
    if $pair[0] != $pair[1]{
        $lf_user_wheel mv -- $pair[0] $pair[1]
    }
}
```

- **Bash**: Uses traditional text processing
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

**Advantages**:
- **Lumesh**: `list.zip` functional operation is elegant, array access is intuitive
- **Bash**: `paste` command is professional, pipe processing has high memory efficiency

### 9. mount-dev Command
**Key Binding**: `mm`

**Comparison**:
- **Lumesh**: Uses structured data processing
```bash
let sel = lsblk -rno 'name,type,size,mountpoint,label,fstype' | into.table([name,'type',size,mountpoint,label,fstype])
| where($type!='disk' && !$mountpoint && $fstype !~: 'member')
| ui.pick "which to mount:"
let src = $sel.type == 'part' ? `/dev/${$sel.name}` : `/dev/mapper/${$sel.name}`
```

- **Bash**: Uses text processing and field extraction
```bash
sel=$(lsblk -rno 'name,type,size,label,mountpoint,fstype' |
awk -F'[ ]' '$2!="disk" && $5=="" && $6!~/member/ { print $1,$2,$3,$4 }' |
fzf --prompt='choose to Mount: ' --preview='')
x=$(echo "$sel" | cut -d' ' -f1)
typ=$(echo $sel | cut -d' ' -f2)
```

**Advantages**:
- **Lumesh**: Structured data processing is powerful, field access is type-safe, `where` filtering is intuitive
- **Bash**: `awk` text processing is flexible and efficient, field extraction is mature and stable

## Overall Advantages Comparison

**Lumesh Advantages**:
- Modern syntax, chain calls are intuitive
- Rich built-in functions, type-safe
- Consistent functional programming style
- Strong structured data processing capability
- Better debugging and error handling mechanisms
- Can reduce dependency on third-party tools, saving resources

**Bash Advantages**:
- Mature Unix tool ecosystem
- Good compatibility and portability
- Professional and efficient third-party text processing tools

## Notes

Both implementations achieve the same LF file manager functionality, with completely identical key bindings. The Lumesh version demonstrates the advantages of modern shell languages, while the Bash version reflects the stability of traditional Unix philosophy. Which one to choose mainly depends on user preference for syntax style and dependency on tool ecosystem.

Read more
- [LF configuration comparison (Lumesh vs Bash) B](/zh-cn/cases/lf_2)
