---
title: Pipes and Errors
date: 2025-06-11 19:16:45
highlight: true
weight: 70
tags:
 - syntax
categories:
 - wiki
 - syntax
---

> Pipes, Redirection, Errors, Logs

## IX. Pipes and Redirection

### Pipes
1. Introduction to Pipes

Lumesh uses the same pipe symbol as bash, but it is more powerful:

- Smart Pipe `|`
    Automatically determines the appropriate behavior and can transmit structured data.

    + Left Side: Can automatically read from the result of operations or standard output;
      > *Reading Principle*:
      > For functions, built-in commands, and operations, read from the structured data channel.
      > For third-party system commands, read from standard output.

    + Right Side:
      > *Output Principle*:
      > If it is a third-party command, the data is passed as standard input.
      > If it is a function, the data is passed as the last parameter.

|   Data          |  Functions, Operations, Built-in Commands  |     Third-party Commands       |
|-----------------|--------------------------------------------|--------------------------------|
|   Input (Left)  |   Reads from the structured data channel   |    Reads from standard output   |
|   Output (Right) |   Outputs to the last parameter            |    Outputs to standard input    |


- Position Pipe `|_`
    Forces the use of positional parameters in the pipe, directing the pipe to a specified position in the right-side function, using `_` as a placeholder. If not specified, it appends to the end of the parameters.
    In most cases, manual specification is unnecessary. Using `|` is sufficient.
    However, if the right-side command cannot read standard input or requires specified parameter positions, this pipe must be used.

  ```bash
  3 | print a _ b               # Prints result: a 3 b
  ```

- Loop Dispatch Pipe `|>`
    Used to loop dispatch tasks from the left-side list to the right-side command.
    Also supports `_` placeholders.

  ```bash
    0...8 |> print lineNo          # Will print 8 lines
    ls -1 *.txt |> cp _ /tmp/      # Will copy the listed files
  ```

- PTY Pipe `|^`
    Forces the right-side command to use PTY mode.
    Some programs require complete terminal control permissions to function correctly, thus requiring PTY mode.
    The smart pipe maintains a list of such programs, so generally, there is no need to force PTY mode, but if you find a program not functioning correctly, you can try forcing PTY mode.

2. Basic Usage of Pipes

Traditional bash pipes, to maintain compatibility with more commands, can only handle byte streams. Byte streams are text data output by third-party commands, which the shell dispatches to the next program for processing. This mode greatly facilitates data transmission between different programs.

In fact, structured pipes are more efficient because they eliminate the need to convert plain text into structured data; some can even save time interacting with input/output devices.

**Structured Pipes are More Efficient**
For example:
  ```bash
  # -- This is a text stream pipe --
  echo 3+5 | bat              # This is a third-party command, and requires the pipe to read from standard output, resulting in double inefficiency

  # -- This is a structured pipe -- *Recommended usage*
  3+5 | bat                   # The operation result is directly sent to the next program

  # -- This is incorrect usage --
  print 3+5 | bat             # The print statement outputs to standard output, while also passing None as the operation result; bat captures the None result from the print statement

  # -- This is a structured pipe --
  tap 3+5 | bat               # The tap statement prints to standard output while passing the result down, bat correctly captures this operation result
  3+5 | tap | bat             # Equivalent to the previous statement
  ```
From the examples above, we can see:
  + If the operation result does not need to be printed and only needs to be passed down, use the pipe directly.
  + If the operation result needs to be printed and passed down, use tap before the pipe.
  + If the operation result is printed and does not need to be passed down, use print.
  + Avoid using echo unless advanced options like echo -e are needed.

3. Advanced Usage of Pipes

- **Filtering**
  ```bash
  # Filter data by size and display specified columns:
  fs.ls -l | where(size > 5K) | select(name,size,modified)

  # Output
  +--------------------------------------+
  | MODIFIED          NAME          SIZE |
  +======================================+
  | 2025-06-02 06:26  Cargo.lock    46K  |
  | 2025-06-02 04:40  CHANGELOG.md  9K   |
  +--------------------------------------+

  ```

