---
title: Lumesh写的lf文件管理器配置
date: 2025-07-05 19:16:45
highlight: true
tags:
 - glance
categories:
 - wiki
 - why
 - syntax
---

Lumesh写的lf文件管理器配置

shebang 只是为了语法高亮

ifs 设置，需配合`LUME_IFS_MODES`设置。更好的解决方案是不直接使用包含多个变量的`$fx`作为命令参数，而是使用`$fx.lines()`分割后作为参数。

shellopts `-s` 表示严格模式。非严格模式允许直接使用字面量作为变量，严格模式可以获得更快的解析速度。

shell 后面指定lume或lumesh作为脚本解析器。

[使用Lumesh编写lf配置文件的语法演示](/zh-cn/cases/case_lf)

```bash
#! lumelf
set ifs '\n'
# set ifs "\n"
set shellopts '-sM'
set shell lume
set filesep "\n"
& if $LF_LEVEL.to_int() > 1 { lf -remote `send $id echoerr "Nest Level $LF_LEVEL"` }
& if !fs.exists('/tmp/lf') {mkdir /tmp/lf}
& if !fs.exists('/tmp/lf/files') {touch /tmp/lf/files /tmp/lf/tags /tmp/lf/history; 'copy' >! /tmp/lf/files}
#
# ========== General settings ==========
# --style--
set borderfmt "\033[32m"
set errorfmt "\033[1;43;41m"
set timefmt '2006-1-2 15:04'
set rulerfmt "%{lf_user_wheel}| %a|  %p| %{lf_mode}| %{lf_selmode} \033[7;31m %m \033[0m|  \033[7;33m %c \033[0m|  \033[7;35m %s \033[0m|  \033[7;34m %f \033[0m|  %i/%t"
set tempmarks '=>+@^#'

set drawbox
set roundbox
set showbinds
set scrolloff 10
set icons
# set globsearch
set incsearch
set incfilter
# set smartcase
set ignorecase
set anchorfind false
set findlen 0
set tabstop 4
set info 'size'
set preview
# set sixel
# set previewer ~/.config/lf/previewer.lm
set previewer ~/.config/lf/previewer
# set cleaner ~/.config/lf/cleaner
set dircounts
set selmode 'dir'
# setlocal ~/Downloads sortby "atime"
# setlocal ~/Downloads reverse
set user_wheel ''
# Remove some defaults
map d
map y
map p
map m
# map s
map f
map c
map r
map t
map G
map F
map w
# ========== Commands ==========
#    profile
cmd profile ${{
  lf -remote `send $id source ~/.config/lf/profiles/${$argv[0]}.lmf`
}}
map z2 profile extra
map z3 profile disk
map z4 profile convert
map z5 profile develop
map z6 profile auto-redraw
map z7 profile tarzip

# defaults:
cmd open % eprint 'not dir'
map e $$lf_user_wheel hx $f
map zm set info perm
map zu set info user:group

# ---------- shell ----------
# map W  ${{ $SHELL }}
map W $lume -mic `cd $PWD`
map . :read; cmd-history-prev;
map <a-\;> push :<space>$fx<home>
map <a-4> push $<space>$fx<home>
map <a-7> push &<space>$fx<home>
map <a-5> push %<space>$fx<home>
map <a-1> push !<space>$fx<home>

cmd all-cmd ${{
    let cmd = lf -remote `query $id cmds` | .lines() | .skip(1) | \
        .map(x -> {$x.split("\t\t") | .first()}) | ui.pick("select cmd:")
    lf -remote `send $id :$cmd`
}}
map <c-e> all-cmd

cmd history-cmd ${{
    let cmd = lf -remote `query $id history` | .lines() | .last() | ui.pick("history command:") | .split("\t\t") | .last()
    lf -remote `send $id $cmd`
}}
map <backspace> history-cmd
map <backspace2> history-cmd
# <c-h>

cmd history-dir ${{
  let hist = lf -remote `query $id jumps` | .lines() | .skip(2) | .map(x -> $x.split()) | ui.pick("choose history:")
  lf --remote `send $id cd ${$hist.last()}`
}}
map <c-g> history-dir

# ---------- quit ----------
cmd quit-print ${{ print $fx ; lf -remote `send $id quit` }}
map <c-o> quit-print

# ---------- settings ----------
map <f-12> ${{ lf -remote `query $id maps` | less }}

cmd edit-config ${{
    hx ~/.config/lf/lfrc
    lf -remote `send $id source ~/.config/lf/lfrc`
}}
map zc edit-config

cmd toggle-preview ${{
    match $lf_preview {
        'true' => lf -remote `send $id set nopreview; set ratios 1:5`
        _ => lf -remote `send $id set preview; set ratios 1:2:3`
    }
}}
map zp toggle-preview

cmd toggle-selmode %{{
    match $lf_selmode {
        dir => lf -remote `send $id :set selmode 'all'`
        _ => lf -remote `send $id :set selmode 'dir'`
    }
}}
map zS toggle-selmode

cmd parent-panel-off ${{
    lf -remote `send $id :set preview; set ratios 2:3`
}}
map zP parent-panel-off

cmd toggle-super ${{
    if $lf_user_wheel {
        lf -remote `send $id :set user_wheel;set borderfmt "\033[32m"; set promptfmt "\033[32;1m%u@%h\033[0m:\033[34;1m%d\033[0m\033[1m%f\033[0m"`
    }else{
        lf -remote `send $id :set user_wheel 'pkexec --keep-cwd';set borderfmt "\033[31m"; set promptfmt "\033[5;5mSUPER\033[0m $id \033[0m:\033[34;1m%d\033[0m\033[1m%f\033[0m"`
    }
}}
map zz toggle-super

# ---------- reload ----------
# reload config
map <c-s> source ~/.config/lf/lfrc

# ---------- navigation ----------
map <tab> half-down
map <backtab> half-up
map J push 3j
map K push 3k
map <c-j> push 7j
map <c-k> push 7k
map <a-j> push 10j
map <a-k> push 10k
# ---------- quick navigation ----------
# Fast navigation
# map gh cd ~
map g<space> push :cd<space>
map g/ cd /
map gr cd /
map gn cd /run
map go cd /opt
map gu cd /usr
map gm cd /mnt
map gt cd /tmp
map gp cd /proc
map ge cd /etc
map gv cd /var
map gs cd /usr/share
map gc cd ~/.config
map gd cd ~/Documents
map gD cd ~/Downloads
map gl cd ~/.local
map gb cd ~/.local/bin
map g. cd ~/.config/lf
map ga cd /usr/share/applications
map gG bottom

# zoxide
cmd zox %{{
    if len($argv) {
        let select=zoxide query --exclude (pwd()) $argv
        lf -remote `send $id cd $select`
    }
}}
map ; push :zox<space>
map gz push :zox<space>

cmd zoxide-query ${{
    let select=zoxide query -i
    lf -remote `send $id cd $select`
}}
map gq zoxide-query

# cmd cd-usermedia & lf -remote `send $id cd /run/media/$USER`
cmd cd-usermedia &{{
    mkdir -p `$XDG_RUNTIME_DIR/media`
    lf -remote `send $id cd $XDG_RUNTIME_DIR/media`
}}
map gi cd-usermedia

# link
cmd follow-link %{{
    let real=readlink $f
    lf -remote `send $id select $real`
}}
map gL follow-link

# ---------- select ----------
cmd select-files &{{
    let htag= $lf_hidden ? '-H' : ''
    let r=fd --exact-depth 1 $argv $htag -c never -j 4 | .lines() | .join(' ')
    lf -remote `send $id :unselect; toggle $r`
}}
map Sf select-files -t file
map Sd select-files -t directory
map SF select-files -t empty -t file
map SD select-files -t empty -t dir
map Sl select-files -t symlink
map Sx select-files -t executable
map Sn select-files --regex '^[^.]+$'
map Se push :select-files<space>-e<space>
map SE push :select-files<space>--exclude<space>
map Ss push :select-files<space>--size<space>  # -1k +3m
map Si push :select-files<space>--changed-within<space>  # 1min 2h 3d 4week
map Sb push :select-files<space>--changed-before<space>  # 1min 2h 3d 4week
map So select-files -o root

map Sr push :select-files<space>--regex<space>

# ---------- search ----------
map , find
# map <backslash> find
# map \[ find-prev
# map \] find-next
map <lt> find-prev
map <gt> find-next

cmd fzf-edit $ hx (fzf)
map fe fzf-edit

# Select the file or directory via fzf
# limit size 50k
cmd fzf-file ${{
    print $argv
    typeof $argv | print
    let ext = len($argv) ? '-e' + $argv[0] : ''
    let selected = fd --type 'file' $ext '-S-50k' -j 4 | fzf --preview "~/.config/lf/previewer {} 30 18"
    lf -remote `send $id select $selected`
}}
map ff<space> push :fzf-file<space>
map fft fzf-file txt
# map fp fzf-file png
# map fj fzf-file jpg
# map fa fzf-file mp3
# map fv fzf-file mp4
# map ffw fzf-file docx
# map ffx fzf-file xlsx
map ffg fzf-file gz
# map ffz fzf-file zip
map ffm fzf-file md
map ffs fzf-file sh
map ffy fzf-file py

# cd into the selected directory via fzf
cmd fzf-folder ${{
    let select = $lf_user_wheel fd --type d '.' -d 5 -j 4 | fzf --preview 'ls {}'
    lf -remote `send $id cd $select`
}}
map fd fzf-folder

# limit size 50k
cmd fzf-content ${{
    let file_type = $argv[0] ?: 'md'
    let RG_PREFIX = `$lf_user_wheel rg --type $file_type --column --line-number --no-heading --color=always --smart-case --max-filesize 50K`
    let res = fzf --ansi --disabled \
          --bind `start:reload:$RG_PREFIX {q}` \
          --bind `change:reload:sleep 0.1; $RG_PREFIX {q} || true` \
          --bind "alt-enter:unbind(change,alt-enter)+change-prompt(2. fzf> )+enable-search+clear-query" \
          --color "hl:-1:underline,hl+:-1:underline:reverse" \
          --prompt '1. ripgrep> ' \
          --delimiter ':' \
          --preview 'bat --color=always {1} --highlight-line {2}' \
          --header `Searching Content in FileType: $file_type` \

          # --preview-window 'up,60%,border-bottom,+{2}+3/3,~3' \
          # --bind 'enter:become(vim {1} +{2})'
    if $res {
          let a = $res.split(':').take(3).join(':')
          $lf_user_wheel hx $a
    }
    # [ -n "$res" ] && lf -remote `send $id select \"$res\"`
}}
map fc<space>  push :fzf-content<space>
map fct fzf-content txt
map fcm fzf-content md
map fcs fzf-content sh
map fcy fzf-content py
map fcj fzf-content js


