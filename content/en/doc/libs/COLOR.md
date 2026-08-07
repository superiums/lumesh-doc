---
title: Built-in Constant COLOR
date: 2025-12-25 19:16:45
weight: 41
tags:
  - const
categories:
  - wiki
  - const
---

## Basic Colors

| Foreground | Foreground Bright | Background | Background Bright |
|------------|-------------------|------------|-------------------|
| BLACK | LIGHT_BLACK | BG_BLACK | BG_LIGHT_BLACK |
| RED | LIGHT_RED | BG_RED | BG_LIGHT_RED |
| GREEN | LIGHT_GREEN | BG_GREEN | BG_LIGHT_GREEN |
| YELLOW | LIGHT_YELLOW | BG_YELLOW | BG_LIGHT_YELLOW |
| BLUE | LIGHT_BLUE | BG_BLUE | BG_LIGHT_BLUE |
| MAGENTA | LIGHT_MAGENTA | BG_MAGENTA | BG_LIGHT_MAGENTA |
| CYAN | LIGHT_CYAN | BG_CYAN | BG_LIGHT_CYAN |
| GRAY | LIGHT_GRAY | BG_GRAY | BG_LIGHT_GRAY |

Usage:
```bash
COLOR.RED + 'lume'
# Equivalent to
string.red('lume')
```

## 256 Colors

| Foreground | Background |
|------------|------------|
| FG_1 | BG_1 |
| FG_2 | BG_2 |
| ... | ... |
| FG_256 | BG_256 |

Usage:
```bash
COLOR.FG_50 + 'lume'
# Equivalent to
string.clr('lume', 50)
```

## True Colors

- By Name

| Foreground | Background |
|------------|------------|
| aliceblue | BG_aliceblue |
| BG_antiquewhite | BG_antiquewhite |
| ... | ... |
| yellowgreen | BG_yellowgreen |

Available colors can be viewed using the following methods:
```bash
string.colors()
string.colors(false)
```

Usage:
```bash
COLOR.green + 'lume'
# Equivalent to
string.color('lume', 'green')
```

- By Code

| Foreground | Background |
|------------|------------|
| FGX_000000 | BGX_000000 |
| FGX_000001 | BGX_000001 |
| ... | ... |
| FGX_ffffff | BGX_ffffff |

Usage:
```bash
COLOR.FGX_aaff22 + 'lume'
# Equivalent to
string.color('lume', '#aaff22')
```

## Reset:

RESET

Usage:
```bash
COLOR.RED + 'lume' + COLOR.RESET + ' normal'
# Equivalent to
string.red('lume') + ' normal'
```