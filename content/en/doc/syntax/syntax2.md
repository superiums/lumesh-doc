---
title: Lumesh Syntax Manual 2
subtitle: Functions, Commands, Pipelines, Errors, Interaction
date: 2025-06-11 19:16:45
highlight: true
tags:
 - syntax
categories:
 - wiki
 - syntax
---

> Functions, Commands, Pipelines, Errors, Interaction

## VI. Functions

### **fn Function Definition**
   - Defined using `fn`, supports *default parameters* and *rest parameter collection*.
       ```bash
       fn add(a, b, c = 10, *d) {
          return a + b + c + len(c)
       }

       # Equivalent to:
       fn add(a, b, c = 10, *d) {
          a + b + c + len(c)
       }

       echo add(2, 3)  # Outputs 5
       echo add(2, 3, 4, 5)  # Outputs 10
       ```

### **Lambda Expressions**
   - Defined using `->`.

       ```bash
       let add = (x, y) -> x + y
       ```
  - Differences between lambda and regular functions:
      + Lambda does not support default parameters and return statements, nor does it support rest parameter collection.
      + Lambda *supports partial application of parameters*, returning subsequent lambdas.
  - Similarities:
      Both lambda and functions inherit the current environment variables and run in an isolated environment, without polluting the current environment.

### Function Calls
   Function names are followed by `()` or `!` to execute the function.

  ```bash
  # Custom functions can be called like this
  add(3, 5)
  # Or like this
  add! 3 5   # Note the need to add the ! suffix to distinguish from command calls.
  ```

   Distinguishing between function calls and command executions helps avoid command overrides by functions with the same name, such as:
  ```bash
  # Test Case 6: Function and command name conflict
  fn ls() { echo "My ls" }
  ls -l                # Executes the system command ls
  ls()                 # Calls the function
  ls!                  # When using the ! suffix, calls the function; this is a syntax sugar that flattens the parentheses.
  ```

**Tips**:
- When the parameter is a literal lambda expression, use the parentheses pattern; the flat pattern cannot be parsed.
- Similarly, when the parameter is a letter for logical operations like &&, use the parentheses pattern or the flat pattern after grouping with parentheses.

**Edge Cases**:
   - When function names conflict, the new definition will override the old one.
   - When calling a function, a mismatch in the number of parameters will throw an error. For example:
   `[ERROR] arguments mismatch for function `add`: expected 3, found 1`

### Chained Calls

  ```bash
  [1, 3, 5, 6].sum()

  "hello world"
      .split(' ')
      .map(s -> s.to_upper())
      .join('-')
      .green()
  ```
For commonly used data types, chained calls can be directly used, including:
`String, List, Map, Time, Integer, Float, Range`

### Decorators
Decorators are a special type of higher-order function that can insert required logic before and after the execution of a specific function.

```bash
# Using decorators during function definition
@timeit
fn test(n) {
  for i in 0..n {
    n += i
  }
  print 'sum is' n
}

# Decorator function
fn timeit() {
    fn wrapper(func_ti) {
        (args_ti) -> {
            let start = Time.stamp_ms()                       # Logic before function execution
            func_ti(args_ti)                                  # Function execution
            let end = Time.stamp_ms()                         # Logic after function execution
            print '>Time:'.green().bold() (end - start) 'ms'
        }
    }
}
```
**Please Note**
- Variables defined by all decorators applied to the same function cannot have the same name.
For example, if you want to apply a second decorator to the `test` function above, that decorator cannot use the variable names `func_ti` or `args_ti`.
This is a trade-off for performance and convenience; to achieve higher performance, we decided to accept this minor inconvenience.

## VII. Running System Commands

### Command Invocation

In Lumesh, you can conveniently run programs just like in other shells, such as:
  ```bash
  ls
  ls -l
  ```

- Multiple commands: If the preceding command fails, subsequent commands will not continue to execute;
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
- blank argument for command
  ```bash
  notepad.exe             # if not recognized as command(cmd with extentions in windows)
  notepad.exe _           # pass an empty argument to force a command recognize
  ```

