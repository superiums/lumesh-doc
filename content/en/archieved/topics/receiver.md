---
title: Placeholder
date: 2025-12-25 19:16:45
---
    
## Placeholder `_`

> A unique placeholder in Lumesh, with the following uses:

- As a blank placeholder after a single-word command

```bash
ls _        # Indicates this is a command

let ls = 5
ls          # This will directly print the variable     
```

*   As an open interval in ranges or slices

```bash
_..10      # Includes all integers less than 10

1.._       # Includes all integers greater than or equal to 1

a[_..10]   # Includes all elements in a with indices less than 10
```

*   On the right side of a pipeline, indicating the target position of the pipeline data

```bash
2 | print 1 _ 3     # The pipeline data is placed at the second parameter position
    
```
