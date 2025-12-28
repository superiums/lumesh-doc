---
title: Lumesh Built-in Function Library
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - top
categories:
 - wiki
 - libs
---

## Top-Level Function Introduction

### Shell Control Functions

#### Process Control
**`exit [status]`** - Exit the shell program
- **Parameters**:
  - `status` (optional): `Integer` - Exit status code, defaults to 0
- **Example**:
  - `exit` - Normal exit
  - `exit 1` - Exit with status code 1

#### Directory Navigation
**`cd [path]`** - Change the current working directory
- **Parameters**:
  - `path` (optional): `String|Symbol` - Target directory path, supports `~` home directory expansion, defaults to the current directory
- **Example**:
  - `cd "/home/user"` - Switch to absolute path
  - `cd "~/Documents"` - Switch to Documents under home directory
  - `cd` - Switch to home directory

**`pwd`** - Display the current working directory
- **Parameters**: None
- **Returns**: `String` - Absolute path of the current directory

#### Environment Variable Management
**`set <var> <val>`** - Define a variable in the root environment
- **Parameters**:
  - `var` (required): `String|Symbol` - Variable name
  - `val` (required): `Any` - Variable value
- **Example**: `set "PATH" "/usr/bin:/bin"`

**`unset <var>`** - Remove a variable from the root environment
- **Parameters**:
  - `var` (required): `String|Symbol` - Name of the variable to remove

### Input and Output Functions

#### Standard Output
**`print <args>...`** - Print parameters to standard output, separated by spaces, without a newline
- **Parameters**:
  - `args` (variable): `Any...` - List of values to print
- **Example**: `print "Hello" "World" 123`

**`println <args>...`** - Print parameters to standard output, separated by spaces, with a newline at the end
- **Parameters**:
  - `args` (variable): `Any...` - List of values to print
- **Example**: `println "Line1" "Line2"`

**`tap <args>...`** - Print parameters and return the result, used for debugging pipelines
- **Parameters**:
  - `args` (variable): `Any...` - Values to print and return
- **Returns**: Returns the value if a single parameter, or a list if multiple parameters
- **Purpose**: Insert debug output in the middle of a pipeline without affecting the data flow

#### Error Output
**`eprint <args>...`** - Output to standard error stream, without a newline
**`eprintln <args>...`** - Output to standard error stream, each parameter on a new line
- **Parameters**:
  - `args` (variable): `Any...` - List of values to output
**`throw <string>`** - Throw an error
- **Parameters**:
  - `msg` (required): `String` - Error message to throw

