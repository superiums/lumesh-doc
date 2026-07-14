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

*   Functional Call: `Console.func(arg0, arg1...)`
*   Command Call: `Console.func arg0 arg1...`

---

#### Function Classification and Detailed Description

##### I. Basic Terminal Information

1.  **`Console.width`**
    **Function**: Get the terminal width (number of columns)  
    **Parameters**: None  
    **Return Value**: `integer` (returns `none` on failure)  
    **Example**:

    ```bash
        Console.width()  # => 120
    ```

2.  **`Console.height`**
    **Function**: Get the terminal height (number of rows)  
    **Parameters**: None  
    **Return Value**: `integer` (returns `none` on failure)  
    **Example**:

    ```bash
        Console.height()  # => 40
    ```

---

##### II. Terminal Control

1.  **`Console.write`**
    **Function**: Output text at a specified position  
    **Parameters**:

    *   `x: integer` (column position)
    *   `y: integer` (row position)
    *   `text: string` (content to output)  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.write 10 5 "Hello"  # Output "Hello" at row 5, column 10
    ```

2.  **`Console.title`**
    **Function**: Set the terminal window title  
    **Parameters**: `title: string`  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.title "My App"
    ```

3.  **`Console.clear`**
    **Function**: Clear the terminal screen  
    **Parameters**: None  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.clear()
    ```

4.  **`Console.flush`**
    **Function**: Force refresh the output buffer  
    **Parameters**: None  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.flush()
    ```

---

##### III. Terminal Mode Control

1.  **`Console.mode_raw`**
    **Function**: Enable raw input mode (disable line buffering)  
    **Parameters**: None  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.mode_raw()
    ```

2.  **`Console.mode_normal`**
    **Function**: Disable raw input mode (enable line buffering)  
    **Parameters**: None  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.mode_normal()
    ```

3.  **`Console.screen_alternate`**
    **Function**: Enable alternate screen (preserve main screen content)  
    **Parameters**: None  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.screen_alternate()
    ```

4.  **`Console.screen_normal`**
    **Function**: Disable alternate screen (return to main screen)  
    **Parameters**: None  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.screen_normal()
    ```

---

##### IV. Cursor Control

1.  **`Console.cursor_to`**
    **Function**: Move the cursor to a specified position  
    **Parameters**:

    *   `x: integer` (column position)
    *   `y: integer` (row position)  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.cursor_to 10 5
    ```

2.  **Directional Movement**:

    | Function                     | Parameter          | Functionality   |
    |------------------------------|--------------------|------------------|
    | `Console.cursor_up()`        | `lines: integer`    | Move up N lines  |
    | `Console.cursor_down()`      | `lines: integer`    | Move down N lines|
    | `Console.cursor_left()`      | `cols: integer`     | Move left N columns |
    | `Console.cursor_right()`     | `cols: integer`     | Move right N columns |

    **Example**:

    ```bash
        Console.cursor_down 3  # Move down 3 lines
    ```

3.  **`Console.cursor_save`**
    **Function**: Save the current cursor position  
    **Parameters**: None  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.cursor_save()
    ```

4.  **`Console.cursor_restore`**
    **Function**: Restore the saved cursor position  
    **Parameters**: None  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.cursor_restore()
    ```

5.  **`Console.cursor_hide`**
    **Function**: Hide the cursor  
    **Parameters**: None  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.cursor_hide()
    ```

6.  **`Console.cursor_show`**
    **Function**: Show the cursor  
    **Parameters**: None  
    **Return Value**: `none`  
    **Example**:

    ```bash
        Console.cursor_show()
    ```

---

##### V. Keyboard Input

1.  **`Console.read_line`**
    **Function**: Read a line of input (displays input content)  
    **Parameters**: None  
    **Return Value**: `string`  
    **Example**:

    ```bash
        let input = Console.read_line()
    ```

2.  **`Console.read_password`**
    **Function**: Read a password (hides input content)  
    **Parameters**: None  
    **Return Value**: `string`  
    **Example**:

    ```bash
        let pwd = Console.read_password()
    ```

3.  **`Console.read_key`**
    **Function**: Read a single key  
    **Parameters**: None  
    **Return Value**: `string` (escape sequence for special keys)  
    **Example**:

    ```bash
        let key = Console.read_key()
    ```

4.  **Special Key Constants**:

    ```bash
        Console.keys.enter      # Enter key
        Console.keys.backspace  # Backspace key
        Console.keys.tab        # Tab key
        Console.keys.esc        # ESC key
        Console.keys.up         # Up arrow
        Console.keys.down       # Down arrow
        Console.keys.left       # Left arrow
        Console.keys.right      # Right arrow
        Console.keys.f1         # F1 function key
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
