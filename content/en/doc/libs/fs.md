---
title: Lumesh Fs Module  
date: 2025-07-13 19:16:45  
highlight: true  
tags:  
 - libs  
 - fs  
 - filesystem  
categories:  
 - wiki  
 - libs  
---

The Fs module provides comprehensive file system operation capabilities, including file reading and writing, directory management, path manipulation, and file attribute querying. All functions support home directory expansion (`~`) and relative path handling, providing a unified error handling mechanism.

## Function Overview

| Function Category | Main Functions | Purpose |
|-------------------|----------------|--------|
| **System Directories** | `dirs` | Get standard system directories |
| **Directory Operations** | `ls`, `glob`, `tree`, `mkdir`, `rmdir` | Directory browsing and management |
| **File Operations** | `mv`, `cp`, `rm` | Move, copy, and delete files |
| **Path Operations** | `canon`, `join`, `base_name` | Path handling and parsing |
| **Status Checks** | `exists`, `is_dir`, `is_file` | File system status queries |
| **File Reading and Writing** | `read`, `write`, `append`, `head`, `tail` | File content operations |

## System Directory Functions

**`dirs`** - Get standard system directories  
- **Parameters**: None  
- **Returns**: `Map` - A mapping containing system directory paths  
- **Includes Directories**:
  - `home` - User home directory
  - `config` - Configuration directory
  - `cache` - Cache directory
  - `data` - Data directory
  - `pic` - Pictures directory
  - `desk` - Desktop directory
  - `docs` - Documents directory
  - `down` - Downloads directory
  - `current` - Current working directory

## Directory Operation Functions

**`ls [path]`** - List directory contents  
- **Parameters**:
  - `path` (optional): `String` - Directory path, defaults to the current directory  
- **Returns**: `List[Map]` - A list of file information  
- **Supports Options**:
  - `-l` - Detailed information
  - `-a` - Show hidden files
  - `-L` - Follow symbolic links
  - `-U` - Unix timestamp
  - `-k` - Display size in KB
  - `-u` - Show user information
  - `-m` - Display permission mode
  - `-p` - Show full path

**`glob <pattern>`** - Pattern match files  
- **Parameters**:
  - `pattern` (required): `String` - File matching pattern  
- **Returns**: `List[String]` - List of matched file paths  

**`tree [path]`** - Get directory tree structure  
- **Parameters**:
  - `path` (optional): `String` - Directory path, defaults to the current directory  
- **Returns**: `Map` - Nested directory tree structure  

**`mkdir <path>`** - Create a directory  
- **Parameters**:
  - `path` (required): `String` - Path of the directory to create  
- **Functionality**: Recursively create directories (similar to `mkdir -p`)  

**`rmdir <path>`** - Remove an empty directory  
- **Parameters**:
  - `path` (required): `String` - Path of the directory to remove  
- **Note**: Can only remove empty directories  

## File Operation Functions

**`mv <source> <destination>`** - Move a file or directory  
- **Parameters**:
  - `source` (required): `String` - Source path  
  - `destination` (required): `String` - Destination path  
- **Functionality**: Supports renaming and moving operations  
- **Special Handling**: If the destination path ends with `/`, the source file will be moved into that directory  

**`cp <source> <destination>`** - Copy a file or directory  
- **Parameters**:
  - `source` (required): `String` - Source path  
  - `destination` (required): `String` - Destination path  
- **Functionality**: Recursively copy directories and files  

**`rm <path>`** - Remove a file or directory  
- **Parameters**:
  - `path` (required): `String` - Path to remove  
- **Functionality**: Automatically determines whether to delete a file or directory  

## Path Operation Functions

**`canon <path>`** - Normalize a path  
- **Parameters**:
  - `path` (required): `String` - Path to normalize  
- **Returns**: `String` - The normalized absolute path  

**`join <path>...`** - Join paths  
- **Parameters**:
  - `path` (variable): `String...` - Path components to join  
- **Returns**: `String` - The joined path  

**`dir_name <path>`** - Extract directory path  
- **Parameters**:
  - `path` (required): `String` - File path  
- **Returns**: `String` - Directory path  

**`base_name [split_ext?] <path>`** - Extract file name  
- **Parameters**:
  - `split_ext` (optional): `Boolean` - Whether to separate the extension  
  - `path` (required): `String` - File path  
- **Returns**: `String` - File name or `List` - `[base name, extension]`  

## Status Check Functions

**`exists <path>`** - Check if a path exists  
- **Parameters**:
  - `path` (required): `String` - Path to check  
- **Returns**: `Boolean` - Returns true if the path exists  

**`is_dir <path>`** - Check if it is a directory  
- **Parameters**:
  - `path` (required): `String` - Path to check  
- **Returns**: `Boolean` - Returns true if it is a directory  

**`is_file <path>`** - Check if it is a file  
- **Parameters**:
  - `path` (required): `String` - Path to check  
- **Returns**: `Boolean` - Returns true if it is a file  

## File Reading and Writing Functions

**`read <file>`** - Read file content  
- **Parameters**:
  - `file` (required): `String` - File path  
- **Returns**: `String|Bytes` - File content (automatically detects text or binary)  
- **Functionality**: Prioritizes reading as text; if it fails, reads as a byte array  

**`write <file> <content>`** - Write to a file  
- **Parameters**:
  - `file` (required): `String` - File path  
  - `content` (required): `String|Bytes` - Content to write  
- **Functionality**: Supports writing strings and byte arrays  

**`append <file> <content>`** - Append to a file  
- **Parameters**:
  - `file` (required): `String` - File path  
  - `content` (required): `String|Bytes` - Content to append  
- **Functionality**: Appends content to the end of the file  

**`head [n] <file>`** - Read the first N lines of a file  
- **Parameters**:
  - `n` (optional): `Integer` - Number of lines, defaults to 10  
  - `file` (required): `String` - File path  
- **Returns**: `List[String]` - The first N lines of the file  

**`tail [n] <file>`** - Read the last N lines of a file  
- **Parameters**:
  - `n` (optional): `Integer` - Number of lines, defaults to 10  
  - `file` (required): `String` - File path  
- **Returns**: `List[String]` - The last N lines of the file  

## Usage Examples

### Basic File Operations
```bash
# Read a file
Fs.read("config.txt")

# Write to a file
Fs.write("output.txt", "Hello, World!")

# Append content
Fs.append("log.txt", "New log entry\n")
```

### Directory Operations
```bash
# List current directory
Fs.ls()

# List specified directory in detail
Fs.ls("-l", "/home/user")

# Create a directory
Fs.mkdir("~/projects/new-project")

# Get system directories
Fs.dirs()
```

### File Management
```bash
# Copy a file
Fs.cp("source.txt", "backup.txt")

# Move a file
Fs.mv("old-name.txt", "new-name.txt")

# Delete a file
Fs.rm("temp-file.txt")
```

### Path Operations
```bash
# Join paths
Fs.join("~", "Documents", "file.txt")

# Get file name
Fs.base_name("/path/to/file.txt")  # Returns "file.txt"

# Normalize path
Fs.canon("../relative/path")
```

## Notes

The Fs module provides complete file system operation capabilities, with all path operations supporting home directory expansion (`~`) and relative path handling. The file reading and writing functions can automatically handle text and binary content, offering a flexible file operation interface. Parameter type descriptions indicate that `<>` denotes required parameters, `[]` denotes optional parameters, and `...` denotes variable parameters.
