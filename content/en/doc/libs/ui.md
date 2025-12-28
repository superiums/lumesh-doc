---
title: Lumesh UI Module  
date: 2025-06-23 19:20:45  
highlight: true  
tags:  
 - libs  
 - ui  
categories:  
 - wiki  
 - libs  
---

## Overview
The `ui` module provides a set of interactive command-line interface functions that support user input of various data types (integers, floats, text, passwords, etc.) as well as the ability to select options. The calling format can use `Ui.function_name(arg1, arg2)` or space-separated parameters `Ui.function_name arg1 arg2`.

## View Help
```bash
help ui
```

## Function List
| Function Name | Description                   | Parameter Format                          | Return Type    |
|----------------|------------------------------|-------------------------------------------|-----------------|
| int            | Read integer input           | `<prompt message>`                        | Integer         |
| float          | Read float input             | `<prompt message>`                        | Float           |
| text           | Read text input              | `<prompt message>`                        | String          |
| passwd         | Read password input          | `<prompt message> [confirm required]`    | String          |
| confirm        | User confirmation (yes/no)   | `<prompt message>`                        | Boolean         |
| pick           | Single choice (select one option) | `[prompt or config] <options list/string>` | Any type        |
| multi_pick     | Multiple choice (select multiple options) | `[prompt or config] <options list/string>` | List            |

---

## Input Function Detailed Description

### 1. int - Read Integer Input
Get integer input from the user and validate its validity.

**Syntax:**
```bash
Ui.int "Prompt message"
```

**Example:**
```bash
; Read user age
let age = (Ui.int "Please enter your age:")
```

---

### 2. float - Read Float Input
Get float input from the user and validate its validity.

**Syntax:**
```bash
Ui.float "Prompt message"
```

**Example:**
```bash
; Read product price
let price = Ui.float "Please enter the product price:"
```

---

### 3. text - Read Text Input
Get a line of text input from the user.

**Syntax:**
```bash
Ui.text "Prompt message"
```

**Example:**
```bash
# Read username
let name = Ui.text("Please enter your name:")
```

---

### 4. passwd - Read Password Input
Securely read password input, with an option to confirm the password.

**Syntax:**
```bash
Ui.passwd "Prompt message" [confirm?]
```

**Parameters:**
- `confirm?` (optional): Boolean, defaults to true
  - `true`: Requires password confirmation (input twice)
  - `false`: Does not require confirmation (input once)

**Example:**
```bash
# Read password (requires confirmation)
let pwd1 = (Ui.passwd "Set password:" True)

# Read password (no confirmation)
let pwd2 = (Ui.passwd "Please enter password:" false)
```

---

### 5. confirm - User Confirmation
Ask the user a yes/no question and return a boolean value.

**Syntax:**
```bash
Ui.confirm "Prompt message"
```

**Example:**
```bash
# Confirm whether to continue operation
if (Ui.confirm "Do you want to continue the operation?") {
    # Code to continue execution
}
```

---

## Selection Function Detailed Description

### 6. pick - Single Choice
Allow the user to select one option from multiple choices.

**Syntax:**
```bash
Ui.pick [config|prompt] options list
```

**Parameter Format:**
- **Parameter 1 (optional): Configuration object or prompt string**
  - Type: `Map` or `String`
  - Supported configuration items:
    - `msg`: Prompt message (string)
    - `page_size`: Number of options displayed per page (integer)
    - `starting_cursor`: Initial cursor position (integer)
- **Options Data Source (choose one):**
  1. **List Expression**: `list("Option1" "Option2" ...)`
  2. **Multi-line String**: `"Option1\nOption2\n..."`
  3. **Multiple Parameter Form**: Directly pass multiple option expressions `Ui.pick Option1 Option2 ...`

**Example**:

1. Simple single choice:
```bash
let color = (Ui.pick "Choose a color:" ["Red", "Green", "Blue"])
```

2. Using string option source:
```bash
let fruits = "Apple\nBanana\nOrange"
let choice = (Ui.pick "Choose your favorite fruit:" fruits)
```

3. Using detailed configuration:
```bash
let oss = ["Windows", "Linux", "macOS"]
let choice = Ui.pick({
    msg: "Please select an operating system:",
    page_size: 5,
    starting_cursor: 1,
    }, oss)
```

### 7. multi_pick - Multiple Choice
Allow the user to select multiple options from a list.

**Syntax:**
```bash
Ui.multi_pick [config|prompt] options list
```

**Parameter Format:**
- **Parameter 1 (optional): Configuration object or prompt string**
  - Type: `Map` or `String`
  - Supported configuration items:
    - `msg`: Prompt message (string)
    - `page_size`: Number of options displayed per page (integer)
    - `starting_cursor`: Initial cursor position (integer)
- **Options Data Source (choose one):**
  1. **List Expression**: `list("Option1" "Option2" ...)`
  2. **Multi-line String**: `"Option1\nOption2\n..."`
  3. **Multiple Parameter Form**: Directly pass multiple option expressions `Ui.multi_pick Option1 Option2 ...`

**Example**:

```bash
Fs.ls -l | Ui.multi_pick "Which files do you want to delete?"

let oss = ["Windows", "Linux", "macOS", "Android", "Nokia"]
let choice = Ui.multi_pick({
    msg: "Which operating systems have you used:",
    page_size: 5,
    starting_cursor: 1,
    }, oss)
```
