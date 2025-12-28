---
title: dash写的lf文件管理器配置
date: 2025-07-05 19:16:45
highlight: true
tags:
 - glance
categories:
 - wiki
 - why
 - syntax
---

# dash写的lf文件管理器配置

[使用Lumesh编写lf配置文件的语法演示](/zh-cn/cases/case_lf)

```bash
set shell sh
set shellopts '-eu'
set ifs "\n"
 [ "$LF_LEVEL" -eq 1 ] || lf -remote "send $id echoerr \"Warning: You're in a nested lf instance: Level $LF_LEVEL !\""
& [ -d "/tmp/lf" ] || mkdir /tmp/lf
& [ -f "/tmp/lf/files" ] || (touch /tmp/lf/files /tmp/lf/tags /tmp/lf/history && echo 'copy'>/tmp/lf/files)
#
et errorfmt "\033[1;43;41m"
set drawbox
set roundbox
set showbinds
et scrolloff 10
set icons
et incsearch
set incfilter
et ignorecase
set anchorfind false
set findlen 0
set tabstop 4
set info 'size'
set preview
set sixel
set previewer ~/.config/lf/previewer
et dircounts
set selmode 'dir'
et user_wheel ''
ap d
map y
map p
map m
ap f
map c
map r
map t
map G
map F
map w

md profile &lf -remote "send $id source ~/.config/lf/profiles/$1"
map z2 profile extra
map z3 profile disk
map z4 profile convert
map z5 profile develop
map z6 profile auto-redraw
map z7 profile tarzip

md open %echo 'not dir'
map e $$lf_user_wheel hx "$f"
map zm set info perm
map zu set info user:group

ap W $fish
map . :read; cmd-history-prev;
map <a-\;> push :<space>$fx<home>
map <a-4> push $<space>$fx<home>
map <a-7> push &<space>$fx<home>
map <a-5> push %<space>$fx<home>
map <a-1> push !<space>$fx<home>

cmd all-cmd ${{
    clear
    cmd=$(
        lf -remote "query $id cmds" |
        awk -F'\t' 'NR > 1 { print $NF}' |
        sort -u |
        fzf --reverse --prompt='Execute command: ' --preview=''
    )
    lf -remote "send $id $cmd"
}}
map <c-e> all-cmd

cmd history-cmd ${{
    clear
    cmd=$(
        lf -remote "query $id history" |
        awk -F'\t' 'NR > 1 { print $NF}' |
        sort -u |
        fzf --reverse --prompt='Execute command: ' --preview=''
    )
    lf -remote "send $id $cmd"
}}
map <backspace> history-cmd
map <backspace2> history-cmd
cmd history-dir ${{
    clear
    cmd=$(
        lf -remote "query $id jumps" |
        awk -F'\t' 'NR > 1 { print $NF}' |
        sort -u |
        fzf --reverse --prompt='Jump to: ' --preview=''
    )
    lf -remote "send $id $cmd"
}}
map <c-g> history-dir

md quit-print ${{ echo $fx ; lf -remote "send $id quit" }}
map <c-o> quit-print

ap <f-12> maps

cmd edit-config ${{
    hx ~/.config/lf/lfrc ||  $EDITOR ~/.config/lf/lfrc
    lf -remote "send $id source ~/.config/lf/lfrc"
}}
map zc edit-config

cmd toggle-preview %{{
    if [ "$lf_preview" = "true" ]; then
        lf -remote "send $id :set nopreview; set ratios 1:5"
    else
        lf -remote "send $id :set preview; set ratios 1:2:3"
    fi
}}
map zp toggle-preview

cmd parent-panel-off ${{
    lf -remote "send $id :set preview; set ratios 2:3"
}}
map zP parent-panel-off

cmd toggle-super ${{
  [ -z "$lf_user_wheel" ] && \
    lf -remote "send $id :set user_wheel \"/home/$USER/.config/lf/pkexec\"; set borderfmt '\033[33;41m'; \
    set promptfmt \"\033[5;5mSUPER\033[0m \033[0m:\033[34;1m%d\033[0m\033[1m%f\033[0m\"" \
    || \
    lf -remote "send $id :set user_wheel; set borderfmt '\033[0m'; \
    set promptfmt \"\033[32;1m%u@%h\033[0m:\033[34;1m%d\033[0m\033[1m%f\033[0m\""

}}
map zz toggle-super

ap <c-s> source ~/.config/lf/lfrc

ap <tab> half-down
map <backtab> half-up
map J push 3j
map K push 3k
map <c-j> push 7j
map <c-k> push 7k
map <a-j> push 10j
map <a-k> push 10k
ap g<space> push :cd<space>
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
ap gG bottom

md z %{{
  if [ -n "$@" ]; then
	  select="$(zoxide query --exclude $PWD $@)" \
      && lf -remote "send $id cd $select" \
      || lf -remote "send $id echo Cancelled."
  fi
}}
map ; push :z<space>
map gz push :z<space>

cmd zoxide-query ${{
  select="$(zoxide query -i)" \
      && lf -remote "send $id cd $select" \
      || lf -remote "send $id echo Cancelled."
}}
map gq zoxide-query

md cd-usermedia & lf -remote "send $id cd /run/user/$(id -u)/media"
map gi cd-usermedia

md follow-link %{{
  lf -remote "send ${id} select '$(readlink $f)'"
}}
map gL follow-link

md select-files &{{
    set -f  # 禁用通配符扩展
    get_files() {
        if [ "$lf_hidden" = 'false' ]; then

            fd --exact-depth 1 $@ -c never -j 1 -0
        else
            fd --exact-depth 1 $@ -H -c never -j 1 -0
        fi |
        xargs -0 printf ' %q'
    }

    lf -remote "send $id :unselect; toggle $(get_files $@)"
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

ap , find
ap <lt> find-prev
map <gt> find-next
map fe $hx $(fzf)

md fzf-file ${{
  [ -n ${1:-''} ] && E="-e$1" || E='-S-50k'
  select=$($lf_user_wheel fd --type f $E | fzf --preview "~/.config/lf/previewer {} 30 18") \
    && lf -remote "send $id select $select" \
    || lf -remote "send $id echo Cancelled."
}}
map ff push :fzf-file<space>
map fnt fzf-file txt
ap fnw fzf-file docx
map fnx fzf-file xlsx
map fng fzf-file gz
map fnz fzf-file zip
map fnm fzf-file md
map fns fzf-file sh
map fny fzf-file py

md fzf-folder ${{
  select=$($lf_user_wheel fd --type d . -d 5 | fzf --preview 'ls {}') \
    && lf -remote "send $id cd $select" \
    || lf -remote "send $id echo Cancelled."
}}
map fd fzf-folder

md fzf-rg ${{
    set -f  # 禁用通配符扩展
    RG_PREFIX="$lf_user_wheel rg --column --line-number --no-heading --color=always --smart-case --max-filesize 50K"
    [ -n ${1:-''} ] && RG_PREFIX="$RG_PREFIX --type $1"
    res=$(
      : | fzf --ansi --disabled \
          --bind "start:reload:$RG_PREFIX {q}" \
          --bind "change:reload:sleep 0.1; $RG_PREFIX {q} || true" \
          --bind "alt-enter:unbind(change,alt-enter)+change-prompt(2. fzf> )+enable-search+clear-query" \
          --color "hl:-1:underline,hl+:-1:underline:reverse" \
          --prompt '1. ripgrep> ' \
          --delimiter : \
          --preview 'bat --color=always {1} --highlight-line {2}' \
          --header "Searching Content in FileType: ${1:-'*'}"
    )
    [ -n "$res" ] && $lf_user_wheel hx $(echo $res|cut -d: -f1) +$(echo $res|cut -d: -f2)
}}
map fr  push :fzf-rg<space>
map fct :fzf-rg txt
map fcm :fzf-rg md
map fcs :fzf-rg sh
map fcy :fzf-rg py
map fcj :fzf-rg js


ap \\ filter
map Fi filter
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
ap yy copy

md yank-name &{{
  basename -a -- "$fx" | head -c-1 | wl-copy
}}
map yn yank-name

md yank-path &{{
  echo $fx | tr ' ' '\n' | wl-copy
}}
map yp yank-path

cmd yank-basename &basename -a -- "$fx" | cut -d. -f1 | head -c-1 | wl-copy
map yb yank-basename

cmd yank-clear &{{
  echo > ~/.local/share/lf/files
  lf -remote "send clear"
}}
map yu yank-clear

cmd yank-list $echo $fx | hx
map \| yank-list

ap yc cut

md delete ${{
  set -f
  echo
  echo -e "=====\033[31mDELETE\033[0m====="
  echo "$fx"
  echo "================"
  printf "Delete %d files? [y/N]" $(wc -w <<< $fx)
  read ans
  if [ "$ans" = "y" ]; then
    echo $fx | xargs $lf_user_wheel rm -rf
  fi
}}
map dD delete

cmd trash %{{
  set -f
  printf "Temparay Trash %d files? [y/N]" $(wc -w <<< $fx)
  read ans
  if [ "$ans" = "y" ]; then
    [ -d "/tmp/.trash" ] || mkdir -p /tmp/.trash
    echo $fx | xargs $lf_user_wheel rm -rf
    echo "Trash complete!"
  else
    echo "Canceled!"
  fi
}}
map dd trash

cmd paste ${{
    load=$(cat ~/.local/share/lf/files)
    mode=$(echo "$load" | sed -n '1p')
    list=$(echo "$load" | sed '1d')
    [ -z "$list" ] && echo 'No files yanked' && exit 0
    fn=$(basename -a -- $list)
    printf "$mode %d files? [y/N]" $(wc -w <<< $fn)
    read ans
    if [ "$ans" = "y" ]; then
      if [ "$mode" = 'copy' ]; then
          $lf_user_wheel cp -r $@ -- $list .
          $lf_user_wheel cp -r $@ -- $list .
          tg='='
      elif [ "$mode" = 'move' ]; then
          $lf_user_wheel mv -- $list .
          tg='>'
      fi
      echo > ~/.local/share/lf/files
      lf -remote "send clear"
      for file in $fn;do
        lf -remote "send $id :select \"$file\"; tag $tg"
      done
    fi
}}
ap po paste --backup=numbered --force
map pb paste --backup=numbered
ap pO paste --force
map pp paste -i
map pn paste -n
map pu paste --update
map pP paste --update --preserve
ap ps paste --symbolic-link
map pl paste --link
map pH paste -H


cmd link %{{
    load=$(cat ~/.local/share/lf/files)
    mode=$(echo "$load" | sed -n '1p')
    list=$(echo "$load" | sed '1d')
    if [ "$mode" = 'copy' ]; then
        $lf_user_wheel ln -s $list .
    elif [ "$mode" = 'move' ]; then
        $lf_user_wheel ln $list .
    fi
    echo > ~/.local/share/lf/files
    lf -remote "send clear"
    fn=$(basename -a -- $list)
    for file in "$fn";do
      lf -remote "send $id :select $file; tag @"
    done
}}
map pL link

md paste-rsync &{{
    set -- $(cat ~/.local/share/lf/files)
    mode="${1:-}"
    shift
    case "$mode" in
        copy)
            $lf_user_wheel rsync -av --ignore-existing --progress -- "$@" . |
            stdbuf -i0 -o0 -e0 tr '\r' '\n' |
            while IFS= read -r line; do
                lf -remote "send $id echo $line"
            done
            ;;
         move)
            $lf_user_wheel mv -n -- "$@" .
            ;;
    esac
    echo > ~/.local/share/lf/files
    lf -remote "send clear"
}}
map pr paste-rsync

cmd paste-to %{{
   if [ -n "${1:-}" ]; then
     $lf_user_wheel cp -r --backup=numbered -i -- $fx $1
     [ -d "$1" ] \
       && lf -remote "send $id :unselect; cd $1; select $(basename $fx)" \
       || lf -remote "send $id :unselect; select $1; "
   else
     echo "Cancelled."
   fi
}}
map pt push :paste-to<space>

cmd paste-from %{{
   if [ -n "${1:-}" ]; then
     $lf_user_wheel cp -r --backup=numbered -i -- $1 .
     fn=$(basename "$1")
     lf -remote "send $id :unselect; select $fn; "
   else
     echo "Cancelled."
   fi
}}
map pf push :paste-from<space>


md rename-to %{{
    fn=$(basename "$fx")
    printf "rename $fn to:"
    read ans
    [ -n "$ans" ] && $lf_user_wheel mv -- $fn $ans
}}
map mv rename-to

map ch :rename; cmd-home                  #rename head
map ct :rename; cmd-end                   #rename tail
map ca rename                             #rename after basename
map cn :rename; cmd-delete-home           #rename basename
map ce push ca<c-f><c-k>                  #rename extension
map cf :rename; cmd-end; cmd-delete-home  #rename fullname

md bulk-rename ${{
  old="$(mktemp)"
  new="$(mktemp)"
  printf '%s\n' "$fs" >"$old"
  printf '%s\n' "$fs" >"$new"
  helix "$new"
  [ $(wc -l < "$new") -ne $(wc -l < "$old") ] && exit
  paste "$old" "$new" | while IFS= read -r names; do
    src="$(printf '%s' "$names" | cut -f1)"
    dst="$(printf '%s' "$names" | cut -f2)"
    if [ "$src" = "$dst" ] || [ -e "$dst" ]; then
        continue
    fi
    $lf_user_wheel mv -- "$src" "$dst"
  done
  rm -- "$old" "$new"
  lf -remote "send $id unselect"
}}

map cb bulk-rename

md chmod %{{
  printf "\nMode Bits: "
  read ans
  if [ -n "$ans" ]; then
    set -f
    printf "%s\n" $fx |xargs -P 4 -i $lf_user_wheel chmod $ans {}

    lf -remote 'send reload'
  fi
}}
map cm chmod

cmd chown %{{
  printf "\nnew Owner:Group : "
  read ans
  if [ -n "$ans" ]; then
    set -f  # 禁用通配符扩展
    for file in "$fx"
    do
      $lf_user_wheel chown $@ $ans $file
    done
    lf -remote 'send reload'
  fi
}}
map co chown
map cO chown -R

md mkfile %{{
  if [ -n "$@" ];then
    $lf_user_wheel touch -- "$@";
    lf -remote "send $id select $@"
  fi
}}
map mf push :mkfile<space>

md mkdirs ${{
    set -f  # 禁用通配符扩展
    $lf_user_wheel mkdir -p -- "$@"
    for file in "$@";do
      lf -remote "send $id :select $(echo $file| cut -d'/' -f1); tag +"
    done
}}
map mk push :mkdirs<space>

md folder-selected %{{
  set -f
  printf "Directory name: "
  read newd
  $lf_user_wheel mkdir -- "$newd"
  $lf_user_wheel mv -- $fx "$newd"
  lf -remote "send $id select \"$newd\""
}}
map ms folder-selected


ap i $LESSOPEN='| ~/.config/lf/previewer %s 20 30' less -R -k ~/.config/lf/less.lesskey "$f"

ap En &geany "$fx"
map Ec &code "$fx"
map Ep &lapce "$fx"
map Eg &geany "$fx"
map Ee &gedit "$fx"
map Ea &apostrophe "$fx"
map El &lite-xl "$fx"
map Em &marker "$fx"
map Er &retext "$fx"
map Ev &vi "$fx"
map Ez &zed "$fx"

ap rr &$lf_user_wheel foot lf .
map rt &thunar .
map rs &spacefm -t .
map rh &hx .
map rc &code .
map rp &lapce .
map rn &geany .
map rl &lite-xl .
map rz &zed .
cmd cmus-play &{{
    set -f  # 禁用通配符扩展
    if [ -z "$(pgrep -x cmus)" ]; then
      foot cmus && cmus-remote -c -q "$fx" && cmus-remote -p
    else
      cmus-remote -c -q "$fx"
      cmus-remote -p
    fi
}}
map Om cmus-play
md open-handlr $ handlr open $fx; lf -remote "send $id unselect"
map o open-handlr

cmd open-with-gui &$@ $fx ## opens with a gui application outside lf client
map Og push :open-with-gui<space> ## input application

cmd open-with-cli $$@ $fx ## opens with a cli application inside lf client
map Oc push :open-with-cli<space> ## input application

map Ox &xarchiver "$f"

md extract-to %{{
  set -f
  if [ -n "${1:-}" ]; then
    case "$f" in
      *.[gb7xs]z|*.t[gbx]z|*.zip|*.tar|*.bz2|*.lzma|*.lz4|*.zst|*.rar)
        $lf_user_wheel ouch d -qd $1 $f && \
        fn=$(basename "$f" | cut -d. -f1) && \
        lf -remote "send $id :cd $1; select $fn && tag ^"
        ;;
      *) echo "Unsupported format";
        exit
        ;;
    esac
  else
    echo 'Cancelled.'
  fi
}}
map ah push :extract-to<space>.<enter>
map ax push :extract-to<space>/tmp/
map aX push :extract-to<space>

md compress-to %{{
    if [ -n "${1:-}" ]; then
      if test "$(echo $1 | grep '/$')" -o -d "$1" ; then
        name="$(basename -a $fx | head -n1)"
        dir=$(dirname $1$name)
      else
        name=$(basename "$1")
        dir=$(dirname $1)
      fi
      test "$(echo $name | grep -E '\.(gz|tgz|zip|tar|7z|bz|bz2|xz|lzma|sz|lz4|zst|rar)$')" || name="$name.tgz"

      if [ ! -d "$dir" ]; then
        echo "$dir not exist, create? [y/N]: "
        read MK
        if [ "$MK" = "y" ]; then
          $lf_user_wheel mkdir $dir
        else
          echo "Cancelled."
          exit
        fi
      fi
      $lf_user_wheel ouch c -q $(basename -a "$fx") "$dir/$name" &&\
      lf -remote "send $id :cd $dir; select $name; tag '#'"
    else
      echo "Cancelled."
    fi
}}
map ac push :compress-to<space>/tmp/


cmd am ${{
    mntdir="/tmp/lf/mount/$(basename $f).mnt"
    mkdir -p "$mntdir"
    $lf_user_wheel archivemount "$f" "$mntdir" -o nosave
    lf -remote "send $id cd $mntdir"
}}
map am am
map mb mark-save

md diff !{{
  set -- $fx
  if [ "$#" -gt 1 ]; then
    $lf_user_wheel diff -w $1 $2 && echo 'Same'
  else
    echo 'please select 2 files!'
  fi
}}
map df diff

cmd delta !{{
  set -- $fx
  if [ "$#" -gt 1 ]; then
    $lf_user_wheel delta $1 $2 && echo "Same"
  else
    echo 'please select 2 files!'
  fi
}}
map dt delta

cmd diff-md5 %{{
  set -- $fx
  if [ "$#" -gt 1 ]; then
    sum1=$(md5sum $1 |cut -d' ' -f1)
    sum2=$(md5sum $2 |cut -d' ' -f1)
    if [ "$sum1" = "$sum2" ]; then
      lf -remote "send $id :select \"$1\"; tag ="
      lf -remote "send $id :select \"$2\"; tag ="
      echo 'Same'
    else
      lf -remote "send $id :select \"$1\"; tag !"
      lf -remote "send $id :select \"$2\"; tag !"
      echo 'Differ'
    fi
  else
    echo 'select 2 files to compare.'
  fi
}}
map dm diff-md5

cmd check-sum %{{
  case "$fx" in
    *.sha256)
      sha256sum -c "$fx" ;;
    *.sha512)
      sha512sum -c "$fx" ;;
    *.sha1)
      sha1sum -c "$fx" ;;
    *.md5)
      md5sum -c "$fx" ;;
    *)
      sha256sum "$fx"
      ;;
  esac
}}
map dc check-sum

ap d<space> !dust


cmd mount-dev ${{
  sel=$(lsblk -rno 'name,type,size,label,mountpoint,fstype' |\
      awk -F'[ ]' '$2!="disk" && $5=="" && $6!~/member/ { print $1,$2,$3,$4 }' |\
      fzf --prompt='choose to Mount: ' --preview='')
  if [ -n "$sel" ]; then
    x=$(echo "$sel" | cut -d' ' -f1)
    typ=$(echo $sel | cut -d' ' -f2)
    lab=$(echo $sel | cut -d' ' -f4)
    dist="/run/user/$(id -u)/media/${lab:-$x}"
    [ -d $dist ] || mkdir -p "$dist"
    [ "$typ" != "part" ] && x="mapper/$x"
    $lf_user_wheel mount -m -o defaults,noatime /dev/$x $dist \
      && lf -remote "send $id cd $dist" \
      && notify-send "device $x mounted" \
      || lf -remote "send $id echoerr Mount Failed:$?"
  else
    exit 0
  fi
}}
map mm mount-dev

cmd umount-dev ${{
  x=$(mount | awk '$1 ~ /^\/dev/ && $3 !~/^\/(home|boot|var)?$/ {sub(/^\/dev\//, "", $1); print $1,$5,$3}' |
    fzf --prompt='choose to UMount: ' --preview='' |
    awk '{print $3}')
  if [ -n "$x" ]; then
    [ -n $(echo $PWD | grep "^$x/") ] && dir=$(dirname $x) && lf -remote "send $id cd $dir"
    [ -z "$lf_user_wheel" ] && lf -remote "send $id echoerr Must Be ROOT" && exit 0
    $lf_user_wheel umount "$x" \
      && lf -remote "send $id reload"\
      && notify-send "$x removed"\
      || lf -remote "send $id echoerr Umount Failed:$?"
  else
    exit 0
  fi
}}
map mu umount-dev

ap T tag-toggle

md drag-in %{{
  dest=$(dragon-drop --target -x -p)
  cp $dest .
  lf -remote "send $id :select $(basename "$dest"); tag ="
}}
map di drag-in

cmd drag-out &dragon-drop $fx
map do drag-out

md on-cd &{{
    printf "\033]0;lf $PWD\007" > /dev/tty
}}

n-cd

```
