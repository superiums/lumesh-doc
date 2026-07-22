---
title: Simple Script Examples
date: 2025-07-05 19:16:45
highlight: true
tags:
 - glance
categories:
 - wiki
 - why
 - syntax
---

Simple Examples to OPerate Data Structure via Lumesh.
<!--more-->
- Directly access nested properties
```bash
let user = {
    name: "Alice",
    profile: {
        age: 25,
        skills: ["rust", "javascript", "python"]
    }
}
user.profile.skills@1  # Output: "javascript"
```

- Chained calls
```bash
1...10 | .map(x -> x * 2) | .filter(x -> x > 10)
# Chained calls, clear and intuitive
```

- Loop dispatch pipeline
```bash
ls -1 |> print "-->" _ "<--"
```

- Structured pipeline
```bash
df -H | into.table() | pprint
fs.ls -l | where(size > 5K) | select(name,size,modified)
```

- Error capture
```bash
6 / 0 ?.               # Ignore error
6 / 0 ?: x -> print x  # Handle error
```

- Data debugging
```bash
let a := (x) -> x + 1
debug a
```

- Mapping operations
```bash
# Mapping transformation
let data = {a: 1, b: 2, c: 3}

# Transform keys and values simultaneously
let result = map.map(
    k -> k.to_upper(),     # Key transformation function
    v -> v * 2,           # Value transformation function
    data
)
# Result: {A: 2, B: 4, C: 6}
```

## Practical Examples

### File Handling:

- Find files larger than 5KB modified within the last 24 hours and display in a table:
  ```bash
  fs.ls -l ./src/ | where(size > 5K) | where (fs.diff('d',modified) > 1) | pprint
  ```

- Backup all .rs source files in the current directory and its subdirectories:
  1. Method 1: Using loop dispatch pipeline
  ```bash
    ls **/*.rs |> cp _ ./backup/
  ```

  2. Method 2: Using loop
  ```bash
    for f in **/*.rs {
        cp _ ./backup
    }
  ```

- Remove comments from a file and save:
  1. Method 1: Functional
  ```bash
  let content = fs.read("data.txt")
  let lines = content.lines()
  let filtered = lines | list.filter(line -> !line.starts_with('#'))
  # Write result
  filtered.join('\n') | fs.write("output.txt")
  ```

  2. Method 2: Using chained calls
  ```bash
  fs.read("data.txt").lines().filter(x -> !x.starts_with('#')) >> "output.txt"
  ```

  3. Method 3: Using chained pipeline
  ```bash
  fs.read("data.txt") | .lines() | .filter(x -> !x.starts_with('#')) >> "output.txt"
  ```

### System Management

- Find user processes with CPU usage over 2% and display in a table
```bash
 ps u -u1000 | into.table() | where(into.float(CPU) > 2.0) | pprint
```

- Find processes using more than 10% of memory
```bash
ps u -u1000 | into.table() | where(into.float(MEM) > 10.0) | pprint
```

### Network Operations

- Request JSON data and interpret it as a table
```bash
# HTTP request
curl 'https://jsonplaceholder.typicode.com/posts/1/comments' | from.json() | pprint

# Further filtering
curl 'https://jsonplaceholder.typicode.com/posts/1/comments' | from.json() | where(id < 3) | select(name,email) | pprint
```

- Request JSON data and save in other formats
```bash
let a = curl 'https://jsonplaceholder.typicode.com/posts/1/comments' | from.json()
a >> data.json                         # JSON format
a | into.csv() >> data.csv         # CSV format
a | into.toml() >> data.toml       # TOML format

# a is already a valid Lumesh expression
type a     # List
len(a)     # Can perform other regular operations
```

### Operations and Maintenance Scripts

- Write a script to let users select mountable disks
```bash
let sel = ( lsblk -rno 'name,type,size,mountpoint,label,fstype' | into.table([name,'type',size,mountpoint,label,fstype]) \
    | where($type != 'disk' && !$mountpoint && $fstype !~: 'member') \
    | ui.pick "which to mount:") ?: { print 'no device selected' }

if $sel {
    let src = (sel.type == 'part' ? `/dev/${sel.name}` : `/dev/mapper/${sel.name}`)
    let point = (sel.label == none ? sel.name : sel.label)
    let dest = `/run/user/${id - u}/media/${point}`
    if !fs.exists($dest) { mkdir -p $dest }
    sudo mount -m -o 'defaults,noatime' $src $dest ?: \
        e -> { notify-send 'Mount Failed' $e.msg; exit 1 }
    notify-send 'Mount' `device $src mounted`
}
```

