---
title: lf文件管理器配置对比 D
date: 2025-07-15 10:16:45
highlight: true
tags:
 - case
categories:
 - wiki
 - case
---

# lf filemanager 配置对比 D

### 31. rename-to 命令
**按键绑定**: `mv`

**写法对比**:
- **lumesh**: 使用内置文件函数和字符串插值
```bash
let base_name = Fs.base_name($fx)
let new_name =read `rename "$base_name" to:`
if $new_name {
    $lf_user_wheel mv -- $base_name $new_name
    lf -remote `send $id :select $new_name`
}
```

- **bash**: 使用basename和printf
```bash
fn=$(basename "$fx")
printf "rename $fn to:"
read ans
[ -n "$ans" ] && $lf_user_wheel mv -- $fn $ans
```

**优势说明**:
- **lumesh**: 内置文件函数类型安全，字符串插值更直观
- **bash**: basename命令标准，条件判断简洁

### 32. chmod 命令
**按键绑定**: `cm`

**写法对比**:
- **lumesh**: 使用管道操作符和循环
```bash
let ans = read "Mode Bits:"
if $ans {
    $fx |> $lf_user_wheel chmod $ans _
    lf -remote 'send reload'
}
```

- **bash**: 使用xargs并行处理
```bash
printf "\nMode Bits: "
read ans
if [ -n "$ans" ]; then
    set -f
    printf "%s\n" $fx |xargs -P 4 -i $lf_user_wheel chmod $ans {}
    lf -remote 'send reload'
fi
```

**优势说明**:
- **lumesh**: 管道操作符`|>`语法现代化，循环处理直观
- **bash**: xargs并行处理性能更好，`-P 4`支持多进程

### 33. chown 命令
**按键绑定**: `co`, `cO` (递归)

**写法对比**:
- **lumesh**: 使用管道操作符
```bash
let ans = read "new Owner:Group :"
if $ans {
    $fx |> $lf_user_wheel chown $argv $ans -- _
    lf -remote 'send reload'
}
```

- **bash**: 使用传统for循环
```bash
printf "\nnew Owner:Group : "
read ans
if [ -n "$ans" ]; then
    set -f
    for file in "$fx"
    do
        $lf_user_wheel chown $@ $ans $file
    done
    lf -remote 'send reload'
fi
```

**优势说明**:
- **lumesh**: 管道操作符统一处理，语法一致性好
- **bash**: for循环控制精确，错误处理更细粒度

### 34. mkfile 命令
**按键绑定**: `mf`

**写法对比**:
- **lumesh**: 使用条件判断和循环
```bash
if len($argv)>0 {
    $lf_user_wheel touch -- $argv
    for file in $argv{
        lf -remote `send $id select $file; tag '+'`
    }
}
```

- **bash**: 使用参数检查
```bash
if [ -n "$@" ];then
    $lf_user_wheel touch -- "$@";
    lf -remote "send $id select $@"
fi
```

**优势说明**:
- **lumesh**: `len()`函数语义清晰，循环处理每个文件
- **bash**: 参数检查简洁，批量选择效率高

### 35. mkdirs 命令
**按键绑定**: `mk`

**写法对比**:
- **lumesh**: 使用字符串方法和条件判断
```bash
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
```

- **bash**: 使用cut命令提取目录名
```bash
set -f
$lf_user_wheel mkdir -p -- "$@"
for file in "$@";do
    lf -remote "send $id :select $(echo $file| cut -d'/' -f1); tag +"
done
```

**优势说明**:
- **lumesh**: 字符串方法`.starts_with()`直观，路径处理类型安全
- **bash**: cut命令处理路径分割高效，set -f禁用通配符扩展安全

### 36. folder-selected 命令
**按键绑定**: `ms`

**写法对比**:
- **lumesh**: 使用内置函数和错误检查
```bash
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
```

- **bash**: 使用printf和传统工具
```bash
set -f
printf "Directory name: "
read newd
$lf_user_wheel mkdir -- "$newd"
$lf_user_wheel mv -- $fx "$newd"
lf -remote "send $id select \"$newd\""
```

**优势说明**:
- **lumesh**: 内置存在性检查，错误处理更完善
- **bash**: 直接创建目录，流程更简洁

### 37. 编辑器启动命令
**按键绑定**: `En` (geany), `Ec` (code), `Ep` (lapce), `Eg` (geany), `Ee` (gedit), `Ea` (apostrophe), `El` (lite-xl), `Em` (marker), `Er` (retext), `Ev` (vi), `Ez` (zed)

**写法对比**:
- **lumesh**: 直接使用变量
```bash
&geany $fx
&code $fx
&lapce $fx
```

- **bash**: 使用引号保护
```bash
&geany "$fx"
&code "$fx"
&lapce "$fx"
```

**优势说明**:
- **lumesh**: 变量展开自动处理空格
- **bash**: 引号保护防止参数分割，更安全

### 38. 终端启动命令
**按键绑定**: `rr` (foot lf), `rt` (thunar), `rs` (spacefm), `rh` (hx), `rc` (code), `rp` (lapce), `rn` (geany), `rl` (lite-xl), `rz` (zed)

**写法对比**:
- **lumesh**: 使用字符串字面量
```bash
&$lf_user_wheel foot lf '.'
&thunar '.'
&hx '.'
```