### Wildcard Expansion
In Lumesh, `~` directory expansion and `*` expansion are also supported:

  ```bash
  ls ~/**/*.md
  ```
*But `{}` expansion as in bash is not supported.*

### Background Running and Output Control Characters
- Like bash, use the `&` symbol to run programs in the background.
- Output control character `&`: We adopt a more concise way to suppress command output.
- Output control characters apply only to commands, not to functions.

  ```bash
  thunar &         # run in background, and suppress stdout and stderr
  ls &-            # suppress stdout
  ls /o &?         # suppress stderr
  ls /o '/' &+     # suppress stdout and stderr

  ls /o '/' &? | bat            # suppress stderr and pipe stdout to the next command.
  ```

Here is a comparison with bash syntax:

| Task         |  lumesh  | bash               |
|--------------|----------|--------------------|
| Background Run |   cmd &  | cmd &               |
| Suppress Std Output |   cmd &- | cmd 1> /dev/null    |
| Suppress Error Output |   cmd &? | cmd 2> /dev/null    |
| Suppress All Output |   cmd &. | cmd 2>&1 > /dev/null|

### Output Channels

- Standard Output (defined the same as in bash, can use `&-` to suppress standard output)
- Error Output (defined the same as in bash, can use `&?` to suppress standard output, can use error handling characters to handle errors)
- Structured Data Channel (unique to Lumesh, can be disabled in configuration)

  Set `let LUME_PRINT_DIRECT = False` in the configuration file to disable the structured data channel.

  ```bash
  ❯ ls
  Documents  Downloads  dprint.json  typescript        # Standard output

  ❯ ls /x
  ls: cannot access '/x': No such file or directory    # Error output
  [ERROR] command `ls` failed: "exit status: 2"        # Lumesh error capture, target handled by error handling characters

  ❯ 3 + 5
  8                           # Standard output
    >> [Integer] <<           # Structured channel data type hint
  8                           # Structured channel (operation result)
  ```

**Output Printing**

- `print` consumes the operation result and prints it to standard output.
- `tap` prints to standard output while retaining the operation result.

  ```bash
  ❯ print 3 + 5
  8

  ❯ tap 3 + 5
  8

    >> [Integer] <<
  8

  ```

## VIII. Pipelines and Redirection

### Pipelines
1. Introduction to Pipelines

Lumesh uses the same pipeline symbol as bash, but it is more powerful:

- Smart Pipeline `|`
    Automatically determines the appropriate behavior and can transmit structured data.

    + Left Side: Can automatically read from operation results or standard output;
      > *Reading Principle*:
      > For functions, built-in commands, and operations, read from the structured data channel.
      > For system third-party commands, read from standard output.

    + Right Side:
      > *Output Principle*:
      > If it is a third-party command, the data is passed as standard input.
      > If it is a function, the data is passed as the last parameter.

|   Data          |  Functions, Operations, Built-in Commands  |     Third-party Commands       |
|-----------------|--------------------------------------------|--------------------------------|
|   Input (Left Side)  |   Reads from structured data channel   |    Reads from standard output   |
|   Output (Right Side) |   Outputs to the last parameter       |    Outputs to standard input     |

- Positional Pipeline `|_`
    Forces the use of positional pipelines, passing to specified parameter positions of the right-side function, using `_` as a placeholder. If not specified, it appends to the end of the parameters.
    In most cases, there is no need to specify manually. Using `|` is sufficient.
    However, if the right-side command cannot read standard input or requires specified parameter positions, this pipeline needs to be used.

  ```bash
  3 | print a _ b               # Prints result: a 3 b
  ```

- Loop Dispatch Pipeline `|>`
    Used to loop dispatch list tasks from the left side to the right-side command.
    Also supports `_` placeholders.

  ```bash
    0...8 |> print lineNo          # Will print 8 lines
    ls -1 *.txt |> cp _ /tmp/      # Will copy the listed files
  ```

