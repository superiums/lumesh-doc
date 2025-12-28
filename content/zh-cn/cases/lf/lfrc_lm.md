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

# Lumesh写的lf文件管理器配置

shebang 只是为了语法高亮

ifs 设置，需配合`LUME_IFS_MODES`设置。更好的解决方案是不直接使用包含多个变量的`$fx`作为命令参数，而是使用`$fx.lines()`分割后作为参数。

shellopts `-s` 表示严格模式。非严格模式允许直接使用字面量作为变量，严格模式可以获得更快的解析速度。

shell 后面指定lume或lumesh作为脚本解析器。

[使用Lumesh编写lf配置文件的语法演示](/zh-cn/cases/case_lf)

```bash
set ifs "\n"
set shellopts '-s'
set shell lumesh
set filesep "\n"
& if $LF_LEVEL.to_int() > 1 { lf -remote `send $id echoerr "Nest Level $LF_LEVEL"` }
& if !Fs.exists('/tmp/lf') {mkdir /tmp/lf}
& if !Fs.exists('/tmp/lf/files') {touch /tmp/lf/files /tmp/lf/tags /tmp/lf/history; 'copy' >! /tmp/lf/files}

set borderfmt "\033[32m"
set errorfmt "\033[1;43;41m"
set timefmt '2006-1-2 15:04'
set rulerfmt "%{lf_user_wheel}| %a|  %p|  %{lf_selmode} \033[7;31m %m \033[0m|  \033[7;33m %c \033[0m|  \033[7;35m %s \033[0m|  \033[7;34m %f \033[0m|  %i/%t"
set locale 'zh-CN'
set tempmarks '=>+@^#'

set drawbox
set roundbox
set showbinds

set scrolloff 10
set icons
set incsearch
set incfilter
set ignorecase
set anchorfind false
set findlen 0
set tabstop 4
set info 'size'
set preview
set sixel
set previewer ~/.config/lf/previewer
set dircounts
set selmode 'dir'
set user_wheel ''
map d
map y
map p
map m
map f
map c
map r
map t
map G
map F
map w
cmd profile ${{
  print lf -remote `send $id source ~/.config/lf/profiles/${$argv[0]}`
}}
map z2 profile extra
map z3 profile disk
map z4 profile convert
map z5 profile develop
map z6 profile auto-redraw
map z7 profile tarzip

cmd open % eprint 'not dir'
map e $$lf_user_wheel hx $f


map zm set info perm
map zu set info user:group


map W $lume
map . :read; cmd-history-prev;
map <a-\;> push :<space>$fx<home>
map <a-4> push $<space>$fx<home>
map <a-7> push &<space>$fx<home>
map <a-5> push %<space>$fx<home>
map <a-1> push !<space>$fx<home>

cmd all-cmd ${{
    let cmd = lf -remote `query $id cmds` | .lines() | .drop(1) | \
        .map(x -> {x.split("\t\t") | .first()}) | Ui.pick "select cmd:"
    lf -remote `send $id :$cmd`
}}
map <c-e> all-cmd

cmd history-cmd ${{
    let cmd = lf -remote `query $id history` | .lines() | Ui.pick "history command:" | .split("\t\t") | .last()
    lf -remote `send $id $cmd`
}}
map <backspace> history-cmd
map <backspace2> history-cmd

cmd history-dir ${{
  let hist = lf -remote `query $id jumps` | Into.table('jump','path') | .drop(1) | Ui.pick "choose history:"
  lf --remote `send $id cd ${$hist.path}`
}}
map <c-g> history-dir

cmd quit-print ${{ print $fx ; lf -remote `send $id quit` }}
map <c-o> quit-print

map <f-12> ${{ lf -remote `query $id maps` | less }}

cmd edit-config ${{
    hx ~/.config/lf/lfrc
    lf -remote `send $id source ~/.config/lf/lfrc`
}}
map zc edit-config

cmd toggle-preview %{{
    match $lf_preview {
        'true' => lf -remote `send $id :set nopreview; set ratios 1:5`
        _ => lf -remote `send $id :set preview; set ratios 1:2:3`
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
        lf -remote `send $id :set user_wheel;set borderfmt \033[32m; set promptfmt '\033[32;1m%u@%h\033[0m:\033[34;1m%d\033[0m\033[1m%f\033[0m'`
    }else{
        lf -remote `send $id :set user_wheel pkexec;set borderfmt \033[31m; set promptfmt '\033[5;5mSUPER\033[0m $id \033[0m:\033[34;1m%d\033[0m\033[1m%f\033[0m'`
    }
}}
map zz toggle-super

map <c-s> source ~/.config/lf/lfrc

map <tab> half-down
map <backtab> half-up
map J push 3j
map K push 3k
map <c-j> push 7j
map <c-k> push 7k
map <a-j> push 10j
map <a-k> push 10k
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

cmd cd-usermedia &{{
    lf -remote `send $id cd $XDG_RUNTIME_DIR/media`
}}
map gi cd-usermedia

cmd follow-link %{{
    let real=readlink $f
    lf -remote `send $id select $real`
}}
map gL follow-link

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

map , find
map <lt> find-prev
map <gt> find-next

cmd fzf-edit $ hx (fzf)
map fe fzf-edit

cmd fzf-file ${{
    let ext = len($argv) ? '-e'+$argv[0] : ''
    let selected = fd --type 'file' $ext '-S-50k' -j 4 | fzf --preview "~/.config/lf/previewer {} 30 18"
    lf -remote `send $id select $selected`
}}
map ff<space> push :fzf-file<space>
map fft fzf-file txt
map ffg fzf-file gz
map ffm fzf-file md
map ffs fzf-file sh
map ffy fzf-file py

cmd fzf-folder ${{
  select = $lf_user_wheel fd --type d '.' -d 5 -j 4 | fzf --preview 'ls {}'
    lf -remote `send $id cd $select`
}}
map fd fzf-folder

cmd fzf-content ${{
    let file_type = len($argv)>0 ? $argv[0] : 'sh'
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
    if $res {
          let a = $res.split(':').take(3).join(':')
          $lf_user_wheel hx $a
    }
}}
map fc<space>  push :fzf-content<space>
map fct fzf-content txt
map fcm fzf-content md
map fcs fzf-content sh
map fcy fzf-content py
map fcj fzf-content js


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
map Fs setfilter .sh
map Fy setfilter .py
map Fc setfilter



map yy copy

cmd yank-path &{{
    $fx.lines().join("\n") | wl-copy
}}
map yp yank-path

cmd yank-name &{{
    $fx.lines() | .map(Fs.base_name) | .join("\n") | wl-copy
}}
map yn yank-name

cmd yank-basename &{{
    $fx.lines() | .map(x -> {Fs.base_name(True,$x) | .first()}) | .join("\n") | wl-copy
}}
map yb yank-basename

cmd yank-clear &{{
  '' >! ~/.local/share/lf/files
  lf -remote 'send clear'
}}
map yu yank-clear

cmd yank-list $$fx | hx
map \| yank-list

map yc cut

cmd delete ${{
  println '=====DELETE====='.red().bold() $fx '================'.red()
  if Ui.confirm('Delete these files [y/n]:'){
    $lf_user_wheel rm -rf $fx.lines()
  }
}}
map dD delete

cmd trash %{{
    let files = $fx.lines() | List.map(Fs.base_name)
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

        '' >! ~/.local/share/lf/files
        lf -remote 'send clear'
        for file in $base_names {
            lf -remote `send $id :select "$file"; tag $tg`
        }
    }
}}
map po mpaste --backup=numbered --force
map pb mpaste --backup=numbered
map pO mpaste --force
map pi mpaste -i
map pn mpaste -n
map pu mpaste --update
map pP mpaste --update --preserve
map ps mpaste --symbolic-link
map pl mpaste --link
map pH mpaste -H
map pp paste


cmd link %{{
    let load= Fs.read ~/.local/share/lf/files | .lines()
    let files=$load.drop(1)
    let file_count = len($files)
    if $file_count==0 {
        print 'No files yanked'
        exit 0
    }
    let mode=$load.at(0)
    let base_names = $files.map(Fs.base_name)

    for filex in $base_names{
        if (Fs.exists Fs.join('.',$filex)) {
            eprint $filex 'Already exists!'
            exit 1
        }
    }
    match $mode {
        copy => $lf_user_wheel ln -s -- $files '.'
        move =>  $lf_user_wheel ln -- $files '.'
    }

    '' >! ~/.local/share/lf/files
    lf -remote 'send clear'
    for file in $base_names {
        lf -remote `send $id :select "$file"; tag '@'`
    }

}}
map pL link

cmd paste-rsync %{{
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

    '' >! ~/.local/share/lf/files
}}
map pr paste-rsync

cmd paste-to %{{
    let dest = $argv[0] ?: {print 'Cancelled';exit 0}
    $lf_user_wheel cp -r --backup=numbered -i -- $fx $dest
    if Fs.is_dir($dest){
        let base_names = $fx.lines() | .map(Fs.base_name) | .join("\n")
        lf -remote `send $id :unselect; cd $dest; select $base_names`
    }else{
        lf -remote `send $id :unselect; select $dest; `
    }
}}
map pt push :paste-to<space>

cmd paste-from %{{
    let dest = $argv[0] ?: {print 'Cancelled';exit 0}
    $lf_user_wheel cp -r --backup=numbered -i -- $dest '.'
    let base_name = Fs.base_name($dest)
    lf -remote `send $id :unselect; select $base_name; `
}}
map pf push :paste-from<space>


cmd rename-to %{{
    let base_name = Fs.base_name($fx)
    let new_name =read `rename "$base_name" to:`
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

cmd bulk-rename ${{
    let new=mktemp
    $fs + "\n" >! $new
    helix $new
    let old_files = $fs.lines()
    let new_files = Fs.read $new | .lines()
    lf -remote `send $id unselect`
    for pair in List.zip($old_files,$new_files){
        if $pair[0] != $pair[1]{
            $lf_user_wheel mv -- $pair[0] $pair[1]
            lf -remote `send $id select ${$pair[1]}`
        }
    }
    rm $new
}}

map cb bulk-rename

cmd chmod ${{
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

cmd mkfile %{{
    if len($argv)>0 {
        $lf_user_wheel touch -- $argv
        for file in $argv{
            lf -remote `send $id select $file; tag '+'`
        }
    }
}}
map mf push :mkfile<space>

cmd mkdirs ${{
    if $argv {
        $lf_user_wheel mkdir -p -- $argv
        let name = ""
        for file in $argv{
            if !$file.starts_with('/'){
                name = Fs.base_name($file)
                lf -remote `send $id :select $name; tag '+'`
            }
        }
    }
}}
map mk push :mkdirs<space>

cmd folder-selected %{{
    let dest = read "Fold to :"
    if $dest {
        if Fs.exists($dest){
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


map i ${{let LESSOPEN=' | ~/.config/lf/previewer %s 20 30'; less -R -k ~/.config/lf/less.lesskey $f}}

map En &geany $fx
map Ec &code $fx
map Ep &lapce $fx
map Eg &geany $fx
map Ee &gedit $fx
map Ea &apostrophe $fx
map El &lite-xl $fx
map Em &marker $fx
map Er &retext $fx
map Ev &vi $fx
map Ez &zed $fx

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
    pgrep -x cmus ?: foot cmus
    cmus-remote -c -q $fx
    cmus-remote -p -q
}}
map Om cmus-play
cmd open-handlr $ handlr open $fx; lf -remote `send $id unselect`
map o open-handlr

cmd open-with-gui &$argv[0] $fx ## opens with a gui application outside lf client
map Og push :open-with-gui<space> ## input application

cmd open-with-cli $$argv[0] $fx ## opens with a cli application inside lf client
map Oc push :open-with-cli<space> ## input application

map Ox &xarchiver $f

cmd extract-to %{{
    let dest = $argv[0] ?: {print 'Cancelled'; exit 0}
    if (Regex.match '\.([gb7xs]z|t[gbx]z|zip|zst|bz2|lz4|lzma|tar|rar|br)$' $f) {
        let base_name = Fs.base_name(True,$f).first()
        let npath = Fs.join($dest,$base_name)
        $lf_user_wheel ouch -q decompress --dir $npath $f
        lf -remote `send $id :cd $dest; select $base_name; tag '^'`
    }else{
        print 'Unsupported file extention'
    }
}}
map ah push :extract-to<space>.<enter>
map ax push :extract-to<space>/tmp/
map aX push :extract-to<space>

cmd compress-to ${{
    let dest = $argv[0] ?: {print 'Cancelled'; exit 0}
    let sources = $fx.lines()
    if $dest.ends_with('/'){
        let base_name = Fs.base_name($sources.first())
        let dest_file = Fs.join($dest, $base_name)
    }else{
        let base_name = Fs.base_name($dest)
        let dest_file = $dest
    }
    if !(Regex.match '\.(gz|tgz|zip|tar|7z|bz|bz2|xz|lzma|sz|lz4|zst|rar)$' $base_name) {
        base_name = $base_name + '.tgz'
        dest_file = $dest_file + '.tgz'
    }
    $lf_user_wheel ouch compress -qSg $sources $dest_file
    let dir = Fs.dir_name($dest)
    lf -remote `send $id :cd $dir; select $base_name; tag '#'`
}}
map ac push :compress-to<space>/tmp/

cmd archive-mount ${{
    let base_name = Fs.base_name($f)
    let mntdir=`/tmp/lf/mount/$base_name`
    mkdir -p $mntdir
    $lf_user_wheel archivemount $f $mntdir -o nosave
    lf -remote `send $id cd $mntdir`
}}
map am archive-mount

map mb mark-save

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
        let s1 = $lines[0] | .words() | .at(0)
        let s2 = $lines[1] | .words() | .at(0)
        print $s1==$s2 ? 'Same' : 'Differ'
    }else{
        echo 'please select 2 files!'
    }
}}
map dm diff-md5

cmd check-sum %{{
    let ext_name = Fs.base_name(True, $fx).last()
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

map d<space> !dust



cmd mount-dev ${{
    let sel =  lsblk -rno 'name,type,size,mountpoint,label,fstype' | Into.table([name,'type',size,mountpoint,label,fstype]) \
        | where($type!='disk' && !$mountpoint && $fstype !~: 'member') \
        | Ui.pick "which to mount:" ?: { print 'no device'; exit 0 }

    if $sel {
        if !$lf_user_wheel {
            eprint 'Must be root to mount'
            exit 1
        }
        let src = $sel.type == 'part' ? `/dev/${$sel.name}` : `/dev/mapper/${$sel.name}`
        let point = $sel.label==None ? $sel.name : $sel.label
        let dest = `$XDG_RUNTIME_DIR/media/$point`
        if !Fs.exists($dest){ mkdir -p $dest }
        $lf_user_wheel mount -m -o 'defaults,noatime' $src $dest  ?: \
            e -> {notify-send 'Mount Failed' $e.msg.lines().join(';'); exit 1}
        lf -remote `send $id cd $dist`
        notify-send 'Mount' `device $src mounted`
    }
}}
map mm mount-dev

cmd umount-dev ${{
    let sel =  lsblk -rno 'name,type,size,mountpoint,label,fstype' | Into.table([name,'type',size,mountpoint,label,fstype])     \
        | where(mountpoint != None) \
        | Ui.pick() ?.

    if sel {
        if $PWD ~: $sel.mountpoint {
            lf -remote `send $id cd /tmp`
        }
        $lf_user_wheel umount $sel.mountpoint ?: \
            e -> {notify-send 'Umount Failed:' $e.msg.lines().join(';'); exit 1}
        lf -remote `send $id reload`
        notify-send 'Umount' `device ${sel.name} umounted`
    }
}}
map mu umount-dev

map T tag-toggle

cmd drag-in %{{
  dest=dragon-drop --target -x -p
  cp $dest .
  let base_name = Fs.base_name($dest)
  lf -remote `send $id :select ${base_name}; tag =`
}}
map di drag-in

cmd drag-out &dragon-drop $fx
map do drag-out

cmd on-cd &{{
    Sys.print_tty `\033]0;lf $PWD\007`
}}

on-cd

```