- **bash**: 同样使用字符串字面量
```bash
&$lf_user_wheel foot lf .
&thunar .
&hx .
```

**优势说明**:
- 两种版本基本相同，都是简单的命令调用

### 39. open-with-gui/cli 命令
**按键绑定**: `Og` (GUI应用), `Oc` (CLI应用)

**写法对比**:
- **lumesh**: 使用数组索引
```bash
&$argv[0] $fx    ## GUI应用
$$argv[0] $fx    ## CLI应用
```

- **bash**: 使用位置参数
```bash
&$@ $fx    ## GUI应用
$$@ $fx    ## CLI应用
```

**优势说明**:
- **lumesh**: 数组索引明确指定第一个参数
- **bash**: 位置参数`$@`展开所有参数，更灵活

### 40. archive-mount 命令
**按键绑定**: `am`

**写法对比**:
- **lumesh**: 使用字符串插值和内置函数
```bash
let base_name = Fs.base_name($f)
let mntdir=`/tmp/lf/mount/$base_name`
mkdir -p $mntdir
$lf_user_wheel archivemount $f $mntdir -o nosave
lf -remote `send $id cd $mntdir`
```

- **bash**: 使用命令替换
```bash
mntdir="/tmp/lf/mount/$(basename $f).mnt"
mkdir -p "$mntdir"
$lf_user_wheel archivemount "$f" "$mntdir" -o nosave
lf -remote "send $id cd $mntdir"
```

**优势说明**:
- **lumesh**: 内置文件函数类型安全，字符串插值清晰
- **bash**: basename命令标准，添加.mnt后缀避免冲突

## 最终对比总结

### 语法现代化程度

| 语法特性 | Lumesh | Bash | 现代化程度 |
|----------|--------|------|------------|
| 条件表达式 | `condition ? value : default` | `[ condition ] && value \|\| default` | Lumesh更现代 |
| 字符串方法 | `.starts_with()`, `.ends_with()` | `grep`, `test` | Lumesh更直观 |
| 文件操作 | `Fs.base_name()`, `Fs.exists()` | `basename`, `test -e` | Lumesh类型安全 |
| 数组处理 | `.lines()`, `.map()` | `awk`, `cut`, `sed` | Lumesh函数式 |
| 错误处理 | `?:`, `?.` | `&&`, `\|\|` | Lumesh更简洁 |

### 性能和资源使用

| 方面 | Lumesh | Bash |
|------|--------|------|
| 启动速度 | 7.40ms（lume版本，含repl和内置库） | 4.78ms |
|      | 6.60ms（lumesh版本，含内置库，不含repl） |  |
| 内存使用 | 6MB | 8MB |
| 工具依赖 | 内置丰富功能 | 依赖系统工具 |

- 字符串处理效率对比
以all-cmds命令中的字符串处理为例：

**数据准备**
```bash
lf -remote `query $id cmds` >> /tmp/cmds
```

**Lumesh 脚本**
```bash
# /tmp/cc.lm
for i in 0..100{
  Fs.read /tmp/cmds | String.lines() | List.drop(1) | List.map(x -> {String.split("\t\t", $x) | List.first()}) | print
}
```

**Bash 脚本**
```bash
# /tmp/cc.lm
for ((i=0;i<=100;i++))
do
	cat /tmp/cmds | awk -F'\t' 'NR>1 {print $1}'
done
```

**执行时间对比**
```bash
# Lume
> time lume /tmp/cc.lm
________________________________________________________
Executed in   32.25 millis    fish           external
   usr time   25.65 millis  859.00 micros   24.79 millis
   sys time    6.79 millis  588.00 micros    6.20 millis

# Bash
> time bash /tmp/cc.sh
________________________________________________________
Executed in  511.61 millis    fish           external
   usr time  230.11 millis    1.69 millis  228.42 millis
   sys time  454.95 millis    0.00 millis  454.95 millis

# Dash 和 Bash结果基本相同
```

### 维护性和可读性

**Lumesh优势**:
- 统一的API设计和命名规范
- 类型安全减少运行时错误
- 现代化语法提高代码可读性
- 内置错误处理机制

**Bash优势**:
- 成熟的生态系统和工具链
- 广泛的社区支持和文档
- 标准化的错误处理模式
- 跨平台兼容性极佳

## Notes

通过对所有lf配置命令的完整对比分析，可以看出：

1. **功能等价性**: 两种实现都能完成相同的文件管理任务，按键绑定完全一致
2. **语法差异**: Lumesh体现了现代shell语言的设计理念，而Bash保持了传统Unix哲学
3. **选择建议**:
   - 选择Lumesh：追求现代化语法、类型安全、代码可读性
   - 选择Bash：注重兼容性、性能、工具生态成熟度

两种实现都是优秀的lf配置方案，选择主要取决于用户的技术栈偏好和具体需求。

Wiki pages you might want to explore:

- [语法概览 (superiums/lumesh)](/zh-cn/glance)
- [特性概览 (superiums/lumesh)](/zh-cn/overview)
- [日常用举例 (superiums/lumesh)](/zh-cn/cases)
- [使用Lumesh编写lf配置文件的语法演示](/zh-cn/case_lf)
- [语法手册 (superiums/lumesh)](/zh-cn/syntax)
- [内置函数 (superiums/lumesh)](/zh-cn/libs/index)
