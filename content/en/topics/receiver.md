---
title: Placeholders
date: 2025-12-25 19:16:45
---

## Placeholders `_`

> Placeholders unique to Lumesh, with the following uses:

- After a single-word command, as a blank placeholder

```bash
ls _        # Indicates this is a command

let ls = 5
ls          # This will directly print the variable     
```

- As an open interval in intervals or slices

```bash

_..10      # Contains all integers less than 10

1.._       # Contains all integers greater than or equal to 1

a[_..10]   # Contains all elements in a with indices less than 10

```

- On the right side of a pipeline, indicates the target position of pipeline data

```bash
2 | print 1 _ 3     # Puts pipeline data in the second argument position

```