## Common Built-in Modules

### File System Module (Fs)

```bash
# Directory operations
fs.ls("/path")              # List directory contents
fs.mkdir("new_dir")         # Create directory
fs.rm("empty_dir")          # Remove empty directory

# File operations
fs.read("file.txt")         # Read file
fs.write("file.txt", data)  # Write to file
fs.append("log.txt", entry) # Append content

# Path operations
fs.exists("/path/to/file")  # Check if path exists
fs.is_dir("/path")          # Check if it is a directory
fs.canon("./relative/path")  # Get absolute path
```

### System Directory Access

```bash
# Get system directories
let dirs = fs.dirs()
println(dirs.home)      # User home directory
println(dirs.config)    # Configuration directory
println(dirs.cache)     # Cache directory
println(dirs.current)   # Current working directory
```

### Detailed File List Information

```bash
# ls command options
fs.ls -l        # Detailed information
fs.ls -a        # Show hidden files
fs.ls -L        # Follow symbolic links
fs.ls -u        # Show user information
fs.ls -m        # Show permission mode
fs.ls -p        # Show full path
```

## Common Built-in Functions

### Core Functions

```bash
# Data operations
len(collection)             # Get length
type(value)                 # Get type
rev("string")               # Reverse string/list
flatten([[1,2],[3,4]])      # Flatten nested structure

# Execution control
eval(expression)            # Evaluate expression
exec_str("let x = 10")      # Execute string code
include("script.lm")        # Include file into current environment
import("module.lm")         # Import module into new environment
```

### Data Querying and Filtering

```bash
# Data table operations
let users = [
    {name: "Alice", age: 25, active: True},
    {name: "Bob", age: 30, active: False},
    {name: "Carol", age: 35, active: True}
]

# Filter rows
let active_users = where(active, users)

# Select columns
let names_ages = select(name, age, users)
```

## Command Execution System

### External Command Execution

Lumesh supports various command execution modes:

```bash
# Basic command execution
ls -la

# Background execution
command &

# Silent execution
command &-
```

### PTY Support

For programs requiring terminal interaction, Lumesh provides PTY support:

```bash
# Automatically determine and enable PTY mode
ls -l | vi

# Force PTY mode (for interactive programs), generally not needed
ls -l |^ vi
'' |^ vi 'file.txt'
```

## Script Execution

### Script Runner

Lumesh provides two execution modes:

```bash
# Directly execute command
lumesh -c "print 'Hello World'"

# Execute script file
lumesh script.lm arg1 arg2

# Access parameters in the script
println(argv[0])  # First parameter
```

### Test Example

```bash
#!/usr/bin/env lumesh

# Test function
fn assert(actual, expected, test_name) {
    if actual != expected {
        print "[FAIL]" test_name "| Actual: " actual "| Expected: " expected
    } else {
        print "[PASS]" test_name
    }
}

# Variable assignment test
let x = 10
assert(str(x), "10", "Single Variable Assignment")

# Delayed assignment test
x := 2 + 3
assert(eval(x), 5, "Delayed Assignment Evaluation")
```

## Logging System

### Log Level Management

```bash
# Set log level
log.set_level(log.level.info)    # Set to INFO level

# Check log level
if log.enabled(1) {
    log.debug("Debugging enabled")
}
log.info("Debug information")

# Disable logging
log.disable()
```

Learn more:
- [Use Cases](/cases)
- [lf Configuration File Comparison (lumesh vs bash)](/cases/lf)
- [Syntax Demonstration for Writing lf Configuration Files in Lumesh](/cases/lf/case_lf)
- [Feature Overview (superiums/lumesh)](/feature)
- [Syntax Manual (superiums/lumesh)](/doc/syntax)
- [Built-in Functions (superiums/lumesh)](/doc/libs/)
