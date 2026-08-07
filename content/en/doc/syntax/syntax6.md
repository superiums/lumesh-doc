---
title: "Syntax: Parameters and Environment Variables"
date: 2025-06-11 19:16:45
highlight: true
weight: 80
tags:
 - syntax
categories:
 - wiki
 - syntax
---

## **Command Line Parameters**:
Parameters are accessed via `argv` list

- Script parameters
```bash
lumesh script.lm Alice tom
# in script.lm
print argv  # outputs "[Alice, tom]"
print argv[0]  # outputs "Alice"
```

- Passing parameters from command line
```bash
lume -c 'print argv' -- a b   # or
lume -c 'print argv' a b
# outputs
[a, b]
```

## **Environment Variables**

### System Environment Variables
- `sys.env()` lists system environment variables
```bash
set var value    # set system environment variable
unset var        # unset system environment variable
```
Typical system variables:
```bash
PATH             # system environment variable
HOME             # system environment variable
```

### Current Environment Variables
- `sys.vars()` lists current environment variables
```bash
let var value    # set current environment variable
del var          # delete current environment variable
```

Current environment variables defined by Lumesh:
   ```bash
   env              # list all current environment variables
   IS_LOGIN         # whether login shell
   IS_INTERACTIVE   # whether interactive mode
   IS_STRICT        # whether strict mode
   ```
### IFS
This is a special environment variable used for internal field separation.

- IFS state behavior control table for three modes

|Mask| Syntax Type | Mask function bit disabled | Enabled but IFS value not set | Enabled and IFS value set |
|----|------------|---------------------------|-------------------------------|---------------------------|
| 2 | **Command string parameters** | Pass entire string as single parameter | Use `newline` as default separator | Split using IFS value as separator |
| 4 | **for loop/dispatch pipe splitting** | Try splitting by `line, space, semicolon, comma` sequentially | Try splitting by `line, space, semicolon, comma` sequentially | Split string using IFS value |
| 8 | **string.split function** | Split using `whitespace` (split_whitespace) | Use `space (" ")` as default separator | Split using IFS value as separator |
| 16| **from.csv/into.csv functions** | Use `comma (",")` as CSV separator | Use `comma (",")` as default separator | Use IFS value's first character as CSV separator* |
| 32| **ui.pick function**  | Split options using `newline ("\n")` | Use `newline ("\n")` as default separator | Split options list using IFS value |

_*Note: CSV functions have special handling, if IFS is set to "\n", still use comma as separator._*

- Control logic flow

1. **First level check**: Check if `LUME_IFS_MODE` bit mask enables corresponding function via `LUME_IFS_MODE` check
2. **Second level check**: If function is enabled, check if `IFS` variable is set to valid string value
3. **Third level execution**: Execute corresponding splitting logic based on check results

This three-layer control design provides maximum flexibility and backward compatibility. System initialization ensures IFS variable exists.

- Configuration description

- **LUME_IFS_MODE**: Bit mask, controlling which syntaxes use IFS
- **IFS**: Actual separator string
- **Default**: LUME_IFS_MODE defaults to 2, only affects command parameter splitting

- Usage examples

To enable all IFS functions, you can set:
  ```bash
  let LUME_IFS_MODE = 62  # 2+4+8+16+32 = 62, enables all functions
  ```

To enable only specific function combinations, add the corresponding bit values. For example:
- Only enable command parameters and string splitting: `LUME_IFS_MODE = 10` (2+8)
- Enable for loop and CSV parsing: `LUME_IFS_MODE = 20` (4+16)


> IFS (Internal Field Separator) is a shell concept, implemented in Lumesh as a configurable string splitting mechanism. Through the bit mask system, users can precisely control which syntax contexts use IFS splitting and which use default behavior. This design provides backward compatibility while allowing users to customize string processing behavior as needed.
