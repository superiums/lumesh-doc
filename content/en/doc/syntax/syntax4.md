---
title: Functions and Commands
date: 2025-06-11 19:16:45
highlight: true
weight: 60
tags:
 - syntax
categories:
 - wiki
 - syntax
---
> Functions and Commands

## VI. Functions

### **Function Definition with `fn`**
   - Defined using `fn`, supports *default parameters* and *rest parameter collection*.
       ```bash
       fn add(a,b,c=10,*d) {
          return  a + b + c + len(c)
       }

       # Equivalent to:
       fn add(a,b,c=10,*d) {
          a + b + c + len(c)
       }

       echo add(2, 3)  # Outputs 5
       echo add(2,3,4, 5)  # Outputs 10
       ```

### **Lambda Expressions**
   - Defined using `->`.

       ```bash
       let add = (x,y) -> x + y
       ```
  - Differences between lambda and regular functions:
      + Lambda does not support default parameters and return statements, nor does it support rest parameter collection.
      + Lambda *supports partial application of parameters*, returning subsequent lambdas.
  - Similarities:
      Both lambda and functions inherit the current environment variables and run in an isolated environment, without polluting the current environment.

### Function Calls
   Function names are followed immediately by `()` or `!` to execute the function.

  ```bash
  # Custom functions can be called like this
  add(3,5)
  # Or like this
  add! 3 5   # Note the need to add the ! suffix to distinguish from command calls.
  ```

   Distinguishing between function calls and command executions helps avoid name clashes, such as:
  ```bash
  # Test case 6: Function and command name clash
  fn ls() { echo "My ls" }
  ls -l                # Executes the system command ls
  ls()                 # Calls the function
  ls!                  # When using the ! suffix, calls the function; this is a syntax sugar that flattens the parentheses.
  ```

**Tips**:
- When the parameter is a literal lambda expression, please use the parentheses pattern; the flat pattern cannot be parsed.
- Similarly, when the parameter is a logical operation like &&, please use the parentheses pattern or the flat pattern after grouping with parentheses.

**Edge Cases**:
   - When function names conflict, the new definition will override the old one.
   - Calling a function with a mismatched number of parameters will throw an error. For example:
   `[ERROR] arguments mismatch for function `add`: expected 3, found 1`

### Chained Calls

  ```bash
  [1,3,5,6].sum()

  "hello world"
      .split(' ')
      .map(s -> s.to_upper())
      .join('-')
      .green()
  ```
For commonly used data types, chained calls can be used directly, including:
`String, List, Map, Time, Integer, Float, Range`

### Decorators
Decorators are a special type of higher-order function that can insert required logic before and after the execution of a specific function.

```bash
# Using a decorator in function definition
@timeit
fn test(n){
  for i in 0..n {
    n += i
  }
  print 'sum is' n
}

# Decorator function
fn timeit(){
    fn wrapper(func_t){
        (args_t) -> {
            let start = time.stamp_ms()                       # Logic before function execution
            func_t(args_t)                                  # Function execution
            let end = time.stamp_ms()                         # Logic after function execution
            print '>Time:'.green().bold() (end - start) 'ms'
        }
    }
}
```
**Please Note**
- All variable names defined by decorators applied to the same function cannot be duplicated.
For example, if a second decorator is to be applied to the `test` function above, that decorator cannot use the variable names `func_t` or `args_t`.
This is a trade-off for performance and convenience; for higher performance, we accept this small inconvenience.

## VII. Running System Commands

### Command Invocation

In Lumesh, you can conveniently run programs just like in other shells, such as:
  ```bash
  ls
  ls -l
  ```

- Multiple commands: if the preceding command fails, subsequent commands will not continue executing;
- Unless the error has been handled:

  ```bash
  ls '/0' ; ls -l         # The latter will not execute
  ls '/0' ?. ; ls -l      # The latter will execute
  ```
- Use the `^` suffix to force command mode
  ```bash
  let id = 5
  id^ -u              # Informs the system this is a command
  ```
- Empty parameter commands
  ```bash
  notepad.exe             # If not recognized as a command (e.g., on Windows, commands with extensions)
  notepad.exe _           # Pass an empty parameter to force recognition as a command
  ```

### Wildcard Expansion
In Lumesh, `~` directory expansion and `*` expansion are also supported:

  ```bash
  ls ~/**/*.md
  ```
*But does not support `{}` expansion as in bash.*

### Background Execution and Output Control Characters
- Like bash, use the `&` symbol to run programs in the background.
- Output control character `&`: we adopt a simpler way to suppress command output.
- Output control characters apply only to commands, not to functions.

  ```bash
  thunar &         # run in background, and shutdown stdout and stderr
  ls &-            # shutdown stdout
  ls /o &?         # shutdown stderr
  ls  /o '/' &+            # shutdown stdout and stderr

  ls  /o '/' &? | bat            # shutdown stderr and pipe stdout to next cmd.
  ```
Here is a comparison of syntax with bash:

| Task         |  Lumesh  | bash               |
|--------------|----------|--------------------|
| Background Run |   cmd &  |cmd &               |
| Close Std Output |   cmd &- |cmd 1> /dev/null    |
| Close Error Output |   cmd &? |cmd 2> /dev/null    |
| Close All Output |   cmd &. |cmd 2>&1 > /dev/null|
| Output Error to Std* |   cmd &+ |cmd 2>&1      |

[^1]: *todo

### Output Channels

- Standard Output (defined the same as in bash, can use `&-` to close standard output)
- Error Output (defined the same as in bash, can use `&?` to close standard output, can use error handling symbols to handle errors)
- Structured Data Channel (specific to Lumesh, can be disabled in configuration)

  Set `let LUME_PRINT_DIRECT= False` in the configuration file to disable the structured data channel.

  ```bash
  ❯ ls
  Documents  Downloads  dprint.json  typescript        # Standard output

  ❯ ls /x
  ls: cannot access '/x': No such file or directory    # Error output
  [ERROR] command `ls` failed: "exit status: 2"        # Lumesh error capture, target of error handling symbols

  ❯ 3 + 5
  8                           # Standard output
    >> [Integer] <<           # Structured channel data type hint
  8                           # Structured channel (operation result)
  ```

**Output Printing**

- `print` consumes the operation result and prints it to standard output.
- `tap` prints to standard output but retains the operation result.

  ```bash
  ❯ print 3+5
  8

  ❯ tap 3+5
  8

    >> [Integer] <<
  8
  ```

## VIII. Built-in Function Library

Lumesh includes a large number of practical function libraries to facilitate convenient functional programming, such as:
- **Collection Operations**: `list.reduce, list.map`
- **File System**: `fs.ls, fs.read, fs.write`
- **String Processing**: `string.split, string.join`, regex module, formatting module
- **Time Operations**: `fs.now, fs.format`
- **Data Conversion**: Into module, From module
- **Mathematical Calculations**: Complete mathematical function library
- **Logging**: Log module
- **UI Operations**: `ui.pick, ui.confirm`

You can view available modules and functions using the `help` command.
You can view specific module functions using the `help String` command.

> Built-in functions support three calling methods:
  ```bash
  string.red(msg)
  string.red msg
  string.red! msg
  ```
And also support chained calls and pipeline method calls:
  ```bash
  msg.red()
  msg | .red()
  ```

For detailed content, please continue reading:
 - [Built-in Lib Library](/doc/libs)