# ---------- filter ----------
map \\ filter
map F<space> filter
map Ft setfilter .txt
map Fp setfilter .png
map Fj setfilter .jpg
map Fa setfilter .mp3
map Fv setfilter .mp4
map Fw setfilter .docx
map Fx setfilter .xlsx
map Fg setfilter .gz
map Fz setfilter .zip
map Fm setfilter .md
map fs.setfilter .sh
map Fy setfilter .py
map Fc setfilter
# map fm :filter; set user_filter true

# ---------- sort ----------

# ---------- operation ----------

# ----- op: yank ----------
map yy copy

# Copy the absolute paths of selections separated by \n
cmd yank-path &{{
    $fx.lines().join("\n") | wl-copy
}}
map yp yank-path

# Copy the file names (including extension) of the selections separated by \n
cmd yank-name &{{
    $fx.lines() | .map(x -> fs.base_name($x)) | .join("\n") | wl-copy
}}
map yn yank-name

cmd yank-basename &{{
    $fx.lines() | .map(x -> {fs.stem($x)}) | .join("\n") | wl-copy
}}
map yb yank-basename

cmd yank-clear &{{
  '' >! ~/.local/share/lf/files
  lf -remote 'send clear'
}}
map yu yank-clear

cmd yank-list $$fx | hx
map \| yank-list

