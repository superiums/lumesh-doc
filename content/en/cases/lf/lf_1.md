---

title: Comparison of lf File Manager Configurations
subtitle: lumesh vs bash
date: 2025-07-15 10:16:45
highlight: true
tags:
 - case
categories:
 - wiki
 - case

---

# Comparison of lf File Manager Configurations A

The **lumesh version** uses modern syntax and built-in functions, while the **bash version** employs traditional shell syntax and external tools.
- [Complete Configuration File in Lume](/data/lfrc_lm)
- [Complete Configuration File in Dash](/data/lfrc_sh)

## Overview

### General Function Commands
- `all-cmd`, `history-cmd`, `history-dir` - Command history and selection
- `toggle-preview`, `toggle-selmode`, `toggle-super` - Interface toggles
- `zox/z`, `zoxide-query`, `cd-usermedia` - Directory navigation

### File Operation Commands
- `select-files` series - File selection and filtering
- `yank-path`, `yank-name`, `yank-basename` - Copy operations
- `delete`, `trash`, `paste/mpaste`, `link` - File management
- `rename-to`, `bulk-rename` - Renaming operations
- `chmod`, `chown`, `mkfile`, `mkdirs` - Permissions and creation

### Search and Preview Commands
- `fzf-edit`, `fzf-file`, `fzf-folder`, `fzf-content` - Fuzzy search
- `filter` series - File filtering

### Compression and Mounting Commands
- `extract-to`, `compress-to`, `archive-mount` - Archive handling
- `mount-dev`, `umount-dev` - Device mounting

### Comparison and Verification Commands
- `diff`, `delta`, `diff-md5`, `check-sum` - File comparison

### External Integration
- `cmus-play`, `open-handlr`, `open-with-gui/cli` - Program launching
- `drag-in`, `drag-out` - Drag-and-drop operations
- Editor launch commands (`En`, `Ec`, `Ep`, etc.)

### System Commands
- `on-cd` - Automatically triggered commands

Both implementations are functionally equivalent, with identical key bindings. The choice mainly depends on the user's preference for modern syntax versus reliance on traditional Unix tools.

## Enabling Shell in lf

- To enable Lumesh in lf:
```bash
set shell lumesh      # Required
set shellopts '-s'    # Optional
set ifs "\n"          # Optional
set filesep "\n"      # Optional
```

- To enable bash in lf:
```bash
set shell bash        # Required
set shellopts '-eu'   # Optional
set ifs "\n"          # Optional
set filesep "\n"      # Optional
```

## Main Command Comparison A

### 1. all-cmd Command
**Key Binding**: `<c-e>`

**Syntax Comparison**:
- **lumesh**: Uses a chained pipeline method and built-in functions
```bash
let cmd = lf -remote `query $id cmds` | .lines() | .sort() | .drop(1) | .map(x -> {x.split("\t\t") | .first()}) | Ui.pick "select cmd:"
```

- **bash**: Uses pipelines and external tools
```bash
cmd=$( lf -remote "query $id cmds" | awk -F'\t' 'NR > 1 { print $NF}' | sort -u | fzf --reverse --prompt='Execute command: ' --preview='' )
```

**Advantages**:
- **lumesh**: More intuitive syntax, readable chained calls, eliminates external application startup and data conversion time, built-in `Ui.pick` provides a unified interaction experience.
- **bash**: Uses standard Unix tools, good compatibility, `awk` offers more flexible text processing.

### 2. history-cmd Command
**Key Binding**: `<backspace>`, `<backspace2>`

**Syntax Comparison**:
- **lumesh**:
```bash
let cmd = lf -remote `query $id history` | .lines() | .sort() | Ui.pick "history command:" | .split("\t\t") | .last()
```

- **bash**:
```bash
cmd=$( lf -remote "query $id history" | awk -F'\t' 'NR > 1 { print $NF}' | sort -u | fzf --reverse --prompt='Execute command: ' --preview='' )
```

**Advantages**:
- **lumesh**: Built-in method chain is more concise, `.last()` is semantically clear.
- **bash**: `awk`'s `$NF` efficiently handles the last column.

### 3. toggle-preview Command
**Key Binding**: `zp`

**Syntax Comparison**:
- **lumesh**: Uses pattern matching
```bash
match $lf_preview {
    'true' => lf -remote `send $id :set nopreview; set ratios 1:5`
    _ => lf -remote `send $id :set preview; set ratios 1:2:3`
}
```

- **bash**: Uses conditional statements
```bash
if [ "$lf_preview" = "true" ]; then
    lf -remote "send $id :set nopreview; set ratios 1:5"
else
    lf -remote "send $id :set preview; set ratios 1:2:3"
fi
```

**Advantages**:
- **lumesh**: `match` syntax is more modern, powerful pattern matching; variables do not require quotes.
- **bash**: Traditional `if-else` structure is clear and easy to debug.

### 4. select-files Command
**Key Binding**: `Sf` (files), `Sd` (directories), `SF` (empty files), `SD` (empty directories), `Sl` (symbolic links), `Sx` (executable files)

**Syntax Comparison**:
- **lumesh**: Uses a ternary operator and built-in functions
```bash
let htag= $lf_hidden ? '-H' : ''
let r=fd --exact-depth 1 $argv $htag -c never -j 1 | .lines() | .join(' ')
```

