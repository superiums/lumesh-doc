---
title: 内置常数COLOR
date: 2025-12-25 19:16:45
weight: 41
tags:
 - const
categories:
 - wiki
 - const
---

## 基本色

| 前景色 | 前景亮色 | 背景色 | 背景亮色 |
|--------|----------|--------|----------|
| BLACK | LIGHT_BLACK | BG_BLACK | BG_LIGHT_BLACK |
| RED | LIGHT_RED | BG_RED | BG_LIGHT_RED |
| GREEN | LIGHT_GREEN | BG_GREEN | BG_LIGHT_GREEN |
| YELLOW | LIGHT_YELLOW | BG_YELLOW | BG_LIGHT_YELLOW |
| BLUE | LIGHT_BLUE | BG_BLUE | BG_LIGHT_BLUE |
| MAGENTA | LIGHT_MAGENTA | BG_MAGENTA | BG_LIGHT_MAGENTA |
| CYAN | LIGHT_CYAN | BG_CYAN | BG_LIGHT_CYAN |
| GRAY | LIGHT_GRAY | BG_GRAY | BG_LIGHT_GRAY |

用法：
```bash
COLOR.RED + 'lume'
# 效果等同于
string.red('lume')
```

## 256色

| 前景色 | 背景色 |
|--------|--------|
| FG_1 | BG_1 |
| FG_2 | BG_2 |
| ... | ... |
| FG_256 | BG_256 |

用法：
```bash
COLOR.FG_50 + 'lume'
# 效果等同于
string.clr('lume',50)
```

## 真彩色
- 按名称

| 前景色 | 背景色 |
|--------|--------|
| aliceblue | BG_aliceblue |
| BG_antiquewhite | BG_antiquewhite |
| ... | ... |
| yellowgreen | BG_yellowgreen |

具体可用颜色，可用如下方式查看：
```bash
string.colors()
string.colors(false)
```

用法：
```bash
COLOR.green + 'lume'
# 效果等同于
string.color('lume','green')
```

- 按代码

| 前景色 | 背景色 |
|--------|--------|
| FGX_000000 | BGX_000000 |
| FGX_000001 | BGX_000001 |
| ... | ... |
| FGX_ffffff | BGX_ffffff |

用法：
```bash
COLOR.FGX_aaff22 + 'lume'
# 效果等同于
string.color('lume','#aaff22')
```

## 恢复：
RESET

用法：
```bash
COLOR.RED + 'lume' + COLOR.RESET + ' normal'
# 效果等同于
string.red('lume') + ' normal'
```