# ----- op: cut ----------
map yc cut

# ----- op: delete ----------
cmd delete ${{
  println '=====DELETE====='.red().bold() $fx '================'.red()
  if ui.confirm('Delete these files [y/n]:'){
    $lf_user_wheel rm -rf $fx.lines()
  }
}}
map dD delete

cmd trash ${{
    let files = $fx.lines() | list.map(x -> fs.base_name($x))
    let ans = read `Trash: $files [y/N]`

    if $ans == 'y' {
        mkdir -p /tmp/.trash
        $lf_user_wheel mv -- $fx.lines() /tmp/.trash/
        print 'Trash complete!'
    }else{
        echo 'Canceled!'
    }
}}
map dd trash

# ----- op: paste ----------
# focus: select;
# tag as choosed: unselect; toggle
# cancel tag as choosed: unselect

cmd mpaste %{{
    let load = fs.read ~/.local/share/lf/files | .lines()
    let files = $load.skip(1)
    let file_count = len($files)
    if $file_count == 0 {
        print 'No files yanked'
        exit 0
    }
    let mode = $load.get(0)
    let base_names = $files.map(x -> fs.base_name($x))
    let tg
    let ans = read `$mode $file_count files? [y/N]`
    if $ans == 'y' {
        match $mode {
            copy => {
                $lf_user_wheel cp -r $argv -- $files '.'
                set tg = '='
            }
            move => {
                $lf_user_wheel mv -- $files '.'
                set tg = '>'
            }
        }

        '' >! ~/.local/share/lf/files
        lf -remote 'send clear'
        # lf -remote `send $id :unselect`
        for name in $base_names {
            lf -remote `send $id :select $name; tag '$tg'`
        }
    }
}}
# with backup
map po mpaste --backup=numbered --force
map pb mpaste --backup=numbered
# no backup
map pO mpaste --force
map pi mpaste -i
map pn mpaste -n
map pu mpaste --update
map pP mpaste --update --preserve
# link
map ps mpaste --symbolic-link
map pl mpaste --link
map pH mpaste -H
# use bultiin paste
map pp paste


