---
title: Parameters and Environment Variables
date: 2025-06-11 19:16:45
highlight: true
weight: 80
tags:
 - syntax
categories:
 - wiki
 - syntax
---

## **Command Line Parameters**:
Parameters are accessed through the `argv` list.

- **Script Parameters**
```bash
lumesh script.lm Alice tom
# in script.lm
print argv  # Outputs "[Alice, tom]"
print argv[0]  # Outputs "Alice"
```

- **Command Line Passing**
```bash
lume -c 'print argv' -- a b   # or
lume -c 'print argv' a b
# Outputs
[a, b]
```

## **Environment Variables**

### System Environment Variables
- `sys.env()` lists system environment variables.
```bash
set var value    # Set a system environment variable
unset var        # Remove a system environment variable
```
Typical system variables:
```bash
PATH             # System environment variable
HOME             # System environment variable
```
   
### Current Environment Variables
- `sys.vars()` lists current environment variables.
```bash
let var value    # Set a current environment variable
del var          # Remove a current environment variable
```

Lumesh-defined current environment variables:
   ```bash
   env              # Lists all current environment variables
   IS_LOGIN         # Whether it is a LOGIN-SHELL
   IS_INTERACTIVE   # Whether it is in interactive mode
   IS_STRICT        # Whether it is in strict mode
   ```
### IFS
This is a special environment variable used for internal field separation.

- IFS functionality controls three states of behavior:

| Mask | Syntax Type | Mask Functionality Not Enabled | Enabled but IFS Value Not Set | Enabled and IFS Value Set |
|------|-------------|-------------------------------|-------------------------------|---------------------------|
| 2    | **Command String Parameters** | Pass the entire string as a single parameter | Use `newline` as the default separator | Split using IFS value as the separator |
| 4    | **For Loop/Loop Dispatch Pipe Split** | Attempt to split by `line, space, semicolon, comma` in order | Attempt to split by `line, space, semicolon, comma` in order | Split string using IFS value |
| 8    | **string.split Function** | Split using `whitespace` (`split_whitespace`) | Use `space(" ")` as the default separator | Split using IFS value as the separator |
| 16   | **From.csv/Into.csv Functions** | Use `comma(",")` as the CSV separator | Use `comma(",")` as the default separator | Use the first character of IFS value as the CSV separator* |
| 32   | **ui.pick Function** | Use `newline("\n")` to separate options | Use `newline("\n")` as the default separator | Split option list using IFS value |

_*Note: The CSV functions have special handling; if IFS is set to "\n", it still uses a comma as the separator._

- Control Logic Flow

1. **First Level Check**: Check if the `LUME_IFS_MODE` bitmask enables the corresponding functionality.
2. **Second Level Check**: If the functionality is enabled, check if the IFS variable is set to a valid string value.
3. **Third Level Execution**: Execute the corresponding splitting logic based on the check results.

This three-layer control design provides maximum flexibility and backward compatibility. The IFS variable will be ensured to exist during system initialization.

- Configuration Description

- **LUME_IFS_MODE**: Bitmask controlling which syntax uses IFS.
- **IFS**: Actual separator string.
- **Default Value**: LUME_IFS_MODE defaults to 2, affecting only command parameter splitting.

- Usage Example

To enable all IFS functionalities, you can set:
  ```bash
  let LUME_IFS_MODE = 62  # 2+4+8+16+32 = 62, enabling all functionalities
  ```

To enable only specific combinations of functionalities, you can sum the corresponding bit values. For example:
- To enable only command parameters and string splitting: `LUME_IFS_MODE = 10` (2+8)
- To enable for loops and CSV parsing: `LUME_IFS_MODE = 20` (4+16)