- PTY Pipeline `|^`
    Forces the right-side command to use PTY mode.
    Some programs require complete terminal control permissions to function correctly, hence the need for PTY mode.
    The smart pipeline maintains a list of such programs, so generally, there is no need to force PTY mode. However, if you find that a program is not functioning correctly, you can try to force PTY mode.

2. Basic Usage of Pipelines

Traditional bash pipelines, to be compatible with more commands, can only handle byte streams. Byte streams are text data output by third-party commands, which the shell dispatches to the next program for processing. This greatly facilitates data transmission between different programs.

In fact, structured pipelines are more efficient because they eliminate the need to convert from plain text to structured data; some can even save time interacting with input and output devices.

**Structured Pipelines are More Efficient**
For example:
  ```bash
  # --This is a text stream pipeline--
  echo 3 + 5 | bat              # This is a third-party command, and requires the pipeline to read from standard output, efficiency is doubly reduced.

  # --This is a structured pipeline-- *Recommended usage*
  3 + 5 | bat                   # Operation result is directly passed to the next program.

  # --This is incorrect usage--
  print 3 + 5 | bat             # The print statement prints to standard output, while passing None as the operation result; bat captures the operation result of the print statement, which is None.

  # --This is a structured pipeline--
  tap 3 + 5 | bat               # The tap statement prints to standard output while passing the result down, bat correctly captures this operation result.
  3 + 5 | tap | bat             # Equivalent to the previous statement.
  ```
From the above examples, it can be seen that:
  + If the operation result does not need to be printed, it should be passed directly using a pipeline.
  + If the operation result needs to be printed and passed down, it should use tap before the pipeline.
  + If the operation result needs to be printed but does not need to be passed down, it should use print.
  + Avoid using echo unless advanced options like echo -e are needed.

3. Advanced Usage of Pipelines

- **Filtering**
  ```bash
  # Filter data by size and display specified columns:
  Fs.ls -l | where(size > 5K) | select(name, size, modified)

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
  Fs.ls -l | where(Fs.diff('d', modified) > 3) | sort(size, name)

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
  Fs.ls -l | group 'type'     # type is a function name, so quotes cannot be omitted

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

- **Compatible with System Third-party Commands**
> You can use parse.cmd or Into.table or chained calls .table() to convert text into structured data. It can accept a sequence of column names as parameters.

> However, when comparing data, type conversion needs to be done manually.

  ```bash
  ls -l --time-style=long-iso | .table() | where(int C4 > 1000)

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
- `>>` Append output
- `>!` Overwrite output
`1 + 2 >> result.txt`

*Error redirection: combined with error handling characters.*
For specific usage, please refer to the error handling section.

| Redirection Type          | Lume                  | Bash                    |
|---------------------------|-----------------------|-------------------------|
| Standard Output, Append    | cmd >> out.txt        | cmd  >> out.txt         |
| Standard Output, Overwrite  | cmd >! out.txt        | cmd  > out.txt          |
| Redirect Error to Standard Output |  cmd ?> >> out.log    | command 2>&1 >> out.log |
| Separate Error Output      |  cmd ?? >> out.log    | cmd 2>> out.log         |
| Merge Standard and Error Output |  cmd ?+ >> out.log    | cmd >> out.log 2>&1     |

## IX. Error Handling

### Error Capture: `?:`
  Followed by `?: expr`,

  - Typically, expr can be a lambda function that accepts an Error object. This Error object is a Map object, and specific properties can be accessed using `.`, `@`, or `[]` indexing.
      This object contains three properties:
      > + `code`
      > + `msg`
      > + `expr`

 - If expr is of a regular type, it provides a default value on exception.

### Error Ignore: `?.`
  Equivalent to `?: {}`

### Error Printing: `?+` `??` `?>`
  - Print error to standard output: `?+`
  Equivalent to `?: e -> echo e.msg`

  - Print error to error output: `??`
  Equivalent to `?: e -> eprint e.msg`

  - Output error as operation result: `?>`
  Equivalent to `?: e -> e.msg`

