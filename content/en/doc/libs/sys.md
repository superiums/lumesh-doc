---
title: Lumesh Sys Module  
date: 2025-07-13 19:16:45  
highlight: true  
tags:  
 - libs  
 - sys  
categories:  
 - wiki  
 - libs  
---

The Sys module provides system-level operations and environment management functionalities, including environment variable management, variable definition checks, expression quoting, error handling, and system information retrieval. All functions support pipeline operations and provide a unified error handling mechanism.

## Function Overview

| Function Category | Main Functions | Purpose |
|-------------------|----------------|--------|
| **Environment Management** | `env`, `set`, `unset` | Get and manage root environment variables |
| **Variable Checks** | `vars`, `has`, `defined` | Check variable definition status |
| **Expression Operations** | `quote` | Quote an expression |
| **Error Handling** | `error`, `ecodes_rt`, `ecodes_lm` | Error generation and error code queries |
| **System Information** | `info` | Retrieve operating system information |
| **Output Control** | `print_tty`, `discard` | TTY output and data discard |

## Environment Management Functions

These functions are used to manage system environment variables:

**`env`** - Get the root environment as a mapping  
- **Parameters**: None  
- **Returns**: `Map` - All variables in the root environment  

**`set <var> <val>`** - Define a variable in the root environment  
- **Parameters**: `var` (required): `String` - Variable name, `val` (required): `Any` - Variable value  
- **Returns**: `None`  

**`unset <var>`** - Remove a variable from the root environment  
- **Parameters**: `var` (required): `String` - Name of the variable to remove  
- **Returns**: `None`  

## Variable Check Functions

These functions are used to check the definition status of variables:

**`vars`** - Get the defined variables in the current environment  
- **Parameters**: None  
- **Returns**: `Map` - Mapping of variables in the current environment  

**`has <var>`** - Check if a variable is defined in the current environment  
- **Parameters**: `var` (required): `String` - Name of the variable to check  
- **Returns**: `Boolean` - Whether the variable exists  

**`defined <var>`** - Check if a variable is defined in the current environment tree  
- **Parameters**: `var` (required): `String` - Name of the variable to check  
- **Returns**: `Boolean` - Whether the variable is defined in the environment tree  

## Expression Operation Functions

**`quote <expr>`** - Quote an expression  
- **Parameters**: `expr` (required): `Expression` - Expression to quote  
- **Returns**: `Quote` - Quoted expression  

## Error Handling Functions

These functions are used for error handling and debugging:

**`error <msg>`** - Return a runtime error  
- **Parameters**: `msg` (required): `String` - Error message  
- **Returns**: Throws `LmError::CustomError`  

**`ecodes_rt`** - Display runtime error codes  
- **Parameters**: None  
- **Returns**: `Expression` - List of runtime error codes  

**`ecodes_lm`** - Display LmError error codes  
- **Parameters**: None  
- **Returns**: `Expression` - List of LmError error codes  

## System Information Functions

**`info`** - Get operating system information  
- **Parameters**: None  
- **Returns**: `String` - Operating system information string  

## Output Control Functions

**`print_tty <arg>`** - Print control sequences to TTY  
- **Parameters**: `arg` (required): `String` - Content to print  
- **Returns**: `None`  

**`discard <arg>`** - Send data to /dev/null  
- **Parameters**: `arg` (required): `Any` - Data to discard  
- **Returns**: `None`  

## Notes

The Sys module is one of the core components of the Lumesh built-in module system, registered as "Sys" in the module registry. The functions in this module are primarily used for system-level operations, complementing other application-level modules such as String and Parse.

The `set` and `unset` functions in the module are also available as global built-in functions and can be used directly without the module prefix.
