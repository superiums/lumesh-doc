---
title: Lumesh Into Module
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - into
 - conversion
categories:
 - wiki
 - libs
---

The Into module provides comprehensive data type conversion capabilities, supporting conversions between different data types, including basic type conversions, time parsing, data serialization, and parsing third-party command outputs. All conversion functions offer detailed error handling and type validation.

## Function Overview

| Function Category | Main Functions | Purpose |
|-------------------|----------------|--------|
| **Basic Type Conversion** | `str`, `int`, `float`, `boolean`, `filesize` | Basic data type conversions |
| **Time Conversion** | `time` | String to date-time conversion |
| **Table Conversion** | `table` | Convert command output to table format |
| **Serialization** | `toml`, `json`, `csv` | Serialize data into different formats |
| **Highlight** | `highlighted` | Convert to Ansi Highlighted code |

## Basic Type Conversion Functions

**`str <value>`** - Format an expression as a string
- **Parameters**: `value` (required): `Any` - The value to convert
- **Returns**: `String` - The formatted string representation
- **Example**: `Into.str(123)` returns `"123"`

**`int <value>`** - Convert a float or string to an integer
- **Parameters**: `value` (required): `Float|String` - The value to convert
- **Returns**: `Integer` - The converted integer
- **Error**: Throws an error if conversion fails
- **Example**:
  - `Into.int("123")` returns `123`
  - `Into.int(3.14)` returns `3`

**`float <value>`** - Convert an integer or string to a float
- **Parameters**: `value` (required): `Integer|String` - The value to convert
- **Returns**: `Float` - The converted float
- **Error**: Throws an error if conversion fails
- **Example**:
  - `Into.float("3.14")` returns `3.14`
  - `Into.float(123)` returns `123.0`

**`boolean <value>`** - Convert a value to a boolean
- **Parameters**: `value` (required): `Any` - The value to convert
- **Returns**: `Boolean` - The converted boolean
- **Logic**: Uses Lumesh's truthiness rules

**`filesize <size_str>`** - Parse a file size string into bytes
- **Parameters**: `size_str` (required): `String` - File size string (e.g., "1KB", "2MB", "3GB")
- **Returns**: `Integer` - Corresponding byte count
- **Supported Units**: B, KB, MB, GB, TB, PB
- **Example**:
  - `Into.filesize("1KB")` returns `1024`
  - `Into.filesize("2.5MB")` returns `2621440`

## Time Conversion Functions

**`time <datetime_str> [datetime_template]`** - Convert a string to a date-time
- **Parameters**:
  - `datetime_str` (required): `String` - Date-time string
  - `datetime_template` (optional): `String` - Date-time format template
- **Returns**: `DateTime` - The parsed date-time object
- **Example**:
  - `Into.time("2023-12-25")` - Parses ISO format date
  - `Into.time("25/12/2023", "%d/%m/%Y")` - Uses custom format

## Table Conversion Functions

**`table [headers|header...] <command_output>`** - Convert third-party command output to a table
- **Parameters**:
  - `headers` (optional): `List[String]|String...` - Table header definitions
  - `command_output` (required): `String` - Command output string
- **Returns**: `List[Map]` - Structured table data
- **Purpose**: Parse outputs from commands like `ps`, `ls`, `df` into structured data
- **Example**:
  ```bash
  ps aux | Into.table()
  ls -l | Into.table("mode", "links", "owner", "group", "size", "date", "name")
  ```

## Serialization Functions

**`toml <expr>`** - Serialize a Lumesh expression to TOML
- **Parameters**: `expr` (required): `Any` - Expression to serialize
- **Returns**: `String` - TOML format string
- **Supported Types**: Maps, lists, basic types
- **Example**: `Into.toml({"name": "Alice", "age": 30})`

**`json <expr>`** - Serialize a Lumesh expression to JSON
- **Parameters**: `expr` (required): `Any` - Expression to serialize
- **Returns**: `String` - JSON format string
- **Supported Types**: Maps, lists, basic types
- **Example**: `Into.json([1, 2, 3])` returns `"[1,2,3]"`

**`csv <expr>`** - Serialize a Lumesh expression to CSV
- **Parameters**: `expr` (required): `List[Map]` - Table data to serialize
- **Returns**: `String` - CSV format string
- **Requirements**: Input must be a list of maps (table format)
- **Example**:
  ```bash
  [{"name": "Alice", "age": 30}, {"name": "Bob", "age": 25}] | Into.csv()
  ```

## Highlight
**`highlighted <script_string>`** - Syntax highlight a script string
- **Parameters**: `script_string` (required): `String` - Script string to highlight
- **Returns**: `String` - Highlighted string
- **Example**:
  ```bash
  Into.highlighted "let x = 10; print x"
  # Returns a highlighted string with ANSI color codes

  Fs.read "script.lm" | Into.highlighted() | print
  # Highlight and display the content of the script file
  ```

**`striped <string>`** - Remove all ANSI escape codes
- **Parameters**: `string` (required): `String` - String containing ANSI escape codes
- **Returns**: `String` - Plain text without escape codes

## Usage Examples

### Basic Type Conversion
```bash
# Number conversion
Into.int("42")        # Returns 42
Into.float("3.14")    # Returns 3.14
Into.str(123)         # Returns "123"

# Boolean conversion
Into.boolean(1)       # Returns true
Into.boolean("")      # Returns false
```

### File Size Conversion
```bash
# Parse file sizes
Into.filesize("1KB")    # Returns 1024
Into.filesize("2.5MB")  # Returns 2621440
Into.filesize("1GB")    # Returns 1073741824
```

### Data Serialization
```bash
# JSON serialization
let data = {"users": [{"name": "Alice"}, {"name": "Bob"}]}
Into.json(data)

# TOML serialization
let config = {"database": {"host": "localhost", "port": 5432}}
Into.toml(config)

# CSV serialization
Fs.ls("-l") | Into.csv()
```

### Command Output Parsing
```bash
# Parse system command output
ps aux | Into.table() | where(cpu > 5.0)
df -h | Into.table() | select("filesystem", "used", "available")
```

## Pipeline Operation Examples

```bash
# Data processing pipeline
"123.45" | Into.float() | Math.round() | Into.str()
# Result: "123"

# File size processing
Fs.ls("-l") | List.map((f) -> Into.filesize(f.size)) | List.sum()
# Calculate total size of the directory

# Configuration file generation
{"server": {"port": 8080, "host": "0.0.0.0"}} | Into.toml() | Fs.write("config.toml")
```

## Notes

The Into module is an important data conversion tool in Lumesh, providing type-safe conversion mechanisms. All conversion functions include detailed error handling to ensure clear error messages when conversions fail. Parameter type descriptions indicate that `<>` denotes required parameters, and `[]` denotes optional parameters. Notably, file size conversions support common storage units, and time conversions support various date formats.

Most functions are already connected to the corresponding types and can be used with chained calls, such as:

```bash
"36".to_int()
'2000K'.to_filesize()
```
