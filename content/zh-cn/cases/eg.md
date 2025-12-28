---
title: Lumesh 日常应用举例
date: 2025-07-05 19:16:45
highlight: true
weight: 1
tags:
 - glance
categories:
 - wiki
 - why
 - syntax
---

## 数据结构操作示例

- 直接访问嵌套属性
```bash
let user = {
    name: "Alice",
    profile: {
        age: 25,
        skills: ["rust", "javascript", "python"]
    }
}
user.profile.skills@1  # 输出: "javascript"
```

- 链式调用
```bash
1...10 | .map(x -> x * 2) | .filter(x -> x > 10)
# 链式调用，清晰直观
```

- 循环派发管道
```bash
ls -1 |> print "-->" _ "<--"
```

- 结构化管道
```bash
df -H | Into.table() | pprint
Fs.ls -l | where(size > 5K) | select(name,size,modified)
```

- 错误捕获
```bash
6 / 0 ?.               #忽略错误
6 / 0 ?: x -> print x  #处理错误
```

- 数据调试
```bash
let a := (x) -> x + 1
debug a
```

- 映射操作

```bash
# 映射转换
let data = {a: 1, b: 2, c: 3}

# 键值同时转换
let result = Map.map(
    k -> k.to_upper(),     # 键转换函数
    v -> v * 2,         # 值转换函数
    data
)
# 结果: {A: 2, B: 4, C: 6}
```

## 实用示例

### 文件处理：


- 找出大于5KB且在24小时内修改过的文件,以表格显示：
  ```bash
  Fs.ls -l ./src/ | where(size > 5K) | where (Fs.diff('d',modified)>1) | pprint
  ```

- 将当前目录及其子目录下的所有rs源码文件备份：
  1. 方式1: 使用循环派发管道
  ```bash
    ls **/*.rs |> cp _ ./backup/
  ```

  2. 方式2: 使用循环
  ```bash
    for f in **/*.rs {
        cp _ ./backup
    }
  ```

- 去除文件中的注释并保存

  1. 方式1: 函数式
  ```bash
  let content = Fs.read("data.txt")
  let lines = content.lines()
  let filtered = lines | List.filter(line -> !line.starts_with('#'))
  # 写入结果
  filtered.join('\n') | Fs.write("output.txt")
  ```

  2. 方式2: 使用链式调用
  ```bash
  Fs.read("data.txt").lines().filter(x -> !x.starts_with('#')) >> "output.txt"
  ```

  2. 方式2: 使用链式管道
  ```bash
  Fs.read("data.txt") | .lines() | .filter(x -> !x.starts_with('#')) >> "output.txt"
  ```


### 系统管理
- 找出cpu占用率超过2%的用户进程，以表格显示
```bash
 ps u -u1000 | Into.table() | where( Into.float(CPU) > 2.0 ) | pprint
```
- 找出内存使用超过10%的进程
```bash
ps u -u1000 | Into.table() | where( Into.float(MEM) > 10.0 ) | pprint
```

### 网络操作

- 请求json数据并解读为表格
```bash
# HTTP 请求
curl 'https://jsonplaceholder.typicode.com/posts/1/comments' | From.json() | pprint

# 进一步筛选
curl 'https://jsonplaceholder.typicode.com/posts/1/comments' | From.json() | where(id < 3) | select(name,email)| pprint
```

- 请求json数据并转为其他格式保存
```bash
let a = curl 'https://jsonplaceholder.typicode.com/posts/1/comments' | From.json()
a >> data.json                         # json 格式
a | Into.csv() >> data.csv         # csv 格式
a | Into.toml() >> data.toml       # toml 格式

# a 已经是Lumesh的有效表达式
type a     # List
len(a)     # 可以进行其他常规操作
```

### 运维脚本
- 写一个脚本让用户选择可挂载磁盘
```bash
let sel = ( lsblk -rno 'name,type,size,mountpoint,label,fstype' | Into.table([name,'type',size,mountpoint,label,fstype]) \
    | where($type!='disk' && !$mountpoint && $fstype !~: 'member') \
    | Ui.pick "which to mount:") ?: { print 'no device selected' }

if $sel {
    let src = (sel.type == 'part' ? `/dev/${sel.name}` : `/dev/mapper/${sel.name}`)
    let point = (sel.label==None ? sel.name : sel.label)
    let dest = `/run/user/${id -u}/media/${point}`
    if !Fs.exists($dest){ mkdir -p $dest }
    sudo mount -m -o 'defaults,noatime' $src $dest  ?: \
        e -> {notify-send 'Mount Failed' $e.msg; exit 1}
    notify-send 'Mount' `device $src mounted`
}
```

