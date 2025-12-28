---
title: Lumesh From Module
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - parse
 - from
categories:
 - wiki
 - libs
---

The From module provides data format parsing and conversion capabilities, supporting the parsing and serialization of common data formats such as JSON, TOML, and CSV, as well as advanced features like expression parsing, syntax highlighting, and data querying. All functions support pipeline operations and provide a unified error handling mechanism.

## Function Overview

| Function Category | Main Functions | Purpose |
|-------------------|----------------|--------|
| **Data Format Parsing** | `json`, `toml`, `csv` | From various data formats into Lumesh expressions |
| **Expression Parsing** | `script`   | From script strings and syntax highlighting |
| **Command Output Parsing** | `cmd` | From command output into structured data |
| **Data Querying** | `jq` | Perform jq-like queries on JSON/TOML data |

## Data Format Parsing Functions

These functions parse various data formats into Lumesh expressions:

**`json <json_string>`** - From JSON into a Lumesh expression
- **Parameters**: `json_string` (required): `String` - JSON string to parse
- **Returns**: `Expression` - Fromd expression
- **Example**:
  ```bash
  From.json '{"name": "Alice", "age": 30}'
  # Returns: {name: "Alice", age: 30}

  '{"items": [1, 2, 3]}' | From.json()
  # Returns: {items: [1, 2, 3]}
  ```

**`toml <toml_string>`** - From TOML into a Lumesh expression
- **Parameters**: `toml_string` (required): `String` - TOML string to parse
- **Returns**: `Expression` - Fromd expression
- **Example**:
  ```bash
  From.toml 'name = "Alice"\nage = 30'
  # Returns: {name: "Alice", age: 30}

  Fs.read "config.toml" | From.toml()
  # From TOML configuration file
  ```

**`csv <csv_string>`** - From CSV into a Lumesh expression
- **Parameters**: `csv_string` (required): `String` - CSV string to parse
- **Returns**: `List[Map]` - Fromd table data
- **Example**:
  ```bash
  From.csv "name,age\nAlice,30\nBob,25"
  # Returns: [{name: "Alice", age: "30"}, {name: "Bob", age: "25"}]

  # Supports custom delimiters (via IFS environment variable)
  IFS = ";"
  From.csv "name;age\nAlice;30"
  ```


## Expression Parsing Functions

These functions are used to parse and process Lumesh scripts:

**`script <script_string>`** - From a script string into a Lumesh expression
- **Parameters**: `script_string` (required): `String` - Script string to parse
- **Returns**: `Expression` - Fromd expression
- **Example**:
  ```bash
  From.script "1 + 2 * 3"
  # Returns: 7

  From.script "let x = 10; x * 2" | eval()
  # Dynamically execute script code
  ```

## Command Output Parsing Functions

**`cmd [headers|header...] <cmd_output_string>`** - From command output into structured data
- **Parameters**: `headers` (optional): `List` - Custom table headers, `cmd_output_string` (required): `String` - Command output string
- **Returns**: `List[Map]` - Structured table data
- **Example**:
  ```bash
  ls -l | From.cmd()
  # Automatically detects headers and parses ls output

  ps aux | From.cmd(USER, PID, CPU, MEM, COMMAND)
  # From ps output using custom headers

  df -h | From.cmd() | .drop(1) | where(C2.to_filesize() > 10G) | pprint
  # From disk usage and filter
  ```

## Data Query Functions

**`jq <query_string> <json_data>`** - Apply jq-like queries to JSON or TOML data
- **Parameters**: `query_string` (required): `String` - Query string, `json_data` (required): `Expression` - JSON data
- **Returns**: `Expression` - Query result
- **Example**:
  ```bash
  From.jq ".name" '{"name": "Alice", "age": 30}'
  # Returns: "Alice"

  From.jq ".[]" '[1, 2, 3]'
  # Returns all elements in the array

  From.jq "select(.age > 25)" '[{"name":"Alice","age":30},{"name":"Bob","age":20}]'
  # Filter records with age greater than 25
  ```

## Notes

The From module is an important component of the Lumesh built-in module system, registered as "From" in the module registry.

This module overlaps with some functionalities of the Into module, which also provides aliases for some serialization functions. CSV parsing supports custom delimiters via the `IFS` environment variable, with a default of using a comma as the separator.