### Error Termination: `?!`
  - Silently terminate the pipeline on error
  `Fs.ls -l | ui.pick ?! |_ cp _ /tmp`
  > When the user abandons the selection, subsequent pipeline operations are terminated upon encountering an error.

  *The above methods can apply to any statement or function end*.
  *The priority of error handling characters is higher than that of pipelines*.

  ```bash
  6 / 0 ?.    # ignore err
  6 / 0 ?+    # print err to stdout
  6 / 0 ??    # print err to stderr
  6 / 0 ?!    # use this err msg as result

  let e = x -> echo x.code
  6 / 0 ?: e   # dealing with the err with a function/lambda

  # also functions could use err handling too.
  fn divide(x, y) {
      x / y
  } ?: e
  ```

**Tips**
- Utilizing the features of error handling characters, you can also provide default values for failed calculations:

  ```bash
  let e = x -> 0
  let result = 6 / 0 ?: e
  echo result              # Outputs default value: 0

  # Equivalent to
  let result = 6 / 0 ?: 0
  echo result              # Outputs default value: 0
  ```

- When defining functions, use error handling
  ```bash
  let e = x -> {print x.code}

  fn div(a, b) {
      a / b
  } ?: e

  div(3, 0)                # the defined error handling will be executed here.
  ```

### Error Debugging:
- Error Messages
  Generally, observing error messages is sufficient to identify the problem.
