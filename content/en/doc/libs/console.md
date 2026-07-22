---
title: Lumesh Console Module  
date: 2025-06-11 19:16:45  
highlight: true  
tags:  
 - libs  
 - console  
categories:  
 - wiki  
 - libs  
---

**Module Path**: `console`  
**Function Description**: Provides terminal control, cursor operations, keyboard input, and other console interaction functionalities. Supports two calling methods:

*   Functional Call: `console.func(arg0, arg1...)`
*   Command Call: `console.func arg0 arg1...`

---

#### Function Classification and Detailed Description

##### I. Basic Terminal Information

1.  **`console.width`**
    **Function**: Get the terminal width (number of columns)  
    **Parameters**: none  
    **Return Value**: `integer` (returns `none` on failure)  
    **Example**:

    ```bash
        console.width()  # => 120
    ```

2.  **`console.height`**
    **Function**: Get the terminal height (number of rows)  
    **Parameters**: none  
    **Return Value**: `integer` (returns `none` on failure)  
    **Example**:

    ```bash
        console.height()  # => 40
    ```

---

##### II. Terminal Control

1.  **`console.write`**
    **Function**: Output text at a specified position  
    **Parameters**:

    *   `x: integer` (column position)
    *   `y: integer` (row position)
    *   `text: string` (content to output)  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.write 10 5 "Hello"  # Output "Hello" at row 5, column 10
    ```

2.  **`console.title`**
    **Function**: Set the terminal window title  
    **Parameters**: `title: string`  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.title "My App"
    ```

3.  **`console.clear`**
    **Function**: Clear the terminal screen  
    **Parameters**: none  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.clear()
    ```

4.  **`console.flush`**
    **Function**: Force refresh the output buffer  
    **Parameters**: none  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.flush()
    ```

---

##### III. Terminal Mode Control

1.  **`console.mode_raw`**
    **Function**: Enable raw input mode (disable line buffering)  
    **Parameters**: none  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.mode_raw()
    ```

2.  **`console.mode_normal`**
    **Function**: Disable raw input mode (enable line buffering)  
    **Parameters**: none  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.mode_normal()
    ```

3.  **`console.screen_alternate`**
    **Function**: Enable alternate screen (preserve main screen content)  
    **Parameters**: none  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.screen_alternate()
    ```

4.  **`console.screen_normal`**
    **Function**: Disable alternate screen (return to main screen)  
    **Parameters**: none  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.screen_normal()
    ```

---

##### IV. Cursor Control

1.  **`console.cursor_to`**
    **Function**: Move the cursor to a specified position  
    **Parameters**:

    *   `x: integer` (column position)
    *   `y: integer` (row position)  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.cursor_to 10 5
    ```

2.  **Directional Movement**:

    | Function                     | Parameter          | Functionality   |
    |------------------------------|--------------------|------------------|
    | `console.cursor_up()`        | `lines: integer`    | Move up N lines  |
    | `console.cursor_down()`      | `lines: integer`    | Move down N lines|
    | `console.cursor_left()`      | `cols: integer`     | Move left N columns |
    | `console.cursor_right()`     | `cols: integer`     | Move right N columns |

    **Example**:

    ```bash
        console.cursor_down 3  # Move down 3 lines
    ```

3.  **`console.cursor_save`**
    **Function**: Save the current cursor position  
    **Parameters**: none  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.cursor_save()
    ```

4.  **`console.cursor_restore`**
    **Function**: Restore the saved cursor position  
    **Parameters**: none  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.cursor_restore()
    ```

5.  **`console.cursor_hide`**
    **Function**: Hide the cursor  
    **Parameters**: none  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.cursor_hide()
    ```

6.  **`console.cursor_show`**
    **Function**: Show the cursor  
    **Parameters**: none  
    **Return Value**: `none`  
    **Example**:

    ```bash
        console.cursor_show()
    ```

---

##### V. Keyboard Input

1.  **`console.read_line`**
    **Function**: Read a line of input (displays input content)  
    **Parameters**: none  
    **Return Value**: `string`  
    **Example**:

    ```bash
        let input = console.read_line()
    ```

2.  **`console.read_password`**
    **Function**: Read a password (hides input content)  
    **Parameters**: none  
    **Return Value**: `string`  
    **Example**:

    ```bash
        let pwd = console.read_password()
    ```

3.  **`console.read_key`**
    **Function**: Read a single key  
    **Parameters**: none  
    **Return Value**: `string` (escape sequence for special keys)  
    **Example**:

    ```bash
        let key = console.read_key()
    ```

4.  **Special Key Constants**:

    ```bash
        console.keys.enter      # Enter key
        console.keys.backspace  # Backspace key
        console.keys.tab        # Tab key
        console.keys.esc        # ESC key
        console.keys.up         # Up arrow
        console.keys.down       # Down arrow
        console.keys.left       # Left arrow
        console.keys.right      # Right arrow
        console.keys.f1         # F1 function key
        ...                             # Other function keys similarly
    ```

---

#### General Rules

1.  **Coordinate System**:

    *   The top-left corner is the origin (0, 0)
    *   The X-axis increases to the right (columns)
    *   The Y-axis increases downward (rows)

2.  **Error Handling**:

    *   Throws exceptions on parameter type errors
    *   Returns `none` on terminal operation failures

3.  **ANSI Escape Sequences**:
    All control functionalities are implemented using standard ANSI escape sequences.
