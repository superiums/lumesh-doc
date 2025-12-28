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
df -H | Into.table() | pprint
Fs.ls -l | where(size > 5K) | select(name,size,modified)
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
let result = Map.map(
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
  Fs.ls -l ./src/ | where(size > 5K) | where (Fs.diff('d',modified) > 1) | pprint
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
  let content = Fs.read("data.txt")
  let lines = content.lines()
  let filtered = lines | List.filter(line -> !line.starts_with('#'))
  # Write result
  filtered.join('\n') | Fs.write("output.txt")
  ```

  2. Method 2: Using chained calls
  ```bash
  Fs.read("data.txt").lines().filter(x -> !x.starts_with('#')) >> "output.txt"
  ```

  3. Method 3: Using chained pipeline
  ```bash
  Fs.read("data.txt") | .lines() | .filter(x -> !x.starts_with('#')) >> "output.txt"
  ```

### System Management

- Find user processes with CPU usage over 2% and display in a table
```bash
 ps u -u1000 | Into.table() | where(Into.float(CPU) > 2.0) | pprint
```

- Find processes using more than 10% of memory
```bash
ps u -u1000 | Into.table() | where(Into.float(MEM) > 10.0) | pprint
```

### Network Operations

- Request JSON data and interpret it as a table
```bash
# HTTP request
curl 'https://jsonplaceholder.typicode.com/posts/1/comments' | From.json() | pprint

# Further filtering
curl 'https://jsonplaceholder.typicode.com/posts/1/comments' | From.json() | where(id < 3) | select(name,email) | pprint
```

- Request JSON data and save in other formats
```bash
let a = curl 'https://jsonplaceholder.typicode.com/posts/1/comments' | From.json()
a >> data.json                         # JSON format
a | Into.csv() >> data.csv         # CSV format
a | Into.toml() >> data.toml       # TOML format

# a is already a valid Lumesh expression
type a     # List
len(a)     # Can perform other regular operations
```

### Operations and Maintenance Scripts

- Write a script to let users select mountable disks
```bash
let sel = ( lsblk -rno 'name,type,size,mountpoint,label,fstype' | Into.table([name,'type',size,mountpoint,label,fstype]) \
    | where($type != 'disk' && !$mountpoint && $fstype !~: 'member') \
    | Ui.pick "which to mount:") ?: { print 'no device selected' }

if $sel {
    let src = (sel.type == 'part' ? `/dev/${sel.name}` : `/dev/mapper/${sel.name}`)
    let point = (sel.label == None ? sel.name : sel.label)
    let dest = `/run/user/${id - u}/media/${point}`
    if !Fs.exists($dest) { mkdir -p $dest }
    sudo mount -m -o 'defaults,noatime' $src $dest ?: \
        e -> { notify-send 'Mount Failed' $e.msg; exit 1 }
    notify-send 'Mount' `device $src mounted`
}
```

## Common Built-in Modules

### File System Module (Fs)

```bash
# Directory operations
Fs.ls("/path")              # List directory contents
Fs.mkdir("new_dir")         # Create directory
Fs.rm("empty_dir")          # Remove empty directory

# File operations
Fs.read("file.txt")         # Read file
Fs.write("file.txt", data)  # Write to file
Fs.append("Log.txt", entry) # Append content

# Path operations
Fs.exists("/path/to/file")  # Check if path exists
Fs.is_dir("/path")          # Check if it is a directory
Fs.canon("./relative/path")  # Get absolute path
```

### System Directory Access

```bash
# Get system directories
let dirs = Fs.dirs()
println(dirs.home)      # User home directory
println(dirs.config)    # Configuration directory
println(dirs.cache)     # Cache directory
println(dirs.current)   # Current working directory
```

### Detailed File List Information

```bash
# ls command options
Fs.ls -l        # Detailed information
Fs.ls -a        # Show hidden files
Fs.ls -L        # Follow symbolic links
Fs.ls -u        # Show user information
Fs.ls -m        # Show permission mode
Fs.ls -p        # Show full path
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
Log.set_level(Log.level.info)    # Set to INFO level

# Check log level
if Log.enabled(1) {
    Log.debug("Debugging enabled")
}
Log.info("Debug information")

# Disable logging
Log.disable()
```

Learn more:
- [Use Cases](/cases)
- [lf Configuration File Comparison (lumesh vs bash)](/cases/lf)
- [Syntax Demonstration for Writing lf Configuration Files in Lumesh](/cases/lf/case_lf)
- [Feature Overview (superiums/lumesh)](/feature)
- [Syntax Manual (superiums/lumesh)](/doc/syntax)
- [Built-in Functions (superiums/lumesh)](/doc/libs/)
