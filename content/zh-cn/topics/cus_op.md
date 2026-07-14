---
title: 自定义运算符
date: 2025-12-25 19:16:45
---

## 自定义运算符

### 自定义单目运算符
自定义单目运算符必须以`__`开头，定义后，作为后缀运算符使用。

```bash
# 定义
let __! = x -> Math.sum(x)
let __+ = x -> x.to_upper()

# 使用
[5,6,7]  __!              # 18
'lume' __+                # LUME

```

> 为何不定义前置运算符？ 因为和函数调用方式类似，还不如直接定义函数。
> 如果您认为有使用的必要，可以到[项目主页](https://codeberg.org/santo/lumesh/issue)提交请求。

### 自定义双目运算符
自定义单目运算符必须以`_`开头（但不以`__`开头），定义后，作为双目运算符使用。

```bash
# 定义自定义运算符
let ..+ = (a, b) -> a.concat(b)  # 自定义加法

# 使用自定义运算符
[1, 2] ..+ [3, 4]    # [1, 2, 3, 4]
```

双目运算符涉及到运算优先级。
- 以`..+`开头的，优先级同加法。
- 以`..*`开头的，优先级同乘法。
- 以`..`开头的其他运算符，优先级高于幂运算，低于单目运算（如`!`)。


### 应用场景

1. 数据处理管道
```bash
let ..-> = (data,processor) -> processor(data)

let a = [-3,-2,1,5,8, 'fast','pipe']
let clean = x -> list.filter(x, i -> typeof(i)=='Integer')
let filter = x -> list.filter(x, i -> i>0)
let save = x -> {x >> /tmp/saved}

a ..-> clean ..-> filter ..-> save

```

2. 类型检查

```bash
let ..? = (value, expected_type) -> typeof(value) == expected_type ? value : throw(format('expected {expected_type}, found {}', typeof(value)))

5 ..? 'String'

```
