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


### 安装方式

1. 下载


**方式一：使用安装脚本(推荐)**


- 执行安装命令
```bash
curl -fsSL https://raw.githubusercontent.com/superiums/lumesh/main/docs/install.sh | bash

```


- 或手动下载并运行
 [下载install.sh](https://github.com/superiums/lumesh/releases/latest)
  然后运行 `bash ./install.sh` 即可自动完成安装

> 安装脚本中，除了自动侦测平台，自动安装二进制文件外，还会安装命令参数自动完成数据、helix语法高亮支持

**方式二：下载预编译版本**
从 Release 页面下载对应平台的二进制包并解压到 PATH：
- [release-page 1](https://codeberg.com/santo/lumesh/releases)
- [release-page 2](https://github.com/superiums/lumesh/releases)

> 如需命令参数自动完成功能，需手动解压data到数据目录

**方式三：从源码编译**
```bash
git clone 'https://codeberg.com/santo/lumesh.git'
cd lumesh
cargo build --release

**方式四：从cargo编译**
```bash
cargo install lumesh
```


2. 手动安装

将所下载的文件拷贝到系统路径。例如：
- windows:
`C:\windows\lume`

- linux:
`/usr/bin/lume`

- macos:
`/usr/bin/lume`

3. 增加可执行权限（仅linux和macos)
`sudo chmod +x /usr/bin/lume`

4. 如需自动完成三方命令，需下载`data.tgz`
并将其中的completions数据解压到共享数据目录。

5. 如需helix语法高亮，需解压`data.tgz`中的tree-sitter到helix配置目录并做相应的配置。

6. 其他模块：
需下载`data.tgz`
并将其中的mods数据解压到共享数据目录。

- 如需设置为登录shell（仅linux和macos)
运行lume后执行`use lman; lman::chsh()`函数
- 如需升级
运行lume后执行`use lman; lman::update()`函数


## 更新
在lume中运行`use lman; lman::update()`函数

## 查看帮助
在lume中运行`help`命令查看函数相关帮助；

在lume中运行`help doc`命令查看在线文档