#### Debugging and Formatting Output
**`debug <args>...`** - Print the debug representation of parameters (similar to Rust's `{:?}` format)
- **Parameters**:
  - `args` (variable): `Any...` - Values to debug print
- **Purpose**: Display the internal structure of data for debugging

**`pprint <list>|<map>`** - Pretty print structured data (lists, maps, etc.)
- **Parameters**:
  - `data` (required): `List|Map` - Structured data to pretty print
- **Purpose**: Display complex data structures in an easy-to-read format

#### User Input
**`read [prompt]`** - Get user input
- **Parameters**:
  - `prompt` (optional): `String` - Input prompt message
- **Returns**: `String` - User input content
- **Example**: `read "Enter your name: "`

### Data Manipulation Functions

#### Data Access
**`get <path> <map|list|range>`** - Access values from nested structures using dot notation
- **Parameters**:
  - `path` (required): `String|Symbol|Integer` - Access path, supports dot separation
  - `data` (required): `Map|List|Range` - Data structure to access
- **Example**:
  - `get "user.name" data` - Get value from nested map
  - `get "items.0.title" data` - Get title field of the first element in the list

    ```bash
    let nested = {a: {b: {c: [42, 43]}}, r: [0..3, 10..15]};
    get "a.b.c.0" nested  # Returns 42
    get "a.r.0" nested    # Returns 0..3
    get "a.r.0.1" nested  # Returns 1
    ```

**`type <value>`** - Get the type of data
- **Parameters**:
  - `value` (required): `Any` - Value to check type
- **Returns**: `String` - Type name
- **Example**: `type 123` returns "Integer"

**`len <collection>`** - Get the length of a collection
- **Parameters**:
  - `collection` (required): `String|List|Map|HMap|Bytes|Range` - Collection to calculate length
- **Returns**: `Integer` - Length of the collection
- **Supported Types**: Strings (character count), lists, maps, hash maps, byte arrays, ranges

#### Data Modification
**`insert <key/index> <value> <collection>`** - Insert an item into a collection
- **Parameters**:
  - `key` (required): `String|Integer` - Insertion position (key for maps or index for lists)
  - `value` (required): `Any` - Value to insert
  - `collection` (required): `Map|List` - Target collection
- **Returns**: The modified new collection
    ```bash
    insert 0 "X" ["A", "B"]  # Returns ["X", "A", "B"]
    insert "key" 42 {}      # Returns {key: 42}
    ```

**`rev <string|list|bytes>`** - Reverse a sequence
- **Parameters**:
  - `sequence` (required): `String|List|Bytes` - Sequence to reverse
- **Returns**: The new reversed sequence

**`flatten <collection>`** - Flatten a nested structure
- **Parameters**:
  - `collection` (required): `List|Map` - Nested structure to flatten
- **Returns**: `List` - The flattened list
- **Purpose**: Expand nested lists or maps into a single-layer list

#### Data Querying
**`where <condition> <list[map]>`** - Filter rows by condition
- **Parameters**:
  - `condition` (required): `Expression` - Filtering condition expression
  - `data` (required): `List[Map]` - Map list to filter
- **Returns**: `List[Map]` - Rows that meet the condition
- **Special Variables**:
  - `LINENO` - Current line number (starting from 0)
  - `LINES` - Total number of lines
- **Example**: `where (age > 18) users`
    ```bash
    Fs.ls -l | where(size < 1K)
    Fs.ls -l | where(LINENO > 1)
    ```

**`select <columns>...<list[map]>`** - Select columns from a map list
- **Parameters**:
  - `columns` (variable): `String...` - Column names to select
  - `data` (required): `List[Map]` - Source data (map list)
- **Returns**: `List[Map]` - A new list containing only the specified columns
- **Example**: `select "name" "age" users`

    ```bash
      Fs.ls -l | select(name, size)
    ```

### Execution Control Functions

#### Expression Evaluation
**`eval <expr>`** - Evaluate an expression
- **Parameters**:
  - `expr` (required): `Expression` - Expression to evaluate
- **Purpose**: Dynamically execute an expression

**`exec_str <string>`** - Evaluate a string
- **Parameters**:
  - `string` (required): `String` - Code contained in the string
- **Purpose**: Execute code in string form

**`exec <expr>`** - Evaluate in the current environment
- **Parameters**:
  - `expr` (required): `Expression` - Expression to execute in the current environment

**`repeat <count> <expr>`** - Repeat execution of an expression a specified number of times
- **Parameters**:
  - `count` (required): `Integer` - Number of repetitions
  - `expr` (required): `Expression` - Expression to repeat
- **Returns**: `List` - A list containing all execution results
- **Purpose**: Repeat an expression and collect results
- **Example**: `repeat 3 (Math.rand())` - Generate a list of 3 random numbers

#### File Execution
**`include <path>`** - Execute a file in the current environment
- **Parameters**:
  - `path` (required): `String` - Path to the file to execute
- **Purpose**: Execute the content of the file in the current environment; variable modifications will affect the current environment

**`import <path>`** - Execute a file in a new environment
- **Parameters**:
  - `path` (required): `String` - Path to the file to execute
- **Purpose**: Execute the file in an isolated environment, without affecting the current environment

For modular programming, it is recommended to use the `use` statement.

`include` and `import` are equivalent to directly embedding code into the current file.

While `use` adopts a namespace.

### Help System

**`help [module]`** - Display help information
- **Parameters**:
  - `module` (optional): `String` - Name of the module to view help for
  - `libs/tops`     - List modules/top-level functions
- **Behavior**:
  - No parameters: Displays a list of all available modules
  - With parameters: Displays a detailed function list for the specified module
- **Example**:
  - `help` - Displays all modules
  - `help Math` - Displays functions in the Math module

**Dynamic Hint**: type `String. ` to see available functions in that module.

## Notes

These top-level built-in functions form the core functional framework of the Lumesh shell. Parameter type descriptions indicate that `<>` denotes required parameters, `[]` denotes optional parameters, and `...` denotes variable parameters. All functions have complete parameter validation and error handling mechanisms to ensure type safety and user-friendly error messages.
