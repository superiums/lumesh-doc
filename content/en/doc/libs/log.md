---
title: Lumesh Log Module  
date: 2025-07-13 19:16:45  
highlight: true  
tags:  
 - libs  
 - log  
categories:  
 - wiki  
 - libs  
---

The Log module provides structured logging capabilities, supporting various log levels, colored output, and thread-safe level management. All functions support pipeline operations and provide a unified error handling mechanism.

## Function Overview

| Function Category | Main Functions | Purpose |
|-------------------|----------------|--------|
| **Log Level Management** | `set_level`, `get_level`, `disable`, `enabled` | Control log output levels |
| **Logging** | `info`, `warn`, `debug`, `error`, `trace` | Log output at different levels |
| **Raw Output** | `echo` | Direct output without formatting |
| **Level Constants** | `level.none`, `level.error`, `level.warn`, `level.info`, `level.debug`, `level.trace` | Log level constants |

## Log Level Constants

The Log module provides a complete set of log level constants:

**`level`** - Log level constant mapping  
- **Includes**: `none` (0), `error` (1), `warn` (2), `info` (3), `debug` (4), `trace` (5)  
- **Example**:
  ```bash
  Log.set_level Log.level.debug
  # Set log level to DEBUG

  Log.set_level 3
  # Directly set to INFO level using a number
  ```

## Log Level Management Functions

These functions are used to control the log output level:

**`set_level <level>`** - Set the log level  
- **Parameters**: `level` (required): `Integer` - Log level (0-5)  
- **Returns**: `None`  
- **Example**:
  ```bash
  Log.set_level Log.level.warn
  # Only show WARN and above level logs

  Log.set_level 1
  # Only show ERROR level logs
  ```

**`get_level`** - Get the current log level  
- **Parameters**: None  
- **Returns**: `Integer` - Current log level  
- **Example**:
  ```bash
  Log.get_level
  # Returns: 3 (default INFO level)

  let current_level = Log.get_level
  ```

**`disable`** - Disable all log output  
- **Parameters**: None  
- **Returns**: `None`  
- **Example**:
  ```bash
  Log.disable
  # Disable all log output

  Log.info "This message will not be displayed"
  ```

**`enabled <level>`** - Check if the specified level is enabled  
- **Parameters**: `level` (required): `Integer` - Log level to check  
- **Returns**: `Boolean` - Whether the level is enabled  
- **Example**:
  ```bash
  Log.enabled Log.level.debug
  # Returns: true/false

  if (Log.enabled 4) { Log.debug "Debug information" }
  ```

## Logging Functions

These functions are used to output log messages at different levels:

**`info <message>...`** - Log an info level message  
- **Parameters**: `message` (required): `Any` - Message to log (supports multiple parameters)  
- **Returns**: `None`  
- **Color**: Green `[INFO]` prefix  
- **Example**:
  ```bash
  Log.info "Application started successfully"
  # Output: [INFO] Application started successfully

  Log.info "User" $username "logged in successfully"
  # Output: [INFO] User alice logged in successfully
  ```

**`warn <message>...`** - Log a warning level message  
- **Parameters**: `message` (required): `Any` - Message to log (supports multiple parameters)  
- **Returns**: `None`  
- **Color**: Yellow `[WARN]` prefix  
- **Example**:
  ```bash
  Log.warn "Disk space is low"
  # Output: [WARN] Disk space is low

  Log.warn "Configuration file" $config_file "does not exist, using default configuration"
  ```

**`debug <message>...`** - Log a debug level message  
- **Parameters**: `message` (required): `Any` - Message to log (supports multiple parameters)  
- **Returns**: `None`  
- **Color**: Blue `[DEBUG]` prefix  
- **Example**:
  ```bash
  Log.debug "Processing request" $request_id
  # Output: [DEBUG] Processing request req-12345

  Log.debug "Variable value:" $var
  ```

**`error <message>...`** - Log an error level message  
- **Parameters**: `message` (required): `Any` - Message to log (supports multiple parameters)  
- **Returns**: `None`  
- **Color**: Red `[ERROR]` prefix  
- **Example**:
  ```bash
  Log.error "Database connection failed"
  # Output: [ERROR] Database connection failed

  Log.error "File" $filename "read failed:" $error_msg
  ```

**`trace <message>...`** - Log a trace level message  
- **Parameters**: `message` (required): `Any` - Message to log (supports multiple parameters)  
- **Returns**: `None`  
- **Color**: Magenta `[TRACE]` prefix  
- **Example**:
  ```bash
  Log.trace "Entering function process_data"
  # Output: [TRACE] Entering function process_data

  Log.trace "Iteration number" $i
  ```

## Raw Output Functions

**`echo <message>...`** - Output without formatting  
- **Parameters**: `message` (required): `Any` - Message to output (supports multiple parameters)  
- **Returns**: `None`  
- **Example**:
  ```bash
  Log.echo "Plain text output, no level prefix"
  # Output: Plain text output, no level prefix

  Log.echo "Value1" "Value2" "Value3"
  # Output: Value1 Value2 Value3
  ```

## Multi-line Output Support

The Log module supports formatted output for multi-line messages:

**Example**:
```bash
Log.info "Multi-line message:\nFirst line\nSecond line\nThird line"
# Output:
# [INFO] Multi-line message:
#        First line
#        Second line
#        Third line
```

**Rules**:

1.  The first line displays a colored label.
2.  Subsequent lines are indented to align with the label length.
3.  A newline character is automatically appended at the end.

## Notes

The Log module is an important component of the Lumesh built-in module system, registered as "Log" in the module registry. This module provides a complete logging solution, supporting colored output, level control, and multi-line formatting. The default log level is INFO (3), which can be dynamically adjusted using the `set_level` function.

---

> Note: Color effects should be viewed in a terminal that supports ANSI.