- **Sorting**
  ```bash
  # Filter data by time and sort by specified columns
  fs.ls -l | where( fs.diff('d',modified) > 3 ) | sort(size,name)

  # Output
  +-------------------------------------------------------+
  | MODE  MODIFIED          NAME          SIZE  TYPE      |
  +=======================================================+
  | 511   2025-03-29 05:58  target        11    symlink   |
  | 493   2025-04-06 12:21  benches       66    directory |
  | 493   2025-05-13 10:57  assets        102   directory |
  | 493   2025-03-23 11:58  target_       128   directory |
  | 420   2025-03-23 05:32  LICENSE       1K    file      |
  | 420   2025-05-29 12:57  README-cn.md  4K    file      |
  | 420   2025-05-29 12:57  README.md     4K    file      |
  +-------------------------------------------------------+
  ```

- **Grouping**
  ```bash
  # Group by type
  fs.ls -l | group 'type'     # type is a function name, so the quotes cannot be omitted

  +-----------------------------------------------------------------+
  | KEY        VALUE                                                |
  +=================================================================+
  | directory  +--------------------------------------------------+ |
  |            | MODE  MODIFIED          NAME     SIZE  TYPE      | |
  |            +==================================================+ |
  |            | 493   2025-05-13 10:57  assets   102   directory | |
  |            | 493   2025-04-06 12:21  benches  66    directory | |
  |            | 493   2025-06-02 05:15  src      346   directory | |
  |            | 493   2025-06-03 04:32  wiki     528   directory | |
  |            +--------------------------------------------------+ |
  | file       +--------------------------------------------------+ |
  |            | MODE  MODIFIED          NAME          SIZE  TYPE | |
  |            +==================================================+ |
  |            | 420   2025-03-23 05:32  LICENSE       1K    file | |
  |            | 420   2025-05-29 12:57  README.md     4K    file | |
  |            | 420   2025-06-02 06:26  Cargo.lock    46K   file | |
  |            | 420   2025-06-02 04:40  CHANGELOG.md  9K    file | |
  |            | 420   2025-06-02 06:26  Cargo.toml    2K    file | |
  |            +--------------------------------------------------+ |
  | symlink    +-----------------------------------------------+    |
  |            | MODE  MODIFIED          NAME    SIZE  TYPE    |    |
  |            +===============================================+    |
  |            | 511   2025-03-29 05:58  target  11    symlink |    |
  |            +-----------------------------------------------+    |
  +-----------------------------------------------------------------+

  ```

- **Compatible with Third-party System Commands**
> You can use From.cmd or Into.table or chained calls .table() to convert text into structured data. It can accept a sequence of column names as parameters.

> However, when comparing data, you need to manually convert types.

  ```bash
  ls -l --time-style=long-iso | .table() | where(int C4>1000)

  # Output
  +------------------------------------------------------------------+
  | C0          C1  C2   C3   C4     C5          C6     C7           |
  +==================================================================+
  | -rw-r--r--  1   tix  tix  10046  2025-06-02  12:40  CHANGELOG.md |
  | -rw-r--r--  1   tix  tix  47312  2025-06-02  14:26  Cargo.lock   |
  | -rw-r--r--  1   tix  tix  2226   2025-06-02  14:26  Cargo.toml   |
  | -rw-r--r--  1   tix  tix  1075   2025-03-23  13:32  LICENSE      |
  | -rw-r--r--  1   tix  tix  4180   2025-05-29  20:57  README-cn.md |
  | -rw-r--r--  1   tix  tix  4333   2025-05-29  20:57  README.md    |
  +------------------------------------------------------------------+
  ```

### Redirection
- `<<` Read
- `>>` Append Output
- `>!` Overwrite Output
`1 + 2 >> result.txt`

*Error Redirection: Combined with Error Handling Symbols*
For specific usage, please refer to the error handling section.

