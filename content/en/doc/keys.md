---
title: Lumesh key bindings
date: 2025-06-11 19:16:45
highlight: true
weight: 50
tags:
 - hotkey
categories:
 - wiki
 - hotkey
---

Default key bindings:

- Ctrl+ J      - Complete hint;
- Alt + J      - Accept one world of hint;
- Ctrl+ O      - Clear all input buffer;
- Alt + S      - insert sudo;

Additionally, custom bindings can be defined through the LUME_HOT_KEYS environment variable.

| **Function Category** | **Keystroke**                | **Description**                               |
|----------------------|------------------------------|-----------------------------------------------|
| **Cursor Movement**   | Home, Ctrl + A               | Move cursor to the beginning of the line      |
|                      | End, Ctrl + E                | Move cursor to the end of the line            |
|                      | Left, Ctrl + B               | Move cursor one character left                 |
|                      | Right, Ctrl + F              | Move cursor one character right                |
|                      | Ctrl + I, Tab                | Next completion                                |
|                      | Meta + B, Alt + Left         | Move left to the previous word                 |
|                      | Meta + F, Alt + Right        | Move right to the next word                    |
| **Text Editing**      | Ctrl + C                      | Interrupt the current command                  |
|                      | Ctrl + D, Del                | Delete the character under the cursor (if line is not empty) |
|                      | Ctrl + H, Backspace          | Delete the character before the cursor         |
|                      | Ctrl + W, Meta + Backspace   | Delete the word leading up to the cursor       |
|                      | Meta + D                     | Delete the next word                           |
|                      | Ctrl + U                      | Delete from the start of the line to the cursor |
|                      | Ctrl + K                      | Delete from the cursor to the end of the line  |
|                      | Ctrl + Y, Meta + Y           | Paste the previously deleted content            |
| **Command Control**   | Ctrl + Z                      | Suspend the current command                     |
|                      | Ctrl + L                      | Clear the screen                               |
|                      | Ctrl + R                      | Reverse search history commands                 |
|                      | Ctrl + P                      | Previous command                                |
|                      | Ctrl + N                      | Next command                                    |
|                      | Ctrl + J, Ctrl + M, Enter    | Finish line entry                              |
|                      | Meta + <                     | Move to the first entry in history             |
|                      | Meta + >                     | Move to the last entry in history              |
| **Undo and Redo**     | Ctrl + _                      | Undo the last operation                         |
|                      | Ctrl + X Ctrl + U            | Undo the last operation                         |
| **Special Functions**  | Ctrl + V                      | Insert any special character                    |
|                      | Meta + C                     | Capitalize the first letter of the current word |
|                      | Meta + L                     | Lower-case the current word                     |
|                      | Meta + U                     | Upper-case the current word                     |
|                      | Ctrl + T                      | Transpose the two characters before and after the cursor |
|                      | Meta + T                     | Transpose the current word with the previous word |
|                      | Meta + 0, 1, ..., -          | Specify the digit to the argument, -
