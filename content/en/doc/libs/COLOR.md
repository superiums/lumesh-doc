---
title: const of COLOR
date: 2025-12-25 19:16:45
weight: 41
tags:
 - const
categories:
 - wiki
 - const
---

## 8bit color

| foreground | foreground light | background | background light |
|--------|----------|--------|----------|
| BLACK | LIGHT_BLACK | BG_BLACK | BG_LIGHT_BLACK |
| RED | LIGHT_RED | BG_RED | BG_LIGHT_RED |
| GREEN | LIGHT_GREEN | BG_GREEN | BG_LIGHT_GREEN |
| YELLOW | LIGHT_YELLOW | BG_YELLOW | BG_LIGHT_YELLOW |
| BLUE | LIGHT_BLUE | BG_BLUE | BG_LIGHT_BLUE |
| MAGENTA | LIGHT_MAGENTA | BG_MAGENTA | BG_LIGHT_MAGENTA |
| CYAN | LIGHT_CYAN | BG_CYAN | BG_LIGHT_CYAN |
| GRAY | LIGHT_GRAY | BG_GRAY | BG_LIGHT_GRAY |

usage：
```bash
COLOR.RED + 'lume'
# same as
string.red('lume')
```

## 256bit color

| foreground | background |
|--------|--------|
| FG_1 | BG_1 |
| FG_2 | BG_2 |
| ... | ... |
| FG_256 | BG_256 |

usage：
```bash
COLOR.FG_50 + 'lume'
# same as
string.clr('lume',50)
```

## true color
- by name

| foreground | background |
|--------|--------|
| aliceblue | BG_aliceblue |
| BG_antiquewhite | BG_antiquewhite |
| ... | ... |
| yellowgreen | BG_yellowgreen |

to list the avaluable colors, use：
```bash
string.colors()
string.colors(false)
```

usage：
```bash
COLOR.green + 'lume'
# same as
string.color('lume','green')
```

- 按代码

| foreground | background |
|--------|--------|
| FGX_000000 | BGX_000000 |
| FGX_000001 | BGX_000001 |
| ... | ... |
| FGX_ffffff | BGX_ffffff |

usage：
```bash
COLOR.FGX_aaff22 + 'lume'
# same as
string.color('lume','#aaff22')
```

## reset：
RESET

usage：
```bash
COLOR.RED + 'lume' + COLOR.RESET + ' normal'
# same as
string.red('lume') + ' normal'
```
