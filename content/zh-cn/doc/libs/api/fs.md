---
title: 内置库 fs
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: fs

- `abs <path>`
	绝对路径

- `append <content> <file>`
	追加到文件，文件不存在则创建

- `base_name <path>`
	带扩展名的文件名

- `canon <path>`
	规范路径，解析符号链接

- `chmod <path> <mode:octal>`
	设置 Unix 权限模式

- `chown <path> <uid> <gid>`
	设置 Unix 所有者，-1 表示保留

- `cp <source> <destination>`
	复制路径，目录递归复制

- `dir_name <path>`
	最后一个 '/' 之前的目录部分

- `exists <path>`
	路径是否存在？

- `extension <path>`
	文件扩展名

- `glob <pattern>`
	按模式匹配文件

- `head <file> [n=10]`
	前 n 行

- `is_dir <path>`
	是否为目录？

- `is_file <path>`
	是否为文件？

- `join <segment>...`
	连接路径段

- `ls [-l|a|h|t|L|c|u|m|p|?] [path]`
	列出目录内容

- `mkdir <path>`
	创建目录，包括父目录

- `mv <source> <destination>`
	移动路径

- `parent <path>`
	父目录路径

- `read <file>`
	读取文件，文本或字节

- `read_link <link_path>`
	读取符号链接目标

- `rm <path>`
	删除路径，目录递归删除

- `rmdir <path>`
	删除空目录

- `size <path>`
	获取文件大小

- `stem <path>`
	不带扩展名的文件名

- `symlink <source> <link_path>`
	创建符号链接

- `tail <file> [n=10]`
	最后 n 行

- `touch <path>`
	创建空文件，或更新修改时间（如果已存在）

- `tree [depth=3] [path]`
	目录树，以嵌套映射形式

- `write [content] <file>`
	创建/覆盖文件
