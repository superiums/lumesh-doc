---
title: 如何安装
date: 2025-12-11 19:16:45
highlight: true
weight: 1
tags:
 - install
categories:
 - wiki
 - install
---

1. 下载

访问[https://codeberg.org/santo/lumesh/releases](https://codeberg.org/santo/lumesh/releases)下载您系统对应的文件。

macos 请到[https://github.com/superiums/lumesh/releases](https://github.com/superiums/lumesh/releases)下载。

2. 安装

将所下载的文件拷贝到系统路径。例如：
- windows:
`C:\windows\lume`

- linux:
`/usr/bin/lume`

- macos:
`/usr/bin/lume`

3. 增加可执行权限（仅linux和macos)
`sudo chmod +x /usr/bin/lume`

4. 如需设置为登录shell（仅linux和macos)
运行lume后执行`set_as_login_shell()`函数

## 更新
在lume中运行`update()`函数

## 查看帮助
在lume中运行`help`命令查看函数相关帮助；

在lume中运行`doc`命令查看在线文档
