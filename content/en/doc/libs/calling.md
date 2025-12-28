---
title: Function Calling
date: 2025-12-25 19:16:45
weight: 2
---

### Top-Level Functions

All built-in functions support three calling syntaxes:
> `func(arg1, arg2)` or `func arg1 arg2` or `func! arg1 arg2`

When parameters contain complex structures (Lambda, logical operations, etc.), it is recommended to use the first form.

### Module Functions
Can be called in the following three ways:

**Module Name Call**

Call using `module_name.function_name`
> `String.red(args)` or `String.red args`

**Chained Call**

> `'a c b'.split().sort()`

**Pipeline Method Call**

> `'a c b' | .split() | .sort()`
