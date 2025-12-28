---
title: Lumesh Regex Module  
date: 2025-07-13 19:16:45  
highlight: true  
tags:  
 - libs  
 - regex  
 - pattern  
categories:  
 - wiki  
 - libs  
---

The Regex module provides comprehensive regular expression operation capabilities, supporting pattern matching, searching, capturing group extraction, text splitting, and replacement operations. All functions are implemented based on the `regex-lite` library, providing efficient regular expression processing capabilities.

## Function Overview

| Function Category | Main Functions | Purpose |
|-------------------|----------------|--------|
| **Matching and Locating** | `find`, `find_all` | Find match positions and content |
| **Matching Validation** | `match` | Validate if the entire text matches the pattern |
| **Capturing Group Operations** | `capture`, `captures`, `capture_name` | Extract captured group content |
| **Text Processing** | `split`, `replace` | Text splitting and replacement |

## Matching and Locating Functions

**`find <pattern> <text>`** - Find the first match  
- **Parameters**:
  - `pattern` (required): `String|Regex` - Regular expression pattern  
  - `text` (required): `String` - Text to search  
- **Returns**: `Map|None` - Match information mapping, containing `start`, `end`, and `found` fields; returns None if not found  
- **Example**: `Regex.find(r'\d+', "abc123def")` returns `{start: 3, end: 6, found: "123"}`  

**`find_all <pattern> <text>`** - Find all matches  
- **Parameters**:
  - `pattern` (required): `String|Regex` - Regular expression pattern  
  - `text` (required): `String` - Text to search  
- **Returns**: `List[Map]` - List of all matches, each element contains `start`, `end`, and `found` fields  
- **Example**: `Regex.find_all(r'\d+', "abc123def456")` returns a list of all matched numbers  

## Matching Validation Functions

**`match <pattern> <text>`** - Check if the entire text matches the pattern  
- **Parameters**:
  - `pattern` (required): `String|Regex` - Regular expression pattern  
  - `text` (required): `String` - Text to validate  
- **Returns**: `Boolean` - Returns true if the entire text matches  

## Capturing Group Operation Functions

**`capture <pattern> <text>`** - Get the first matching capturing group  
- **Parameters**:
  - `pattern` (required): `String|Regex` - Regular expression containing capturing groups  
  - `text` (required): `String` - Text to match  
- **Returns**: `List|None` - List of captured groups, index 0 is the full match, subsequent indices are each capturing group  
- **Example**: `Regex.capture(r'(\d{4})-(\d{2})-(\d{2})', "2023-12-25")` returns `["2023-12-25", "2023", "12", "25"]`  

**`captures <pattern> <text>`** - Get all matching capturing groups  
- **Parameters**:
  - `pattern` (required): `String|Regex` - Regular expression containing capturing groups  
  - `text` (required): `String` - Text to match  
- **Returns**: `List[List]` - List of all matching capturing groups  

**`capture_name <pattern> <text>`** - Get named capturing groups  
- **Parameters**:
  - `pattern` (required): `String|Regex` - Regular expression containing named capturing groups  
  - `text` (required): `String` - Text to match  
- **Returns**: `Map|None` - Mapping of named capturing groups, with keys as group names and values as matched content  
- **Example**: `Regex.capture_name(r'(?P<year>\d{4})-(?P<month>\d{2})', "2023-12")` returns `{year: "2023", month: "12"}`  

## Text Processing Functions

**`split <pattern> <text>`** - Split text by regular expression  
- **Parameters**:
  - `pattern` (required): `String|Regex` - Splitting pattern  
  - `text` (required): `String` - Text to split  
- **Returns**: `List[String]` - List of split strings  
- **Example**: `Regex.split(r'\s+', "hello   world  test")` returns `["hello", "world", "test"]`  

**`replace <pattern> <replacement> <text>`** - Replace all matches  
- **Parameters**:
  - `pattern` (required): `String|Regex` - Matching pattern  
  - `replacement` (required): `String` - Replacement content  
  - `text` (required): `String` - Target text  
- **Returns**: `String` - Text after replacement  
- **Example**: `Regex.replace(r'\d+', "X", "abc123def456")` returns `"abcXdefX"`  

## Parameter Handling Mechanism

The functions in the Regex module support flexible parameter type handling. They can process different combinations of parameters:

- Support for both `Regex` type and `String` type pattern parameters
- Automatically compiles strings into regular expressions
- Provides detailed error messages

## Usage Examples

### Basic Matching Operations
```bash
# Find numbers
Regex.find(r'\d+', "Price: $123.45")
# Returns: {start: 8, end: 11, found: "123"}

# Validate email format
Regex.match(r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$', "user@example.com")
# Returns: true
```

### Capturing Group Operations
```bash
# Parse date
Regex.capture(r'(\d{4})-(\d{2})-(\d{2})', "Today is 2023-12-25")
# Returns: ["2023-12-25", "2023", "12", "25"]

# Using named capturing groups
Regex.capture_name(r'(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})', "2023-12-25")
# Returns: {year: "2023", month: "12", day: "25"}
```

### Text Processing
```bash
# Split text
Regex.split(r'[,;]\s*', "apple, banana; cherry")
# Returns: ["apple", "banana", "cherry"]

# Replacement operation
Regex.replace(r'\b\w+@\w+\.\w+\b', "[EMAIL]", "Contact us at support@example.com")
# Returns: "Contact us at [EMAIL]"
```

### Pipeline Operation Examples
```bash
# Extract all numbers and sum them
"Price: $123, Tax: $45, Total: $168" | Regex.find_all(r'\d+') | List.map((m) -> Into.int(m.found)) | List.sum()
# Result: 336

# Clean and format text
"  Hello,   World!  " | Regex.replace(r'\s+', " ") | String.trim()
# Result: "Hello, World!"
```

## Notes

The Regex module is based on the `regex-lite` library, providing efficient regular expression processing capabilities. All functions support both string and `Regex` type pattern parameters, automatically handling type conversions. The capturing group functionality is particularly powerful, supporting both positional and named captures. Parameter type descriptions indicate that `<>` denotes required parameters and `[]` denotes optional parameters.

It is recommended to use raw strings (e.g., `r'pattern'`) to avoid the complexity of escape characters.

In practical use, chained calls are supported, such as:
```bash
let rg = r'[,;]\s*'
rg.split("apple, banana; cherry")
```
In the examples, type names are used for clarity.
