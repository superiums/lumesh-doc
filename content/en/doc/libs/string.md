---
title: Lumesh String Module
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - string
categories:
 - wiki
 - libs
---

The String module provides a rich set of string manipulation functions, covering validation checks, substring operations, splitting operations, modification operations, formatting, styling, and advanced operations. All functions support pipeline operations and provide a unified error handling mechanism.

## Function Overview

| Function Category | Main Functions | Purpose |
|-------------------|----------------|--------|
| **Type Conversion** | `to_int`, `to_float`, `to_filesize`, `to_time`, `to_table` | Convert strings to other data types |
| **Basic Validation** | `is_empty`, `is_whitespace`, `is_alpha`, `is_numeric`, `is_lower`, `is_upper` | Check string properties |
| **Substring Checks** | `starts_with`, `ends_with`, `contains` | Check string containment relationships |
| **Splitting Operations** | `split`, `split_at`, `chars`, `words`, `lines`, `paragraphs`, `concat` | String splitting and joining |
| **Modification Operations** | `repeat`, `replace`, `substring`, `remove_prefix`, `remove_suffix`, `trim` | Modify string content |
| **Case Conversion** | `to_lower`, `to_upper`, `to_title` | Convert case formats |
| **Formatting** | `pad_start`, `pad_end`, `center`, `wrap`, `format` | String formatting and layout |
| **Styling** | `bold`, `color`,`color_bg`, `green`, `underline`, `strip` | Terminal styling and colors |
| **Advanced Operations** | `caesar`, `get_width`, `grep` | Encryption, measurement, and searching |

## Type Conversion Functions

These functions convert strings to other data types:

**`to_int <value>`** - Convert a string to an integer
- **Parameters**: `value` (required): `String` - The string to convert
- **Returns**: `Integer` - The converted integer
- **Example**: `String.to_int "123"` returns `123`

**`to_float <value>`** - Convert a string to a float
- **Parameters**: `value` (required): `String` - The string to convert
- **Returns**: `Float` - The converted float

**`to_filesize <size_str>`** - Parse a file size string into bytes
- **Parameters**: `size_str` (required): `String` - File size string (e.g., "1KB", "2MB")
- **Returns**: `Integer` - Byte count

**`to_time <datetime_str> [datetime_template]`** - Convert a string to a date-time
- **Parameters**:
  - `datetime_str` (required): `String` - Date-time string
  - `datetime_template` (optional): `String` - Date-time format template
- **Returns**: `DateTime` - Date-time object

**`to_table <command_output>`** - Convert third-party command output to a table
- **Parameters**: `command_output` (required): `String` - Command output string
- **Returns**: `List[Map]` - Table data

## String Validation Functions

### Basic Type Checks

**`is_empty <string>`** - Check if a string is empty
- **Parameters**: `string` (required): `String` - The string to check
- **Returns**: `Boolean` - Returns true if the string is empty
- **Example**: `String.is_empty ""` returns `true`

**`is_whitespace <string>`** - Check if a string is all whitespace characters
- **Parameters**: `string` (required): `String` - The string to check
- **Returns**: `Boolean` - Returns true if all characters are whitespace
- **Example**: `String.is_whitespace "   \t\n"` returns `true`

**`is_alpha <string>`** - Check if a string is all alphabetic characters
- **Parameters**: `string` (required): `String` - The string to check
- **Returns**: `Boolean` - Returns true if all characters are alphabetic
- **Example**: `String.is_alpha "Hello"` returns `true`

**`is_alphanumeric <string>`** - Check if a string is all alphanumeric characters
- **Parameters**: `string` (required): `String` - The string to check
- **Returns**: `Boolean` - Returns true if all characters are alphanumeric
- **Example**: `String.is_alphanumeric "Hello123"` returns `true`

**`is_numeric <string>`** - Check if a string is all numeric characters
- **Parameters**: `string` (required): `String` - The string to check
- **Returns**: `Boolean` - Returns true if all characters are numeric
- **Example**: `String.is_numeric "12345"` returns `true`

### Case Checks

**`is_lower <string>`** - Check if a string is all lowercase
**`is_upper <string>`** - Check if a string is all uppercase
**`is_title <string>`** - Check if a string is in title case
- **Parameters**: `string` (required): `String` - The string to check
- **Returns**: `Boolean` - Returns true if it matches the respective format

## Substring Check Functions

**`starts_with <substring> <string>`** - Check if a string starts with the specified substring
- **Parameters**:
  - `substring` (required): `String` - The prefix to check
  - `string` (required): `String` - The target string
- **Returns**: `Boolean` - Returns true if it starts with the specified substring
- **Example**: `String.starts_with "Hello" "Hello World"` returns `true`

**`ends_with <substring> <string>`** - Check if a string ends with the specified substring
- **Parameters**:
  - `substring` (required): `String` - The suffix to check
  - `string` (required): `String` - The target string
