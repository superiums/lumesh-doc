---
title: "Syntax: Pipes and Errors"
date: 2025-06-11 19:16:45
highlight: true
weight: 70
tags:
 - syntax
categories:
 - wiki
 - syntax
---

> Pipes, Redirection, Errors, Logging

## IX. Pipes and Redirection

### Pipes
1. Introduction to Pipes

Lumesh uses the same pipe operator as bash, but is more powerful:

- Smart Pipe `|`
    Automatically determines the appropriate behavior, can transmit structured data.

    + Left side: can automatically read from operation result or standard output;
      > *Reading Principle*:
      > For functions, built-in commands, and operations, read structured data channels.
      > For system third-party commands, read standard output.

    + Right side:
      > *Output Principle*:
      > If it's a third-party command, pass data as standard input
      > If it's a function, pass as *first* argument.

|   Data          |  Function, Operation, Built-in Command  |     Third-party Command       |
|-----------------|----------------------------------------|-------------------------------|
|   Input (Left)  |   Read structured data channel          |    Read standard output       |
|   Output (Right) |   Output to first argument             |    Output to standard input   |


  **Placeholder `_`**
  
    When piping to a function on the right at a specific position, use `_` as a placeholder. If not specified, it's inserted at the first argument position.
    In most cases, manual specification is not needed. Using `|` is sufficient.
    But if the right-side command cannot read standard input, or needs to specify argument position, use this pipe.

  ```bash
  2 | print 1 _ 3               # Output: 1 2 3
  ```

- Loop Dispatch Pipe `|>`
    Used to loop dispatch left-side list tasks to the right-side command.
    Also supports `_` placeholder.

  ```bash
    0...8 |> print lineNo          # This will print 8 lines
    ls -1 *.txt |> cp _ /tmp/            # This will copy the listed files
  ```


- PTY Pipe `|^`
    Forces right-side command to use PTY mode.
    Some programs require full terminal control permissions to work properly, thus requiring PTY mode.
    The smart pipe maintains a list of such programs, so generally no need to force PTY mode, but if you find a program not working properly, try forcing PTY mode.

2. Basic Pipe Usage

Traditional bash pipes, for compatibility with more commands, can only handle byte streams. Byte streams are text data output by third-party commands, passed to the next program via pipe. This mode greatly facilitates data transmission between different programs.

Actually structured pipes are more efficient, because they save the work of converting from plain text to structured data; even some can save the time dealing with input/output devices.

**Structured Pipes are More Efficient**
For example:
  ```bash
  # --This is a text stream pipe--
  echo 3+5 | bat              # This is a third-party command and needs to pipe from standard output, double efficiency reduction

  # --This is a structured pipe-- *Recommended Usage*
  3+5 | bat                   # Operation result directly passed to next program

  # --This is incorrect usage--
  print 3+5 | bat             # print statement prints to standard output, passing none as operation result, bat catches none (the operation result of print statement)

  # --This is a structured pipe--
  tap 3+5 | bat               # tap statement prints to standard output, while passing result downward, bat correctly catches this operation result
  3+5 | tap | bat             # Equivalent to previous line
  ```
From the examples above, we can see:
  + If operation result doesn't need printing, just pass it down, use pipe directly.
  + If operation result needs printing AND passing down, use tap then pipe.
  + If operation result is printed but doesn't need passing down, use print.
  + Should avoid using echo, unless you need echo -e or other advanced options.

3. Advanced Pipe Usage

- **Filtering**
  ```bash
  # Filter data by size and display specified columns:
  fs.ls -lh | where(size > 5K) | select(name,size,modified)

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
  fs.ls -lh | where( modified.diff(time.now(),'d') > 3 ) | .sort(size,name)

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
  fs.ls -lh | group 'type'     # type is a function name, so quotes cannot be omitted

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

- **Compatible Third-party Commands**
> Can use from.cmd or into.table or chained call .table() to convert text to structured data. Can accept column name sequences as parameters.

> But when comparing data, need to manually convert types.

  ```bash
  ls -l --time-style=long-iso | .to_table() | where(int C4>1000)

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
- `<<` Read Here
- `>>` Append Output
- `>!` Overwrite Output
`1 + 2 >> result.txt`

