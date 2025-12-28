---
title: Lumesh Fs 模块
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - fs
 - filesystem
categories:
 - wiki
 - libs
---

Fs 模块提供了全面的文件系统操作功能，包括文件读写、目录管理、路径操作和文件属性查询等。所有函数都支持家目录展开（`~`）和相对路径处理，提供统一的错误处理机制。

## 功能概览

| 功能类别 | 主要函数 | 用途 |
|---------|---------|------|
| **系统目录** | `dirs` | 获取系统标准目录 |
| **目录操作** | `ls`, `glob`, `tree`, `mkdir`, `rmdir` | 目录浏览和管理 |
| **文件操作** | `mv`, `cp`, `rm` | 文件移动、复制和删除 |
| **路径操作** | `canon`, `join`, `base_name` | 路径处理和解析 |
| **状态检查** | `exists`, `is_dir`, `is_file` | 文件系统状态查询 |
| **文件读写** | `read`, `write`, `append`, `head`, `tail` | 文件内容操作 |

## 系统目录函数

**`dirs`** - 获取系统标准目录
- **参数**：无
- **返回**：`Map` - 包含系统目录路径的映射
- **包含目录**：
  - `home` - 用户家目录
  - `config` - 配置目录
  - `cache` - 缓存目录
  - `data` - 数据目录
  - `pic` - 图片目录
  - `desk` - 桌面目录
  - `docs` - 文档目录
  - `down` - 下载目录
  - `current` - 当前工作目录



## 目录操作函数

**`ls [path]`** - 列出目录内容
- **参数**：
  - `path` (可选): `String` - 目录路径，默认为当前目录
- **返回**：`List[Map]` - 文件信息列表
- **支持选项**：
  - `-l` - 详细信息
  - `-a` - 显示隐藏文件
  - `-L` - 跟随符号链接
  - `-U` - Unix 时间戳
  - `-k` - 大小以 KB 显示
  - `-u` - 显示用户信息
  - `-m` - 显示权限模式
  - `-p` - 显示完整路径



**`glob <pattern>`** - 模式匹配文件
- **参数**：
  - `pattern` (必需): `String` - 文件匹配模式
- **返回**：`List[String]` - 匹配的文件路径列表

**`tree [path]`** - 获取目录树结构
- **参数**：
  - `path` (可选): `String` - 目录路径，默认为当前目录
- **返回**：`Map` - 嵌套的目录树结构

**`mkdir <path>`** - 创建目录
- **参数**：
  - `path` (必需): `String` - 要创建的目录路径
- **功能**：递归创建目录（类似 `mkdir -p`）



**`rmdir <path>`** - 删除空目录
- **参数**：
  - `path` (必需): `String` - 要删除的目录路径
- **注意**：只能删除空目录



## 文件操作函数

**`mv <source> <destination>`** - 移动文件或目录
- **参数**：
  - `source` (必需): `String` - 源路径
  - `destination` (必需): `String` - 目标路径
- **功能**：支持重命名和移动操作
- **特殊处理**：如果目标路径以 `/` 结尾，会将源文件移动到该目录下



**`cp <source> <destination>`** - 复制文件或目录
- **参数**：
  - `source` (必需): `String` - 源路径
  - `destination` (必需): `String` - 目标路径
- **功能**：递归复制目录和文件



**`rm <path>`** - 删除文件或目录
- **参数**：
  - `path` (必需): `String` - 要删除的路径
- **功能**：自动判断文件或目录并执行相应删除操作



## 路径操作函数

**`canon <path>`** - 规范化路径
- **参数**：
  - `path` (必需): `String` - 要规范化的路径
- **返回**：`String` - 规范化后的绝对路径

**`join <path>...`** - 连接路径
- **参数**：
  - `path` (可变): `String...` - 要连接的路径组件
- **返回**：`String` - 连接后的路径

**`dir_name <path>`** - 提取文件路径
- **参数**：
  - `path` (必需): `String` - 文件路径
- **返回**：`String` - 文件路径

**`base_name [split_ext?] <path>`** - 提取文件名
- **参数**：
  - `split_ext` (可选): `Boolean` - 是否分离扩展名
  - `path` (必需): `String` - 文件路径
- **返回**：`String` - 文件名
或 `List` - `[文件基础名,文件扩展名]`

## 状态检查函数

**`exists <path>`** - 检查路径是否存在
- **参数**：
  - `path` (必需): `String` - 要检查的路径
- **返回**：`Boolean` - 路径存在时返回 true

**`is_dir <path>`** - 检查是否为目录
- **参数**：
  - `path` (必需): `String` - 要检查的路径
- **返回**：`Boolean` - 是目录时返回 true



**`is_file <path>`** - 检查是否为文件
- **参数**：
  - `path` (必需): `String` - 要检查的路径
- **返回**：`Boolean` - 是文件时返回 true



## 文件读写函数

**`read <file>`** - 读取文件内容
- **参数**：
  - `file` (必需): `String` - 文件路径
- **返回**：`String|Bytes` - 文件内容（自动检测文本或二进制）
- **功能**：优先尝试读取为文本，失败时读取为字节数组



**`write <file> <content>`** - 写入文件
- **参数**：
  - `file` (必需): `String` - 文件路径
  - `content` (必需): `String|Bytes` - 要写入的内容
- **功能**：支持字符串和字节数组写入



**`append <file> <content>`** - 追加到文件
- **参数**：
  - `file` (必需): `String` - 文件路径
  - `content` (必需): `String|Bytes` - 要追加的内容
- **功能**：在文件末尾追加内容



**`head [n] <file>`** - 读取文件前 N 行
- **参数**：
  - `n` (可选): `Integer` - 行数，默认为 10
  - `file` (必需): `String` - 文件路径
- **返回**：`List[String]` - 文件前 N 行

**`tail [n] <file>`** - 读取文件后 N 行
- **参数**：
  - `n` (可选): `Integer` - 行数，默认为 10
  - `file` (必需): `String` - 文件路径
- **返回**：`List[String]` - 文件后 N 行

## 使用示例

### 基础文件操作
```bash
# 读取文件
Fs.read("config.txt")

# 写入文件
Fs.write("output.txt", "Hello, World!")

# 追加内容
Fs.append("log.txt", "New log entry\n")
```

### 目录操作
```bash
# 列出当前目录
Fs.ls()

# 详细列出指定目录
Fs.ls("-l", "/home/user")

# 创建目录
Fs.mkdir("~/projects/new-project")

# 获取系统目录
Fs.dirs()
```

### 文件管理
```bash
# 复制文件
Fs.cp("source.txt", "backup.txt")

# 移动文件
Fs.mv("old-name.txt", "new-name.txt")

# 删除文件
Fs.rm("temp-file.txt")
```

### 路径操作
```bash
# 连接路径
Fs.join("~", "Documents", "file.txt")

# 获取文件名
Fs.base_name("/path/to/file.txt")  # 返回 "file.txt"

# 规范化路径
Fs.canon("../relative/path")
```

## Notes

Fs 模块提供了完整的文件系统操作能力，所有路径操作都支持家目录展开（`~`）和相对路径处理。文件读写函数能够自动处理文本和二进制内容，提供了灵活的文件操作接口。参数类型说明中，`<>` 表示必需参数，`[]` 表示可选参数，`...` 表示可变参数。