- **Returns**: `Boolean` - Returns true if it ends with the specified substring

**`contains <substring> <string>`** - Check if a string contains the specified substring
- **Parameters**:
  - `substring` (required): `String` - The substring to search for
  - `string` (required): `String` - The target string
- **Returns**: `Boolean` - Returns true if it contains the specified substring

## String Splitting Functions

### Basic Splitting Operations

**`split [delimiter] <string>`** - Split a string by a delimiter
- **Parameters**:
  - `delimiter` (optional): `String` - The delimiter, defaults to the environment variable IFS or space
  - `string` (required): `String` - The string to split
- **Returns**: `List[String]` - List of split strings
- **Example**:
  - `String.split "," "a,b,c"` returns `["a", "b", "c"]`
  - `String.split "a b c"` returns `["a", "b", "c"]` (using default delimiter)

**`split_at <index> <string>`** - Split a string at a specified position
- **Parameters**:
  - `index` (required): `Integer` - Index position to split
  - `string` (required): `String` - The string to split
- **Returns**: `List[String]` - List containing two parts

### Special Splitting Operations

**`chars <string>`** - Split a string into a list of characters
- **Parameters**: `string` (required): `String` - The string to split
- **Returns**: `List[String]` - List of individual characters
- **Example**: `String.chars "Hello"` returns `["H", "e", "l", "l", "o"]`

**`words <string>`** - Split into a list of words by whitespace
- **Parameters**: `string` (required): `String` - The string to split
- **Returns**: `List[String]` - List of words

**`lines <string>`** - Split a string by lines
- **Parameters**: `string` (required): `String` - The string to split
- **Returns**: `List[String]` - List of lines
- **Example**: `String.lines "Line1\nLine2\nLine3"` returns `["Line1", "Line2", "Line3"]`

**`paragraphs <string>`** - Split a string into paragraphs (split by double newline)
- **Parameters**: `string` (required): `String` - The string to split
- **Returns**: `List[String]` - List of paragraphs

**`concat <string>...`** - Concatenate multiple strings
- **Parameters**: `string` (variable): `String...` - List of strings to concatenate
- **Returns**: `String` - The concatenated string

## String Modification Functions

### Case Conversion

**`to_lower <string>`** - Convert to lowercase
**`to_upper <string>`** - Convert to uppercase
**`to_title <string>`** - Convert to title case (capitalize the first letter of each word)
- **Parameters**: `string` (required): `String` - The string to convert
- **Returns**: `String` - The converted string

### Whitespace Handling

**`trim <string>`** - Remove leading and trailing whitespace
**`trim_start <string>`** - Remove leading whitespace
**`trim_end <string>`** - Remove trailing whitespace
- **Parameters**: `string` (required): `String` - The string to process
- **Returns**: `String` - The processed string

### Content Replacement and Extraction

**`replace <old> <new> <string>`** - Replace all matching substrings
- **Parameters**:
  - `old` (required): `String` - Substring to replace
  - `new` (required): `String` - Replacement content
  - `string` (required): `String` - Target string
- **Returns**: `String` - The string after replacement

**`substring <start> <end> <string>`** - Extract a substring
- **Parameters**:
  - `start` (required): `Integer` - Starting position (supports negative indexing)
  - `end` (optional): `Integer` - Ending position (supports negative indexing)
  - `string` (required): `String` - Target string
- **Returns**: `String` - The extracted substring

**`remove_prefix <prefix> <string>`** - Remove a prefix (if present)
**`remove_suffix <suffix> <string>`** - Remove a suffix (if present)
- **Parameters**:
  - `prefix/suffix` (required): `String` - The prefix/suffix to remove
  - `string` (required): `String` - Target string
- **Returns**: `String` - The string after removing the prefix/suffix

**`repeat <count> <string>`** - Repeat a string a specified number of times
- **Parameters**:
  - `count` (required): `Integer` - Number of repetitions (negative counts as 0)
  - `string` (required): `String` - The string to repeat
- **Returns**: `String` - The repeated string
- **Example**: `String.repeat 3 "Hi"` returns `"HiHiHi"`

## Formatting Functions

**`pad_start <length> [pad_char] <string>`** - Pad the string to the specified length from the start
- **Parameters**:
  - `length` (required): `Integer` - Target length
  - `pad_char` (optional): `String` - Padding character, defaults to space
  - `string` (required): `String` - The string to pad
- **Returns**: `String` - The padded string

**`pad_end <length> [pad_char] <string>`** - Pad the string to the specified length from the end
- **Parameters**:
  - `length` (required): `Integer` - Target length
  - `pad_char` (optional): `String` - Padding character, defaults to space
  - `string` (required): `String` - The string to pad
- **Returns**: `String` - The padded string