> IFS (Internal Field Separator) is a shell concept implemented in Lumesh as a configurable string splitting mechanism. Through a bitmask system, users can precisely control which syntax contexts use IFS splitting and which use default behavior. This design provides backward compatibility while allowing users to customize string processing behavior as needed.
[1] https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Environment_variables/Using
[2] https://www.reddit.com/r/learnpython/comments/gysg3z/can_somebody_explain_to_me_why_exactly/
[3] https://en.wikipedia.org/wiki/Environment_variable
[4] https://www.ibm.com/docs/ssw_aix_72/osmanagement/HT_listing_env_var.html
[5] https://medium.com/chingu/an-introduction-to-environment-variables-and-how-to-use-them-f602f66d15fa
[6] https://www.ninjaone.com/blog/how-to-view-and-manage-environment-variables/
[7] https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap08.html
[8] https://configu.com/blog/setting-env-variables-in-windows-linux-macos-beginners-guide/
[9] https://developer.android.com/tools/variables
[10] https://notes.kodekloud.com/docs/Advanced-Bash-Scripting/Special-Shell-Variables/ifs
[11] https://medium.com/@linuxrootroom/understanding-ifs-in-bash-scripting-3c67a39661e9
[12] https://www.baeldung.com/linux/ifs-shell-variable
[13] https://learn.microsoft.com/en-us/windows/win32/procthread/environment-variables
[14] https://stackoverflow.com/questions/780188/when-to-use-environment-variable-or-command-line-parameter
[15] https://unix.stackexchange.com/questions/184863/what-is-the-meaning-of-ifs-n-in-bash-scripting
[16] https://docs.aws.amazon.com/cli/v1/userguide/cli-configure-envvars.html
[17] https://en.wikipedia.org/wiki/Input_Field_Separators
[18] https://www.shellscript.sh/examples/ifs/
[19] https://www.tutorialspoint.com/the-meaning-of-ifs-in-bash-scripting-on-linux
[20] https://www.gatsbyjs.com/docs/how-to/local-development/environment-variables/
[21] https://journals.librarypublishing.arizona.edu/jslat/article/111/galley/105/view/
[22] https://mywiki.wooledge.org/IFS
[23] https://stackoverflow.com/questions/26479562/what-does-ifs-do-in-this-bash-loop-cat-file-while-ifs-read-r-line-do
[24] https://pubs.opengroup.org/onlinepubs/9699919799.orig/utilities/V3_chap02.html
[25] https://tariqumrani.tech.blog/2023/09/04/universal-grammar/
[26] https://docs.nuance.com/speech-suite/nr-config/cfg_set_params_precedence_rules.html
[27] https://docs.oracle.com/javase/tutorial/essential/environment/cmdLineArgs.html
[28] https://www.geeksforgeeks.org/cpp/command-line-arguments-in-c-cpp/
[29] https://www.cherryservers.com/blog/how-to-set-list-and-manage-linux-environment-variables
[30] https://superuser.com/questions/949560/how-do-i-set-system-environment-variables-in-windows-10
[31] https://www.tutorialspoint.com/cprogramming/c_command_line_arguments.htm
[32] https://vercel.com/docs/environment-variables/system-environment-variables
[33] https://www.computerhope.com/issues/ch000549.htm
[34] https://www.whitman.edu/mathematics/java_tutorial/java/cmdLineArgs/cmdLineArgs.html
[35] https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_environment_variables?view=powershell-7.5
[36] https://unix.stackexchange.com/questions/244323/how-to-show-an-environment-variables-current-value
[37] https://kevinlinxc.medium.com/viewing-and-setting-environment-variables-in-every-shell-4df43127bd8
[38] https://stackoverflow.com/questions/5327495/list-all-environment-variables-from-the-command-line
[39] https://superuser.com/questions/1533858/ifs-variable-in-linux
[40] https://www.reddit.com/r/learnpython/comments/rvugkv/anyone_to_explain_what_command_line_arguments_are/
[41] https://learn.microsoft.com/en-us/windows/terminal/command-line-arguments
[42] https://gobyexample.com/command-line-arguments
[43] https://jdtournier.github.io/cmdline-tutorial/intro.html
[44] https://docs.unity3d.com/6000.3/Documentation/Manual/CommandLineArguments.html