*Error Redirection: Combining with error handling operators*
Please refer to the Error Handling chapter for specific usage.

| Redirection Type               | Lume                  | Bash                    |
|--------------------------------|-----------------------|-------------------------|
| Standard Output, Append       | cmd >> out.txt        | cmd  >> out.txt         |
| Standard Output, Overwrite     | cmd >! out.txt        | cmd  > out.txt          |
| Error redirects to stdout      |  cmd ?> >> out.log    | command 2>&1 >> out.log |
| Error output only             |  cmd ?? >> out.log    | cmd 2>> out.log         |
| Merge stdout and stderr        |  cmd ?+ >> out.log    | cmd >> out.log 2>&1     |


## X. Error Handling

### Error Capture: `?:`
  Statement followed by `?: expr`,

  - Usually expr can be a lambda function, which can accept an Error object. This Error object is a Map object, can be indexed with `.` or `@` or `[]` to get specific properties.
      This object contains three properties:
      > + `code`
      > + `msg`
      > + `expr`

 - If expr is a regular type, it provides a default value when an exception occurs.

### Error Ignore: `?.`
  Equivalent to `?: {}`

### Error Print: `?+` `??` `?>`
  - Print error to stdout: `?+`
  Equivalent to `?: e -> echo e.msg`

  - Print error to stderr: `??`
  Equivalent to `?: e -> eprint e.msg`

  - Output error as operation result: `?>`
  Equivalent to `?: e -> e.msg`

### Error Terminate: `?!`
  - When encountering an error, silently terminate the pipeline
  `fs.ls -l | ui.pick ?! |_ cp _ /tmp`
  > When user cancels selection, error terminates subsequent pipeline operations.


  *The above methods can be applied to any statement or function end*.
  *Error handling operators have higher priority than pipes*.

  ```bash

  6 / 0 ?.    # ignore err
  6 / 0 ?+    # print err to stdout
  6 / 0 ??    # print err to stderr
  6 / 0 ?!    # use this err msg as result

  let e = x -> echo x.code
  6 /0 ?: e   # handle error with a function/lambda

  # also functions could use error handling.
  fn divide(x,y){
      x / y
  }?: e

  ```
**Tips**
- Leveraging error handling operator characteristics, can also provide default values when calculation fails:

  ```bash
  let e = x -> 0
  let result = 6 /0 ?: e
  echo result              # output default value: 0

  # equivalent to
  let result = 6 /0 ?: 0
  echo result              # output default value: 0
  ```

- In function definition, use error handling
  ```bash
  let e = x -> {print x.code}

  fn div(a,b){
      a / b
  } ?: e

  div(3,0)                # the defined error handling will be executed on every call.
  ```
### Error Debugging:
- Error Messages
  Generally, observing error messages is enough to identify the problem.
For example:
  ```bash
  [PARSE FAILED]                        # syntax parse error
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
  [ERROR]                                # runtime error
  Message   [1]: type error, expected Symbol as command, found 0...8: List
  Expression[1]: 0...8  x

  SyntaxTree[1]:
  Cmd 〈0...8〉
  〖
    Symbol〈"x"〉
  〗
  ```


- `debug` command
  For further debugging, can use `debug` and `ddebug`, `typeof`/`symof` commands.

  ```bash
  # Simple data debugging:
  let c = 0..8
  debug c                     # Output: Range〈0..9,1〉

  # Complex statement debugging:
  let a = fs.ls -l
  /home/tix |> debug a

  # Output:
  List
    Map
      mode:
        Integer〈511〉
      modified:
        DateTime〈2025-02-20T03:26:13.582549757〉
      name:
        String〈"Documents"〉
      size:
        FileSize〈FileSize { size: 21, unit: B }〉
      type:
        String〈"symlink"〉
  ,
    Map
      mode:
        Integer〈448〉
      modified:
        DateTime〈2026-01-31T03:57:48.270299425〉
      name:
        String〈"Downloads"〉
      size:
        FileSize〈FileSize { size: 0, unit: B }〉
      type:
        String〈"directory"〉

  ```
- Log module
  Using the Log module also helps with error debugging, please refer to [Log module documentation](/zh-cn/doc/libs/api/log) for details
