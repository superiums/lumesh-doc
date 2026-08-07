---
title: "Syntax: Functions and Commands"
date: 2025-06-11 19:16:45
highlight: true
weight: 60
tags:
 - syntax
categories:
 - wiki
 - syntax
---
> Functions, Commands

## VI. Functions

### **fn Function Definition**
   - Define using `fn`, supports *default parameters*, *remaining parameter collection*, supports decorators.
       ```bash
       fn add(a,b,c=10,*d) {
          return  a + b + c + len(c)
       }

       # equivalent to:
       fn add(a,b,c=10,*d) {
          a + b + c + len(c)
       }

       echo add(2, 3)  # outputs 5
       echo add(2,3,4, 5)  # outputs 10
       ```


### **Lambda Expression**
   - Define using `->`.

       ```bash
       let add = (x,y) -> x + y
       ```
  - Differences between lambda and regular functions:
      + lambda doesn't support default parameters and return statements, doesn't support remaining parameter collection.
      + lambda doesn't support decorators.
      + lambda *supports partial application*, returns subsequent lambda
      + lambda supports closure capturing.

  - Same things:
      lambda and functions both inherit current environment variables, run in isolated environment, won't pollute current environment.


### Function Call
   Function name directly followed by `()` or `!` to execute.

  ```bash
  # custom function can be called like this
  add(3,5)
  # or like this
  add! 3 5   # note need to add ! suffix, to distinguish from command call.
  ```

   Distinguishing function call from command execution helps avoid same-name function overriding command, for example:
  ```bash
  # test case 6: function and command same name override
  fn ls() { print "Lume" }
  ls -l                # execute system command ls
  ls()                 # call function
  ls!                  # when using ! suffix, call function, this is syntax sugar for flattening parentheses.
  ```

**Tips**:
- When parameter is literal lambda expression, use parenthesized mode, flat mode cannot parse.
- Similarly, when parameter is logical operators like &&, use parenthesized mode, or use parenthesized grouped flat mode.


**Edge Cases**:
   - When function name conflicts, new definition overrides old.
   - When calling function, argument count mismatch will error. Example:
   `[ERROR] arguments mismatch for function `add`: expected 3, found 1`

### Chained Call

  ```bash
  [1,3,5,6].sum()

  "hello world"
      .split(' ')
      .map(s -> s.upper()())
      .join('-')
      .green()
  ```
For common data types, can directly use chained call, including:
`string, list, map, time, integer, float, range`

### Decorators
Decorator is a special higher-order function that can insert needed logic before and after specific function execution.

```bash
# use decorator during function definition
@timeit
fn test(n){
  for i in 0..n {
    n += i
  }
  print 'sum is' n
}

# decorator func
fn timeit(){
    fn before(){
        let start = time.stamp_ms()
    }
    fn after(){
        let end = time.stamp_ms()
        print '>Time:'.green().bold() (end - start) 'ms'
    }
    [before,after]
}

```
**Decorator function must return [before,after] list, can use placeholder `_` if empty**

### Error Handling
Error capture uses operators like `?:` (see error capture section for more), it can capture during call, or set in function definition.
Example:

```bash
dosth() ?: e -> print e.code

fn test(){
  ...
} ?: e -> print e.msg
```

## VII. Running System Commands

### Command Invocation

In lumesh, you can conveniently run programs like in other shells, for example
  ```bash
  ls                  # only in CFM mode
  ls -l
  ```

- Multiple commands, if previous command fails, subsequent commands won't execute;
- Unless error already handled:

  ```bash
  ls '/0' ; ls -l         # latter won't execute
  ls '/0' ?. ; ls -l      # latter will execute
  ```
- Use `^` suffix to force bypass variable parsing
  ```bash
  let id = 5
  id^ -u              # tell system this is a command
  ```
- Empty argument command
  ```bash
  notepad.exe             # if not recognized as command (e.g. on Windows, command with extension)
  notepad.exe _           # pass empty argument, force recognize as command
  ```

### Wildcard Expansion
In lumesh, also supports `~` directory expansion and `*` expansion:

  ```bash
  ls ~/**/*.md
  ```
*But doesn't support bash's {} expansion*

### Background Execution and Output Control
- Like bash, use `&` symbol to run program in background.
- Output control `&`: we use more concise way to close command output.
- Output control only applies to commands, not functions.

  ```bash
  thunar &         # run in background, and shutdown stdout and stderr
  ls &-            # shutdown stdout
  ls /o &?         # shutdown stderr
  ls  /o '/' &.            # shutdown stdout and stderr

  ls  /o '/' &? | bat            # shutdown stderr and pipe stdout to next cmd.

  ```
Below is comparison with bash syntax:

| Task         |  lumesh  | bash               |
|--------------|----------|--------------------|
| Shutdown stdout |   cmd &- |cmd 1> /dev/null    |
| Shutdown stderr |   cmd &? |cmd 2> /dev/null    |
| Shutdown all output |   cmd &. |cmd 2>&1 > /dev/null|
| *Append stderr to stdout* |   cmd &+ |cmd 2>&1      |
| Background execution     |   cmd &  |cmd &               |


### Output Channels

- Standard Output (same definition as bash, can use `&-` to close standard output)
- Error Output (same definition as bash, can use `&?` to close standard output, can use error handling operators to handle error)
- Structured Data Channel (lumesh exclusive, can be closed in config)

  Set `let LUME_PRINT_DIRECT= false` in config file to close structured data channel

  ```bash
  ❯ ls
  Documents  Downloads  dprint.json  typescript        # standard output


  ❯ ls /x
  ls: cannot access '/x': No such file or directory    # error output
  [ERROR] command `ls` failed: "exit status: 2"        # Lumesh error capture, target of error handling operators

  ❯ rsync -av src/ dest/ &+ | grep "error"             # append error to standard output

  ❯ 3 + 5
  8                           # standard output
    >> [Integer] <<           # structured channel type hint
  8                           # structured channel (operation result)

  ```

**Output Printing**

- print consumes operation result, prints to standard output
- tap   prints to standard output, but preserves operation result

  ```bash
  ❯ print 3+5
  8

  ❯ tap 3+5
  8

    >> [Integer] <<
  8

  ```

## VIII. Built-in Function Libraries

Lumesh has many practical built-in function libraries for convenient functional programming, such as
- **Collection Operations**: `list.fold, list.map`
- **File System**: `fs.ls, fs.read, fs.write`
- **String Processing**: `string.split, string.join`, regex module, formatting module
- **Time Operations**: `fs.now, fs.format`
- **Data Conversion**: into module, from module
- **Math Calculations**: complete math function library
- **Logging**: log module
- **ui operations**: `ui.pick, ui.confirm`

Can view available modules and functions via `help` command.
Can view specific module functions via `help string` command.

> Built-in functions support three calling styles
  ```bash
  string.red(msg)
  string.red msg
  string.red! msg
  ```
and chained call and pipe method call:
  ```bash
  msg.red()
  msg | .red()
  ```

For detailed content, continue reading:
 - [Built-in Libs](/zh-cn/doc/libs/)