- **bash**: Uses functions and conditional statements
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
- **lumesh**: Ternary operator is concise, variable scope is clear.
- **bash**: Function encapsulation provides clear logic and good reusability.

### 5. fzf-content Command
**Key Binding**: `fc<space>`, `fct` (txt), `fcm` (md), `fcs` (sh), `fcy` (py), `fcj` (js)

**Syntax Comparison**:
- **lumesh**: Uses modern syntax and string interpolation
```bash
let file_type = len($argv)>0 ? $argv[0] : 'sh'
let RG_PREFIX = `$lf_user_wheel rg --type $file_type --column --line-number --no-heading --color=always --smart-case --max-filesize 50K`
if $res {
    let a = $res.split(':').take(3).join(':')
    $lf_user_wheel hx $a
}
```

- **bash**: Uses traditional shell syntax
```bash
RG_PREFIX="$lf_user_wheel rg --column --line-number --no-heading --color=always --smart-case --max-filesize 50K"
[ -n ${1:-''} ] && RG_PREFIX="$RG_PREFIX --type $1"
[ -n "$res" ] && $lf_user_wheel hx $(echo $res|cut -d: -f1) +$(echo $res|cut -d: -f2)
```

**Advantages**:
- **lumesh**: String method chaining is more intuitive, conditional expressions are concise.
- **bash**: Parameter expansion is flexible, `cut` command efficiently handles delimiters.

### 6. yank Series Commands
**Key Binding**: `yp` (path), `yn` (filename), `yb` (basename), `yu` (clear)

**Syntax Comparison**:
- **lumesh**: Uses functional programming style
```bash
$fx.lines() | .map(Fs.base_name) | .join("\n") | wl-copy
$fx.lines() | .map(x -> {Fs.base_name(True,$x) | .first()}) | .join("\n") | wl-copy
```

- **bash**: Uses traditional Unix tools
```bash
basename -a -- "$fx" | head -c-1 | wl-copy
echo $fx | tr ' ' '\n' | wl-copy
basename -a -- "$fx" | cut -d. -f1 | head -c-1 | wl-copy
```

**Advantages**:
- **lumesh**: Consistent functional style, rich built-in filesystem functions.
- **bash**: Mature and stable Unix tools, `basename`, `cut`, etc., are professional and efficient.

### 7. paste/mpaste Command
**Key Binding**: `pp` (paste), `po` (force), `pb` (backup), `pO` (force overwrite)

**Syntax Comparison**:
- **lumesh**: Uses modern collection operations
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

- **bash**: Uses traditional text processing
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
- **lumesh**: Collection operations are intuitive, pattern matching is elegant, built-in file functions are type-safe.
- **bash**: Powerful text processing with `sed`, clear conditional branches.

### 8. bulk-rename Command
**Key Binding**: `cb`

**Syntax Comparison**:
- **lumesh**: Uses modern data structures
```bash
let old_files = $fs.lines()
let new_files = Fs.read $new | .lines()
for pair in List.zip($old_files,$new_files){
    if $pair[0] != $pair[1]{
        $lf_user_wheel mv -- $pair[0] $pair[1]
    }
}
```

- **bash**: Uses traditional text processing
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
- **lumesh**: `List.zip` functional operation is elegant, array access is intuitive.
- **bash**: `paste` command is professional, pipeline processing is memory efficient.

### 9. mount-dev Command
**Key Binding**: `mm`

**Syntax Comparison**:
- **lumesh**: Uses structured data processing
```bash
let sel = lsblk -rno 'name,type,size,mountpoint,label,fstype' | Into.table([name,'type',size,mountpoint,label,fstype])
| where($type!='disk' && !$mountpoint && $fstype !~: 'member')
| Ui.pick "which to mount:"
let src = $sel.type == 'part' ? `/dev/${$sel.name}` : `/dev/mapper/${$sel.name}`
```

- **bash**: Uses text processing and field extraction
```bash
sel=$(lsblk -rno 'name,type,size,label,mountpoint,fstype' |
awk -F'[ ]' '$2!="disk" && $5=="" && $6!~/member/ { print $1,$2,$3,$4 }' |
fzf --prompt='choose to Mount: ' --preview='')
x=$(echo "$sel" | cut -d' ' -f1)
typ=$(echo $sel | cut -d' ' -f2)
```

**Advantages**:
- **lumesh**: Powerful structured data processing, type-safe field access, intuitive `where` filtering.
- **bash**: Flexible and efficient text processing with `awk`, mature and stable field extraction.

## Overall Advantages Comparison

**Advantages of Lumesh**:
- Modern syntax, intuitive chained calls
- Rich built-in functions, type-safe
- Consistent functional programming style
- Strong structured data processing capabilities
- Improved debugging and error handling mechanisms
- Minimizes reliance on third-party tools, saving resources

**Advantages of Bash**:
- Mature Unix tool ecosystem
- Good compatibility and portability
- Professional and efficient third-party text processing tools

## Notes

Both implementations achieve the same functionality of the lf file manager, with identical key bindings. The Lumesh version showcases the advantages of modern shell languages, while the bash version reflects the stability of traditional Unix philosophy. The choice primarily depends on the user's preference for syntax style and reliance on tool ecosystems.

Read more
- [lf Configuration File Comparison (lumesh vs bash) B](lf_2)
