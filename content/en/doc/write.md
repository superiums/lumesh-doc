---
title: Script Writing
date: 2025-06-11 19:16:45
highlight: true
weight: 70
tags:
 - syntax
categories:
 - wiki
 - syntax
---

## Interpreter Declaration

The first line of a script file usually contains the interpreter declaration.

The recommended shebang is `#!/usr/bin/en lumesh`

> There are two binary files downloaded: `lume` or `lume-se`. You can link one of them to `lumesh` according to your preference
```bash
ln -sf /usr/bin/lume /usr/bin/lumesh     # or
ln -sf /usr/bin/lume-se /usr/bin/lumesh
```

> Scripts without a shebang line can only be run via `lume my.lm`
> Scripts with a shebang line can be run directly via `my.lm`

## Extension

The recommended extension is `.lm`

## Example
```bash
#!/usr/bin/en lumesh

fn add(msg, *salaries){
    println msg.green()
    println salaries.sum()
}

add('wang fang', 4500, 5000, 6100)

if argv.len() {
    println 'Your args:' argv
}
```