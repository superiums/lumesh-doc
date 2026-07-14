---
title: 语法高亮
date: 2025-12-25 19:16:45
---

## lumesh 语法高亮
> lumesh 作为一门新的语言，如何获得编辑器的语法高亮支持呢？

作者已经创建了一个通用编辑器的语法高亮项目：

[tree-sitter-lumesh](https://github.com/superiums/tree-sitter-lumesh)

支持tree-sitter的编辑器将能够快速获得语法高亮支持。

### Helix Editor

- 方式一：使用install.sh安装lume时，会自动为helix添加语法高亮支持

- 方式二：使用预编译文件

  1. 在helix的配置文件`languages.toml`中添加：

  ```toml file-name=~/.config/helix/languages.toml

  # 其他位置
  [[language]]
  name = "lumesh"
  scope = "source.lumesh"
  injection-regex = "lumesh"
  file-types = ["lm", "lumesh"]
  roots = []
  comment-token = "#"
  indent = { tab-width = 2, unit = "  " }

  ```
  
  2. 链接语法高亮文件
  
  + 个人安装的情况下
  ```bash
  ln -s ~/.local/share/lumesh/tree-sitter-lumesh/grammars/lumesh.so ~/.config/helix/runtime/grammars
  ln -s ~/.local/share/lumesh/tree-sitter-lumesh/queries ~/.config/helix/runtime/
  ```

  + 系统安装的情况下
  ```bash
  ln -s /usr/local/share/lumesh/tree-sitter-lumesh/grammars/lumesh.so ~/.config/helix/runtime/grammars
  ln -s /usr/local/share/lumesh/tree-sitter-lumesh/queries ~/.config/helix/runtime/
  ```


- 方式三：从源码编译：

  1. 在helix的配置文件`languages.toml`中添加：

  ```toml file-name=~/.config/helix/languages.toml
  # 顶部，避免下载其他语言的源码
  use-grammars = { only = [ "lumesh" ] }  


  # 其他位置
  [[language]]
  name = "lumesh"
  scope = "source.lumesh"
  injection-regex = "lumesh"
  file-types = ["lm", "lumesh"]
  roots = []
  comment-token = "#"
  indent = { tab-width = 2, unit = "  " }

  [[grammar]]  
  name = "lumesh"  
  source = { git = "https://github.com/superiums/tree-sitter-lumesh", rev = "v0.13.0" }

  ```

  2. 运行命令：
  ```bash
  helix --grammar fetch
  helix --grammar build
  ```

  3. 运行命令：
  ```bash
  cp ~/.config/helix/runtime/grammars/sources/lumesh/queries/* /home/tix/.config/helix/runtime/queries
  ```

使用：

扩展名为`.lm`的文件或shebang行带有`lumesh`的文件将获得高亮支持。

## lumelf 语法高亮
> 使用lumesh为lf文件管理器书写配置文件

作者已经创建了一个通用编辑器的语法高亮项目：

[tree-sitter-lumelf](https://github.com/superiums/tree-sitter-lumelf)

支持tree-sitter的编辑器将能够快速获得语法高亮支持。

### Helix Editor

- 方式一：使用install.sh安装lume时，会自动为helix添加语法高亮支持


- 方式二：使用预编译文件 或 源码，与上节类似

  但配置略有不同，在helix的配置文件`languages.toml`中添加：

  ```toml file-name=~/.config/helix/languages.toml

[[language]]
name = "lumelf"
scope = "source.lumelf"
injection-regex = "lumelf"
shebangs = ["lumelf"]
file-types = ["lmf"]
roots = []
comment-token = "#"
indent = { tab-width = 2, unit = "  " }

[[grammar]]  
name = "lumelf"  
source = { git = "https://github.com/superiums/tree-sitter-lumelf", rev = "v0.13.1" }

  ```

其他步骤同上节

使用：

在lfrc的首行添加如下shebang行：
`#! lumelf`
或让文件以`.lmf`扩展名命名。