cmd link %{{
    let load= fs.read ~/.local/share/lf/files | .lines()
    let files=$load.skip(1)
    let file_count = len($files)
    if $file_count==0 {
        print 'No files yanked'
        exit 0
    }
    let mode=$load.get(0)
    let base_names = $files.map(x -> fs.base_name($x))

    for filex in $base_names{
        if (fs.exists fs.join('.',$filex)) {
            eprint $filex 'Already exists!'
            exit 1
        }
    }
    match $mode {
        copy => $lf_user_wheel ln -s -- $files '.'
        move => $lf_user_wheel ln -- $files '.'
    }

    '' >! ~/.local/share/lf/files
    lf -remote 'send clear'
    # lf -remote `send $id :unselect`
    for file in $base_names {
        # echo $file..>>/tmp/lf/p
        lf -remote `send $id :select '$file'; tag '@'`
    }

}}
map pL link

# copy with rsync
cmd paste-rsync %{{
    let load = fs.read ~/.local/share/lf/files | .lines()
    let mode = $load.get(0)
    let files = $load.skip(1)
    match $mode{
        copy => {
            $lf_user_wheel rsync -ar --ignore-existing --info=progress2 -- $files '.'
        }
        move => {
            $lf_user_wheel rsync -ar --remove-source-files --ignore-existing --info=progress2 -- $files '.'
        }
    }

    '' >! ~/.local/share/lf/files
    # lf -remote `send $id Rsyn Finished.`
    # lf -remote `send clear`
}}
map pr paste-rsync