For example:
  ```bash
  [PARSE FAILED]                        # Syntax parsing error
  unexpected syntax error: expect "{"
        ▏
      1 ▏ fn assert(actual, expected, test_name, test_count=0) {
      2 ▏     if actual != expected
        ▏                           ^
      3 ▏         print "[FAIL]" test_count test_name "| Actual: " actual "| Expected: " expected
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
  debug c                     # Outputs: Range〈0..9,1〉

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

  } else {

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
  The use of the Log module is also beneficial for error debugging; for specifics, please refer to the [Log Module Documentation](/en/libs/log).

## X. Running Modes

1. REPL Interactive Mode
  User interactive mode, handling user input and output, highlighting code.

   **Auto Prompt**

   - Input part of a word, Lumesh will automatically suggest matching built-in functions, third-party commands, aliases, built-in syntax, and historical commands.

   - Input `module_name + dot + space`: `Into. ` will prompt all available functions within that module.

   **Auto Completion**:

   - Pressing `Tab` will trigger auto completion.

   Completion Modes:

     + The first word will trigger *command completion*, including system executable commands, built-in functions, aliases, and built-in syntax.

     + Words with path symbols, such as `.`, `/` (on Windows, it is `\`), will trigger *path completion*.

     + More than two words that are not paths will trigger *AI auto completion*.

     _To use AI completion, please configure the AI interface first._ The default is the ollama configuration, which connects after running ollama.

   **Common Keys**:

   - `Right` or `Ctrl+J`: Accept completion suggestions (supports paths and historical commands).
   - `Alt+J`: Accept single-word completion suggestions.
   - `Ctrl+D`: Terminate input.
   - `Ctrl+C`: Terminate the current operation.

   For other shortcuts, please refer to [Shortcuts](/en/keys).

   **Custom Key Binding**:
   Custom bindings can be configured in the configuration file.
   Please refer to the [Configuration File](#configuration-file) section.

   **History**: Saved in the default configuration directory, configurable through the configuration file.
   - `UP/DOWN` or `Ctrl+N/P`: Switch historical records.

2. Script Parsing Mode

  Run scripts:

  ```bash
  lume ./my.lm
  ```

  If you are particularly concerned about performance, you can use the command parsing dedicated lite version: `lumesh`.

3. LOGIN-SHELL Mode

  Start the shell; you should first configure the system environment variables in the config file. Similar to .bashrc content.

4. Strict Mode

In strict mode, variables must be declared first and cannot be redeclared.

When using variables, the `$` prefix should be used. This is to enhance script parsing speed.

In non-strict mode, direct access to variables without `$` is allowed.

Both modes allow implicit type conversion.

## XI. Parameters and Environment Variables
### 1. **Command Line Parameters**:
   - Script parameters are accessed through the `argv` list.
   ```bash
   # Run lumesh script.lm Alice tom
   echo argv  # Outputs "[Alice, tom]"
   echo argv@0  # Outputs "Alice"
   ```
### 2. **Environment Variables**
   ```bash
   PATH             # System environment variable
   HOME             # System environment variable

   env              # List all current environment variables
   IS_LOGIN         # Is LOGIN-SHELL
   IS_INTERACTIVE   # Is interactive mode
   IS_STRICT        # Is strict mode
   ```
### 3. **IFS**
This is a special environment variable used for internal field separation.

- The table of three states controlled by IFS functionality:

| Mask | Syntax Type | Mask Functionality Disabled | Enabled but IFS Value Not Set | Enabled and IFS Value Set |
|------|-------------|-----------------------------|-------------------------------|---------------------------|
| 2    | **Command String Parameters** | Pass entire string as a single parameter | Use `newline` as default separator | Use IFS value as separator to split |
| 4    | **For Loop Split** | Try to split by `line, space, semicolon, comma` | Try to split by `line, space, semicolon, comma` | Split string using IFS value |
| 8    | **string.split Function** | Split using `whitespace` (`split_whitespace`) | Use `space(" ")` as default separator | Split string using IFS value |
| 16   | **parse.to_csv Function** | Use `comma(",")` as CSV separator | Use `comma(",")` as default separator | Use the first character of IFS value as CSV separator* |
| 32   | **ui.pick Function** | Use `newline("\n")` to split options | Use `newline("\n")` as default separator | Split option list using IFS value |

_*Note: The CSV function has special handling; if IFS is set to "\n", it still uses a comma as the separator._

- Control Logic Flow

1. **First Layer Check**: Check if the `LUME_IFS_MODE` bitmask is enabled for the corresponding functionality through.
2. **Second Layer Check**: If the functionality is enabled, check if the IFS variable is set to a valid string value.
3. **Third Layer Execution**: Execute the corresponding splitting logic based on the check results.

This three-layer control design provides maximum flexibility and backward compatibility. The IFS variable will be ensured to exist during system initialization.

- Configuration Description

- **LUME_IFS_MODE**: Bitmask that controls which syntax uses IFS.
- **IFS**: Actual separator string.
- **Default Value**: LUME_IFS_MODE defaults to 2, affecting only command parameter splitting.

- Usage Example

To enable all IFS functionalities, you can set:
  ```bash
  let LUME_IFS_MODE = 62  # 2 + 4 + 8 + 16 + 32 = 62, enabling all functionalities
  ```

To enable only specific combinations of functionalities, you can sum the corresponding bit values. For example:
- To enable only command parameter and string splitting: `LUME_IFS_MODE = 10` (2 + 8)
- To enable for loop and CSV parsing: `LUME_IFS_MODE = 20` (4 + 16)

> IFS (Internal Field Separator) is a shell concept that has been implemented in Lumesh as a configurable string splitting mechanism. Through the bitmask system, users can precisely control which syntax contexts use IFS splitting and which use default behavior. This design provides backward compatibility while allowing users to customize string handling behavior as needed.

---

## XII. Configuration Files

 - Supported Configurations

 1. Support for separate configurations for different modes.
 2. AI interface configuration.
 3. Shortcut key binding command configuration.
 4. Command abbreviation configuration.
 5. Command alias configuration.
 6. History path configuration.
 7. Prompt configuration.
    ```bash
        # MODE: 1=use template; 2=use starship; 0=use default.
        let LUME_PROMPT_SETTINGS = {
            MODE: 1,
            TTL_SECS: 2
        }
    ````
    The command line prompt can work in the following modes:
    - 0, default mode
    - 1, template mode, where the template can be a regular expression or a function.

    TTL is used to control the frequency of cache updates, in seconds.

    Generally, regular expressions are more resource-efficient;
    ```bash
    # The template supports the following variables: $CWD, $CWD_SHORT
    let LUME_PROMPT_TEMPLATE = (String.blue($CWD_SHORT) + String.yellow(String.bold(">> ")))
    ```
    Functions can be used to render more complex prompts, such as displaying the git branch. Function templates consume slightly more resources than regular templates.

    ```bash
    let LUME_PROMPT_TEMPLATE := x -> {
        String.format "{} {}{}{} " String.blue(x) String.green(String.bold("|")) \
        (if (Into.exists '.git') {git branch --show-current | String.cyan()} else "") \
        String.green(String.bold(">"))
    }

    ```
    - 2, starship mode

 8. ...

