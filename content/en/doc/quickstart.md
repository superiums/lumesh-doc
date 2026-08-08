---
title: Quick Start
date: 2026-03-16 16:16:00
highlight: true
weight: 1
tags:
  - install
categories:
  - wiki
  - install
---

# Lumesh User Guide - Quick Start

### 1. Installation

One-click installation:
```bash
curl -fsSL https://bun.sh/install | bash
```

### 2. Start

After installation, start the interactive shell:
```bash
lume          # Full interactive
```

### 3. Interactive Commands

Like bash, you can try typing daily commands:
```bash
ls -l
thunar &
# Looks like bash, but with some extra features, like syntax highlighting

jobs
fs.ls -lh | where(size>5K)
# Seems a bit different from bash, supports structured pipelines

ls --       # Press <Tab>
# Auto-completion is also available

3+18/6
0b100 + 0b101    # Binary arithmetic is easy too
# Math operations are simpler than in bash

0...10 |> _ + 100
# Loop dispatch, so convenient

0..8 | ui.pick()
# What? Can I also select?

let a = 0..8
if a ~: 5 {
    print 'include'
}
# Multi-line editing is supported too

list.from(a).sum()
'lume'.upper().green()
# Can also chain calls? Even more convenient than pipes!

5/0 ?: 0
# Error handling is so natural!

help
help libs
help string
# Rich built-in libraries

help doc
# Online documentation is right here
```

You've found your way in, and there are more surprises waiting for you, like `/ commands`, like `programmable hotkeys`, like `AI prompts and generation`...