cmd paste-to %{{
    let dest = $argv[0] ?: {print 'Cancelled';exit 0}
    $lf_user_wheel cp -r --backup=numbered -i -- $fx $dest
    if fs.is_dir($dest){
        let base_names = $fx.lines() | .map(x -> fs.base_name($x)) | .join("\n")
        lf -remote `send $id :unselect; cd $dest; select $base_names`
    }else{
        lf -remote `send $id :unselect; select $dest; `
    }
}}
map pt push :paste-to<space>

cmd paste-from %{{
    let dest = $argv[0] ?: {print 'Cancelled';exit 0}
    $lf_user_wheel cp -r --backup=numbered -i -- $dest '.'
    let base_name = fs.base_name($dest)
    lf -remote `send $id :unselect; select $base_name; `
}}
map pf push :paste-from<space>


# ----- op: change name ----------
cmd rename-to %{{
    let base_name = fs.base_name($fx)
    let new_name = read `rename "$base_name" to:`
    if $new_name {
        $lf_user_wheel mv -- $base_name $new_name
        lf -remote `send $id :select $new_name`
    }
}}
map mv rename-to

map ch :rename; cmd-home                  #rename head
map ct :rename; cmd-end                   #rename tail
map ca rename                             #rename after basename
map cn :rename; cmd-delete-home           #rename basename
map ce push ca<c-f><c-k>                  #rename extension
map cf :rename; cmd-end; cmd-delete-home  #rename fullname

# Bulk rename on selected files or all the non-hidden files in the current directory if no selection
cmd bulk-rename ${{
    let new = mktemp _
    $fs + "\n" >! $new
    hx $new
    let old_files = $fs.lines()
    let new_files = fs.read $new | .lines()
    lf -remote `send $id unselect`
    for pair in list.zip($old_files,$new_files){
        if $pair[0] != $pair[1]{
            $lf_user_wheel mv -- $pair[0] $pair[1]
            lf -remote `send $id select ${$pair[1]}`
        }
    }
    rm $new
}}

map cb bulk-rename

# ----- op: chmod ----------
cmd chmod %{{
    let ans = read "Mode Bits:"
    if $ans {
        $fx |> $lf_user_wheel chmod $ans _
        lf -remote 'send reload'
    }
}}
map cm chmod

cmd chown %{{
    let ans = read "new Owner:Group :"
    if $ans {
        $fx |> $lf_user_wheel chown $argv $ans -- _
        lf -remote 'send reload'
    }
}}
map co chown
map cO chown -R

# ----- op: make new ----------
# map mf push %touch<space>
cmd mkfile %{{
    if len($argv)>0 {
        $lf_user_wheel touch -- $argv
        for file in $argv{
            lf -remote `send $id select $file; tag '+'`
        }
    }
}}
map mf push :mkfile<space>

# cmd mkdir %mkdir -p "$(echo $* | tr ' ' '\ ')"
cmd mkdirs ${{
    if $argv {
        $lf_user_wheel mkdir -p -- $argv
        # let name = ""
        for file in $argv{
            if !$file.starts_with('/'){
                let name = fs.base_name($file)
                lf -remote `send $id :select $name; tag '+'`
            }
        }
    }
}}
map mk push :mkdirs<space>

# Create a directory with the selected items
cmd folder-selected %{{
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
}}
map ms folder-selected


# ----- op: view ----------
map i ${{
    let LESSOPEN=' | ~/.config/lf/previewer %s 20 30'; less -R '--lesskey-content=i quit' $f
}}
# map i ${{let LESSOPEN=' | ~/.config/lf/previewer %s 20 30'; less -R -k ~/.config/lf/less.lesskey $f}}