## 常用内置模块

### 文件系统模块 (Fs)

```bash
# 目录操作
Fs.ls("/path")              # 列出目录内容
Fs.mkdir("new_dir")         # 创建目录
Fs.rm("empty_dir")       # 删除空目录

# 文件操作
Fs.read("file.txt")         # 读取文件
Fs.write("file.txt", data)  # 写入文件
Fs.append("Log.txt", entry) # 追加内容

# 路径操作
Fs.exists("/path/to/file")  # 检查路径是否存在
Fs.is_dir("/path")          # 检查是否为目录
Fs.canon("./relative/path") # 获取绝对路径
```

### 系统目录访问

```bash
# 获取系统目录
let dirs = Fs.dirs()
println(dirs.home)      # 用户主目录
println(dirs.config)    # 配置目录
println(dirs.cache)     # 缓存目录
println(dirs.current)   # 当前工作目录
```

### 文件列表详细信息

```bash
# ls 命令选项
Fs.ls -l        # 详细信息
Fs.ls -a        # 显示隐藏文件
Fs.ls -L        # 跟随符号链接
Fs.ls -u        # 显示用户信息
Fs.ls -m        # 显示权限模式
Fs.ls -p        # 显示完整路径
```

## 常用内置函数

### 核心函数

```bash
# 数据操作
len(collection)             # 获取长度
type(value)                 # 获取类型
rev("string")               # 反转字符串/列表
flatten([[1,2],[3,4]])      # 扁平化嵌套结构

# 执行控制
eval(expression)            # 求值表达式
exec_str("let x = 10")      # 执行字符串代码
include("script.lm")        # 包含文件到当前环境
import("module.lm")         # 导入模块到新环境
```

### 数据查询和过滤

```bash
# 数据表格操作
let users = [
    {name: "Alice", age: 25, active: True},
    {name: "Bob", age: 30, active: False},
    {name: "Carol", age: 35, active: True}
]

# 过滤行
let active_users = where(active, users)

# 选择列
let names_ages = select(name, age, users)
```


## 命令执行系统

### 外部命令执行

Lumesh 支持多种命令执行模式：

```bash
# 基本命令执行
ls -la

# 后台执行
command &

# 静默执行
command &-


```

### PTY 支持

对于需要终端交互的程序，Lumesh 提供 PTY 支持：

```bash
# 自动判断并启用PTY模式
ls -l | vi

# 强制PTY 模式（交互式程序）, 一般无需如此
ls -l |^ vi
'' |^ vi 'file.txt'
```

## 脚本执行

### 脚本运行器

Lumesh 提供两种执行模式：

```bash
# 直接执行命令
lumesh -c "print 'Hello World'"

# 执行脚本文件
lumesh script.lm arg1 arg2

# 脚本中访问参数
println(argv[0])  # 第一个参数
```

### 测试示例

```bash
#!/usr/bin/env lumesh

# 测试函数
fn assert(actual, expected, test_name) {
    if actual != expected {
        print "[FAIL]" test_name "| 实际：" actual "| 预期：" expected
    } else {
        print "[PASS]" test_name
    }
}

# 变量赋值测试
let x = 10
assert(str(x), "10", "单变量赋值")

# 延迟赋值测试
x := 2 + 3
assert(eval(x), 5, "延迟赋值求值")
```

## 日志系统

### 日志级别管理

```bash
# 设置日志级别
Log.set_level(Log.level.info)    # 设置为 INFO 级别

# 检查日志级别
if Log.enabled(1) {
    Log.debug("调试已开启")
}
Log.info("调试信息")

# 禁用日志
Log.disable()
```



了解更多：
- [使用案例](/zh-cn/cases/index)
- [lf配置文件对比(lumesh vs bash)](/zh-cn/cases/lf)
- [使用Lumesh编写lf配置文件的语法演示](/zh-cn/cases/case_lf)
- [特性概览 (superiums/lumesh)](/zh-cn/overview)
- [语法手册 (superiums/lumesh)](/zh-cn/syntax)
- [内置函数 (superiums/lumesh)](/zh-cn/libs/index)
