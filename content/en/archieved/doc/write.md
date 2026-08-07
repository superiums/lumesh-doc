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
The first line of a script file typically contains the interpreter declaration. The recommended shebang is `#!/usr/bin/env lumesh`.

> There are two binary files available for download: `lume` or `lume-se`. You can link one of them to `lumesh` based on your preference.
```bash
ln -sf /usr/bin/lume /usr/bin/lumesh     # or
ln -sf /usr/bin/lume-se /usr/bin/lumesh
```

> Scripts without a shebang line can only be run using `lume my.lm`. Scripts with a shebang line can be executed directly with `my.lm`.

## File Extension
The recommended file extension is `.lm`.

## Example
```bash
#!/usr/bin/env lumesh

fn add(msg, *salaries){
    println msg.green()
    println salaries.sum()
}

add('wang fang', 4500, 5000, 6100)

if argv.len() {
    println 'Your args:' argv
}
```