# ----- op: edit ----------
map En &geany $fx
map Ec &code $fx
map Ep &lapce $fx
map Eg &geany $fx
map Ee &gedit $fx
map Ea &apostrophe $fx
map El &lite-xl $fx
map Em &marker $fx
map Ef &ferrite $fx
map Er &retext $fx
map Ev &vi $fx
map Ez &zed $fx
map Eg $glow $fx | less
# ----- op: open ----------
# reveal dir--
map rr &$lf_user_wheel foot lf '.'
map rt &thunar '.'
map rs &spacefm -t '.'
map rh &hx '.'
map rc &code '.'
map rp &lapce '.'
map rn &geany '.'
map rl &lite-xl '.'
map rz &zed '.'
cmd cmus-play &{{
    # sock=/run/user/1000/cmus-socket 默认socket路径，无须指定
    pgrep -x cmus ?: foot cmus
    cmus-remote -c -q $fx
    cmus-remote -p -q
}}
map Om cmus-play
# open file--
cmd open-handlr $ handlr open $fx; lf -remote `send $id unselect`
map o open-handlr

cmd open-with-gui &{{ let cmd = $argv[0]; $cmd $fx }} ## opens with a gui application outside lf client
map Og push :open-with-gui<space> ## input application

cmd open-with-cli ${{ let cmd = $argv[0]; $cmd $fx }} ## opens with a cli application inside lf client
map Oc push :open-with-cli<space> ## input application

map Ox &xarchiver $f

# ----- op: archive ----------
cmd extract-to ${{
    let dest = $argv[0] ?: {print 'Cancelled'; exit 0}
    # tar, zip, gz, 7z, xz/lzma, bz/bz2, bz3, lz4, sz (Snappy), zst, rar and br
    if (regex.match '\.([gb7xs]z|t[gbx]z|zip|zst|bz2|lz4|lzma|tar|rar|br)$' $f) {
        let base_name = fs.stem($f)
        let npath = fs.join($dest,$base_name)
        $lf_user_wheel ouch -q decompress --dir $npath $f
        lf -remote `send $id :cd $dest; select $base_name; tag '^'`
    }else{
        print 'Unsupported file extension'
    }
}}
map ah push :extract-to<space>./
map ax push :extract-to<space>/tmp/
map aX push :extract-to<space>

# ----- op: compress ----------
cmd compress-to ${{
    let dest = $argv[0] ?: {print 'Cancelled'; exit 0}
    let sources = $fx.lines()
    let base_name
    let dest_file
    if $dest.ends_with('/'){
        set base_name = fs.base_name($sources.first())
        set dest_file = fs.join($dest, $base_name)
    }else{
        set base_name = fs.base_name($dest)
        set dest_file = $dest
    }
    if !(regex.match '\.(tgz|gz|zip|tar|7z|bz|bz2|xz|lzma|sz|lz4|zst|rar)$' $base_name) {
        set base_name = $base_name + '.tgz'
        set dest_file = $dest_file + '.tgz'
    }
    $lf_user_wheel ouch compress -qSg $sources $dest_file
    let dir = fs.dir_name($dest)
    lf -remote `send $id :cd $dir; select $base_name; tag '#'`
}}
map ac push :compress-to<space>/tmp/

# mount archive
cmd archive-mount ${{
    let base_name = fs.base_name($f)
    let mntdir =`/tmp/lf/mount/$base_name`
    mkdir -p $mntdir
    $lf_user_wheel archivemount $f $mntdir -o nosave
    lf -remote `send $id cd $mntdir`
}}
map am archive-mount

# ---------- bookmark ----------
map mb mark-save

# ---------- diff ----------
cmd diff !{{
    let files = $fs.lines()
    if len($files)>1 {
       $lf_user_wheel diff -w $files[0] $files[1] ?: 'Diff'
       println '-----Finished-----'
    }else{
        echo 'please select 2 files!'
    }
}}
map df diff

cmd delta !{{
    let files = $fs.lines()
    if len($files)>1 {
        $lf_user_wheel delta $files[0] $files[1] ?: 'Diff'
        println '-----Finished-----'
    }else{
        echo 'please select 2 files!'
    }
}}
map dt delta