| Redirection Type          | Lume                  | Bash                    |
|---------------------------|-----------------------|-------------------------|
| Standard Output, Append    | cmd >> out.txt        | cmd  >> out.txt         |
| Standard Output, Overwrite  | cmd >! out.txt        | cmd  > out.txt          |
| Error Redirect to Standard Output |  cmd ?> >> out.log    | command 2>&1 >> out.log |
| Separate Error Output      |  cmd ?? >> out.log    | cmd 2>> out.log         |
| Merge Standard and Error Output |  cmd ?+ >> out.log    | cmd >> out.log 2>&1     |


## X. Error Handling

### Error Capture: `?:`
  A statement followed by `?: expr`,

  - Typically, `expr` can be a lambda function that accepts an Error object. This Error object is a Map object, and you can index specific properties using `.` or `@` or `[]`.
      This object contains three properties:
      > + `code`
      > + `msg`
      > + `expr`

 - If `expr` is of a regular type, it provides a default value in case of an exception.

### Error Ignoring: `?.`
  Equivalent to `?: {}`

### Error Printing: `?+`, `??`, `?>`
  - Print error to standard output: `?+`
  Equivalent to `?: e -> echo e.msg`

  - Print error to error output: `??`
  Equivalent to `?: e -> eprint e.msg`

  - Output error as operation result: `?>`
  Equivalent to `?: e -> e.msg`

### Error Termination: `?!`
  - Silently terminate the pipeline when encountering an error
  `fs.ls -l | ui.pick ?! |_ cp _ /tmp`
  > When the user cancels the selection, subsequent pipeline operations terminate upon encountering an error.

  *The above methods can apply to any statement or function at the end*.
  *Error handling symbols have a higher priority than pipes*.

  ```bash

  6 / 0 ?.    # ignore error
  6 / 0 ?+    # print error to stdout
  6 / 0 ??    # print error to stderr
  6 / 0 ?!    # use this error message as result

  let e = x -> echo x.code
  6 / 0 ?: e   # dealing with the error using a function/lambda

  # also functions could use error handling too.
  fn divide(x,y){
      x / y
  } ?: e

  ```
**Tips**
- Utilizing the characteristics of error handling symbols, you can also provide default values for failed calculations:

  ```bash
  let e = x -> 0
  let result = 6 / 0 ?: e
  echo result              # Outputs default value: 0

  # Equivalent to
  let result = 6 / 0 ?: 0
  echo result              # Outputs default value: 0
  ```

- Use error handling when defining functions
  ```bash
  let e = x -> {print x.code}

  fn div(a,b){
      a / b
  } ?: e

  div(3,0)                # the defined error handling will be executed here.
  ```
### Error Debugging:
- Error Messages
  Generally, observing the error message is sufficient to identify the problem.
For example:
  ```bash
  [PARSE FAILED]                        # Syntax parsing error
  unexpected syntax error: expect "{"
        ▏
      1 ▏ fn assert(actual, expected, test_name, test_count=0) {
      2 ▏     if actual != expected
        ▏                           ^
      3 ▏         print "[FAIL]" test_count test_name "| 实际：" actual "| 预期" expected
      4 ▏     } else {
      5 ▏         print "[PASS]" test_count test_name
        ▏
        ↳ at line 2, column 27


  > 0...8 x
  [ERROR]                                # Runtime error
  Message   [1]: type error, expected Symbol as command, found 0...8: List
  Expression[1]: 0...8  x

  SyntaxTree[1]:
  Cmd 〈0...8〉
  〖
    Symbol〈"x"〉
  〗
  ```


- `debug` Command
  For further debugging, you can use the debug command.

  ```bash
  # Simple data debugging:
  let c = 0..8
  debug c                     # Output: Range〈0..9,1〉

  # Complex statement debugging:
  let a := if a {3 + 5} else { print a is False}
  debug a

  # Output:
  if (Symbol〈"a"〉) {

    {

        Integer〈3〉
        +
        Integer〈5〉
    }

  }else{

    {

      Cmd 〈Symbol〈"print"〉〉
      〖
        Symbol〈"a"〉
        Symbol〈"is"〉
        Boolean〈false〉
      〗

    }

  }

  ```
- Log Module
  The use of the Log module is also beneficial for error debugging; please refer to the [Log Module Documentation](/doc/libs/log).
