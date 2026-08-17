---
title: How to Install
date: 2025-12-11 19:16:45
highlight: true
weight: 11
tags:
  - install
categories:
  - wiki
  - install
---


### Installation Methods

1. Download

**Method 1: Use Installation Script (Recommended)**

- Execute installation command
```bash
curl -fsSL https://www.lumesh.cc.cd/install.sh | bash

```

- Or manually download and run
  [Download install.sh](https://github.com/superiums/lumesh/releases/latest)
  Then run `bash ./install.sh` to automatically complete installation

> Installation script, in addition to automatic platform detection and automatic binary installation, will also install command-line auto-completion data, helix syntax highlighting support

**Method 2: Download Pre-compiled Version**
Download binary packages for corresponding platforms from Release page and extract to PATH:
- [release-page 1](https://codeberg.com/santo/lumesh/releases)
- [release-page 2](https://github.com/superiums/lumesh/releases)

> If command-line auto-completion is needed, manually extract data to data directory

**Method 3: Compile from Source**
```bash
git clone 'https://codeberg.com/santo/lumesh.git'
cd lumesh
cargo build --release

**Method 4: Compile from Cargo**
```bash
cargo install lumesh
```

2. Manual Installation

Copy downloaded files to system path. For example:
- windows:
`C:\windows\lume`

- linux:
`/usr/bin/lume`

- macos:
`/usr/bin/lume`

3. Add executable permission (only for Linux and macOS)
`sudo chmod +x /usr/bin/lume`

4. If auto-completion for third-party commands is needed, download `data.tgz`
and extract completions data to shared data directory.

5. If helix syntax highlighting is needed, extract tree-sitter from `data.tgz` to helix configuration directory and make corresponding configuration.

6. Other modules:
Download `data.tgz`
and extract mods data to shared data directory.

- If set as login shell (only for Linux and macOS)
Run lume and execute `use lman; lman::chsh()` function
- If upgrade needed
Run lume and execute `use lman; lman::update()` function


## Update
Run `use lman; lman::update()` function in lume

## View Help
Run `help` command in lume to view function-related help;

Run `help doc` command in lume to view online documentation