cmd diff-md5 %{{
    let files = $fs.lines()
    if len($files)>1 {
        let lines = $lf_user_wheel md5sum $files[0] $files[1] | .lines()
        let s1 = $lines[0] | .words() | .get(0)
        let s2 = $lines[1] | .words() | .get(0)
        print($s1==$s2 ? 'Same' : 'Differ')
    }else{
        echo 'please select 2 files!'
    }
}}
map dm diff-md5

cmd check-sum %{{
    let ext_name = fs.extension($fx)
    match $ext_name {
        sha512 => sha512sum -c $fx
        sha384 => sha384sum -c $fx
        sha256 => sha256sum -c $fx
        sha224 => sha224sum -c $fx
        sha1 => sha1sum -c $fx
        md5 => md5sum -c $fx
        _ => shasum $fx
    }
}}
map dc check-sum

# ---------- other ----------
# list the size of each item in the current directory
map d<space> !dust $fx


# ---mount---

cmd mount-dev ${{
    # let devices = lsblk -Jpo 'name,type,size,mountpoint,label,fstype' | from.json() | .get('blockdevices')
    # let device = table.get $devices 'name' | ui.pick 'mount device:'
    # let sel = table.grep $devices $device | .get(0) | .get(0) | table.rows() | ui.pick('mount partition:') ?: {print 'no partition'; exit 0}

    let cols = 'name,type,size,fstype,label,mountpoint'
    let sel =  lsblk -ro $cols | into.table($cols.split(',')) \
        | where(!$mountpoint && $type!='disk') | table.rows(true) \
        | ui.pick _ 'to mount:' ?: { print 'no device'; exit 0 }

    if $sel {
        if !$lf_user_wheel {
            eprint 'Must be root to mount'
            exit 1
        }
        let src = $sel.type=='part' ? `/dev/${$sel.name}` : `/dev/mapper/${$sel.name}`
        let point = $sel.label==none ? $sel.name : $sel.label
        # let uid = id^ -u
        # let dest = `/run/user/$uid/media/$point`
        let dest = `$XDG_RUNTIME_DIR/media/$point`
        if !fs.exists($dest){ mkdir -p $dest }
        $lf_user_wheel mount -m -o 'defaults,noatime' $src $dest  ?: \
            e -> {notify-send 'Mount Failed' $e.msg.lines().join(';'); exit 1}
            # e -> {lf -remote `send $id echoerr Mount Failed: ${e.msg}`}
        lf -remote `send $id cd $dest`
        notify-send 'Mount' `device $src mounted`
    }
}}
map mm mount-dev

cmd umount-dev ${{
    let sel =  lsblk -rno 'name,type,size,mountpoint,label,fstype' | into.table([name,'type',size,mountpoint,label,fstype])     \
        | where($mountpoint) | table.rows(true) \
        | ui.pick _ ?.

    if sel {
        if $PWD ~: $sel.mountpoint {
            lf -remote `send $id cd /tmp`
        }
        $lf_user_wheel umount $sel.mountpoint ?: \
            e -> {notify-send 'Umount Failed:' $e.msg.lines().join(';'); exit 1}
        lf -remote `send $id reload`
        notify-send 'Umount' `device ${$sel.name} umounted`
    }
}}
map mu umount-dev

#---tag---
map T tag-toggle

# ---drag----
cmd drag-in %{{
  let dest = dragon.skip --target -x -p
  cp $dest .
  let base_name = fs.base_name($dest)
  lf -remote `send $id :select ${$base_name}; tag '='`
}}
map di drag-in

cmd drag-out &dragon.skip $fx
map do drag-out

# ========== Life-Cyle-Hook ========
# cmd on-cd &{{
    # console.print_tty `\033]0;lf $PWD\007`
    # let title = `\033]0;lf $PWD\007`
    # tty-write $title
# }}

# also run at startup
# on-cd

```