For detailed configuration items, please refer to the default configuration.

 - Configuration File Path
 If no configuration file path is specified on the command line, Lumesh will read the default configuration file located in the default path under the subpath `lumesh/config.lm`.

Default Paths:

| Platform | Path                                 | Example                                  |
|----------|--------------------------------------|------------------------------------------|
| Linux    | `$XDG_CONFIG_HOME` or `$HOME`/.config | /home/alice/.config                      |
| macOS    | `$HOME`/Library/Application Support   | /Users/Alice/Library/Application Support |
| Windows  | `{FOLDERID_RoamingAppData}`           | C:\Users\Alice\AppData\Roaming          |

Executing `Into.dirs()` can show your default paths.

- History Path
If not specified in the configuration file, it will be saved in the default path.

On Linux/macOS, the filename will be `.lume_history`.
On Windows, the filename will be `lume_history.log`.

Default Paths:

| Platform | Path                               | Example                      |
|----------|------------------------------------|------------------------------|
| Linux    | `$XDG_CACHE_HOME` or `$HOME`/.cache | /home/alice/.cache           |
| macOS    | `$HOME`/Library/Caches            | /Users/Alice/Library/Caches  |
| Windows  | `{FOLDERID_LocalAppData}`         | C:\Users\Alice\AppData\Local |

Executing `Into.dirs()` can show your default paths.

## XIII. Built-in Function Library

Lumesh includes a wealth of practical function libraries to facilitate convenient functional programming, such as:
- **Collection Operations**: `List.reduce, List.map`
- **File System**: `Fs.ls, Fs.read, Fs.write`
- **String Handling**: `String.split, String.join`, regex module, formatting module
- **Time Operations**: `Fs.now, Fs.format`
- **Data Conversion**: Into module, Parse module
- **Mathematical Calculations**: Complete mathematical function library
- **Logging**: Log module
- **UI Operations**: `Ui.pick, Ui.confirm`

You can view available modules and functions using the `help` command.
You can view specific module functions using the `help String` command.

> Built-in functions support three calling methods:
      ```bash
      String.red(msg)
      String.red msg
      String.red! msg
      ```
And support for chained calls and pipeline method calls:
      ```bash
      msg.red()
      msg | .red()
      ```

For detailed content, please continue reading:
 - [Built-in Libs](/en/libs/index)

## XIV. Module Import
When multiple script files need to work together to complete complex tasks, you can import other modules.
Syntax:
  ```bash
  use module_path
  use module_path as alias
  ```

**Please Note**
- Module names must be unique:
    All imported modules (including descendant modules) cannot have the same name. If there are, please rename using `as name`.

    This is a trade-off for performance and convenience; to achieve higher performance, we decided to accept this minor inconvenience.

- Modules should be used as utility functions:
    When importing a module, only the `fn` definitions and `use` statements will be read; statements outside of function definitions will be ignored to avoid unintended code execution.

For detailed content, please continue reading:
 - [Built-in Libs](/doc/libs/)
 - [Bash Syntax Comparison](/glance/bash)