**`center <length> [pad_char] <string>`** - Center-align the string
- **Parameters**:
  - `length` (required): `Integer` - Target length
  - `pad_char` (optional): `String` - Padding character, defaults to space
  - `string` (required): `String` - The string to center
- **Returns**: `String` - The centered string

**`wrap <width> <string>`** - Wrap text at the specified width
- **Parameters**:
  - `width` (required): `Integer` - Maximum number of characters per line
  - `string` (required): `String` - The text to wrap
- **Returns**: `String` - The wrapped text

**`format <format_string> <args>...`** - Format a string using {} placeholders
- **Parameters**:
  - `format_string` (required): `String` - Format string containing placeholders
  - `args` (variable): `Any...` - Parameters to insert
- **Returns**: `String` - The formatted string

## Styling Functions

### Text Styles

**`bold <string>`** - Apply bold style
**`faint <string>`** - Apply faint style
**`italics <string>`** - Apply italic style
**`underline <string>`** - Apply underline style
**`blink <string>`** - Apply blinking effect
**`invert <string>`** - Invert foreground/background colors
**`strike <string>`** - Apply strikethrough style
- **Parameters**: `string` (required): `String` - The string to style
- **Returns**: `String` - The styled string

### Color Functions

#### 8-bit Colors
**Standard Colors**: `black`, `red`, `green`, `yellow`, `blue`, `magenta`, `cyan`, `white`
**Dark Versions**: `dark_black`, `dark_red`, `dark_green`, `dark_yellow`, `dark_blue`, `dark_magenta`, `dark_cyan`, `dark_white`

- **Parameters**: `string` (required): `String` - The string to be colored
- **Returns**: `String` - The string after applying the color

#### 256 Colors
`color256 <color_spec> <text>` - font color
- **Parameters**: `color_spec` (required): `Integer(0-255)` - The color specification for the string
- **Parameters**: `text` (required): `String` - The string to be colored
- **Returns**: `String` - The string after applying the color


`color256_bg <color_spec> <text>` - background color
- _same as above_

#### True Color
`color <color_name|hex_color|r,g,b> <text>` - font color
- **Parameters**:
  `color_name`: `String` - The name of the color, e.g., 'olive'
  `hex_color`: `String` - The hexadecimal color code, e.g., '#ff0000'
  `r,g,b`: `Integer,Integer,Integer` - RGB color values, e.g., 255,0,0
  `color_name`: `String` - The name of the color

- **Parameters**: `text` (required): `String` - The string to be colored
- **Returns**: `String` - The string after applying the color


`color_bg <color_name|hex_color|r,g,b> <text>` - font color
- _same as above_



`colors [skip_colorize?]` - List available colors
- **Parameters**: `skip_colorize`: `boolean` - Whether to skip coloring the color cards
- **Returns**: `Map` - A list of available colors


## Advanced Operation Functions

**`caesar <shift> <string>`** - Caesar cipher encryption
- **Parameters**:
  - `shift` (required): `Integer` - Shift amount, defaults to 13 (ROT13)
  - `string` (required): `String` - The string to encrypt
- **Returns**: `String` - The encrypted string
- **Description**: Only encrypts ASCII letters; other characters remain unchanged

**`get_width <string>`** - Get the display width of a string
- **Parameters**: `string` (required): `String` - The string to measure
- **Returns**: `Integer` - Maximum line width of the string
- **Description**: For multi-line strings, returns the width of the longest line

**`grep <substring> <string>`** - Find lines containing a substring
- **Parameters**:
  - `substring` (required): `String` - The substring to search for
  - `string` (required): `String` - The text to search
- **Returns**: `List[String]` - List of lines containing the substring

**`pprint [headers] <string>`** - Automatically convert to a table and pretty print

## Usage Examples

### Pipeline Operation Examples
```bash
# Text data processing pipeline
"Hello World" | String.to_upper() | String.replace "WORLD" "UNIVERSE"
# Result: "HELLO UNIVERSE"

# Analyze text content
"The quick brown fox" | String.words() | List.map(String.to_upper)
# Result: ["THE", "QUICK", "BROWN", "FOX"]

# Multi-step text processing
"  Hello,   World!  " | String.trim() | String.split "," | List.map(String.trim)
# Result: ["Hello", "World!"]
```

### Styling and Formatting Examples
```bash
# Apply styles
"Important" | String.bold() | String.red()
# Result: Bold red text

# Format text
String.center 20 "*" "Title"
# Result: "*******Title********"
```

## Notes

The String module provides comprehensive string processing capabilities, from basic validation and operations to advanced formatting and styling functions. All functions are optimized to support Unicode characters and provide consistent error handling. Parameter type descriptions indicate that `<>` denotes required parameters, `[]` denotes optional parameters, and `...` denotes variable parameters.

In practical use, chained calls are supported, such as:
```bash
"Important".bold().red()
```
In the examples, type names are used for clarity.
