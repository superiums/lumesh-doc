---
title: 启动与运行
date: 2025-06-11 19:16:45
highlight: true
weight: 2
tags:
 - install
categories:
 - wiki
 - install
---

> 交互模式通过`lume` 或 `lume -i`命令启动

## 启动参数

命令行支持多种执行模式，如同您看到的：

```bash
> lume -h
Lumesh scripting language runtime

Usage: lume [OPTIONS] [FILE_N_ARGS]... [-- [CMD_ARGV]...]

Arguments:
  [FILE_N_ARGS]...  script file and args to execute
  [CMD_ARGV]...     args for cmd

Options:
  -p, --profile      config file
  -s, --strict       strict mode
  -i, --interactive  force interactive mode
  -m, --cfmoff       NO command first mode
  -a, --aioff        NO ai mode
  -n, --nohistory    NO history (private) mode
  -c, --cmd <CMD>    command to eval
  -h, --help         Print help
  -V, --version      Print version
```

## 运行模式

### 1. REPL 交互模式
> `lume`

  用户交互模式是默认启动模式，处理用户输入输出。
  
  支持代码高亮，自动补全。

   **自动提示**

   - 输入部分单词，lumesh将自动提示匹配的 内置函数、三方命令、别名、内置语法、历史命令

   - 输入`模块名+点+空格`：`into. ` 会提示该模块内的全部可用函数。

   **自动补全**：

   - 按下`Tab`会触发自动补全。

   补全模式：

     + 第一个单词，会触发 *命令补全*，包括系统可执行命令、内置函数、别名、内置语法。

     + 带路径符号的单词，如`.`,`/` (win则是`\`), 会触发*路径补全*。

     + 超过二个单词，且不是路径，则触发 *AI自动补全*。

     _要使用AI补全，请先配置AI接口_ 默认的是ollam的默认配置，运行ollama后即可连接。


   **常用按键**：

   - `Right` 或 `Ctrl+J`：接受补全建议（支持路径和历史命令）。
   - `Alt+J` ： 接受单个单词的补全建议。
   - `Ctrl+D`：终止输入。
   - `Ctrl+C`：终止当前操作。
   - `Ctrl+L`： 清屏

   其他快捷键请参考 [快捷键](keys)

   **自定义快捷键绑定**：
   可在配置文件配置自定义绑定。
   请参看 [配置文件](config) 章节。

   **历史记录**：保存在默认配置目录,可通过配置文件配置。
   - `UP/DOWN` 或 `Ctrl+N/P`：切换历史记录。


### 2. 脚本解析模式
> `lume [文件名]`
  运行脚本：

  ```bash
  lume ./my.lm
  lume ./my.lm arg1 arg2
  ```

  如果您特别关注性能，可以使用命令解析专属lite版本：`lumesh`

### 3. 命令执行模式
> `lume -c <命令> <参数>...`
如果需要在执行命令后保留REPL窗口，继续交互，可加`-i`参数
> `lume -ic <命令> <参数>...`

3. LOGIN-SHELL模式
> 系统启动时的第一个shell

  启动shell，应先在config文件里，配置好系统环境变量。类似.bashrc的内容。
  准备好后，可执行`set_as_login_shell()`或以下脚本：
  
  ```bash
    let p = (About | .get('bin'))
    if !(fs.read /etc/shells | .contains($p)) {
        sudo lume -c `fs.append /etc/shells "\n$p"` ?: doas lume -c `fs.append /etc/shells "\n$p"`
    }
    chsh -s $p
  ```
  
### 4. 严格模式

- 严格

严格模式下，变量应先申明，且不能重复申明。

使用变量时，应使用 `$` 前缀。这是为了提升脚本解析速度。

- 非严格

非严格模式下，允许不带`$`直接访问变量。

两种模式都允许隐式转换数据类型。


#### 模式切换
- 启动模式
  取决因素1: 在配置文件中的设置： `set LUME_STRICT = true` 则启用严格模式。
  
  取决音素2: 启动lume的参数： 如有 `-s` 参数，将覆盖配置文件的设置。

- 命令切换
  ```bash
  sys.set_strict(true)     # 开启严格模式
  sys.set_strict(false)    # 关闭严格模式
  ```

### 5. 命令优先模式

#### 模式介绍

- 普通模式
优先解析符号为操作符，数字为数字。
如`ping '1.1.1.1'` 和 `chmod '+x' a.b` 需要引号
`1+2` `a[0]` `0...8` 和 `map.key` 可以直接使用。

- 命令优先模式CFM
优先解析为命令，如`.` `+` 等操作符将作为普通字符处理。例如
`ping 1.1.1.1` `chmod +x a.b` 都被解析为命令而无须添加引号。
任何数字相关运算无效。


#### 模式切换
- 启动模式
  取决因素1: 在配置文件中的设置： `set LUME_CFM = true` 则启用命令优先模式。
  
  取决音素2: 启动lume的参数： 如有 `-m` 和 `-M`参数，将覆盖配置文件的设置。
  

- 命令切换
  ```bash
  sys.set_cfm(true)     # 开启CFM模式
  sys.set_cfm(false)    # 关闭CFM模式
  ```
  
- 临时切换
  行首输入`:` 切换到普通模式
  
  行首输入`>` 切换到命令优先模式

- 免切换
  多行输入自动识别为普通模式。

#### 举例

- CFM模式

> `lume -m`
```bash
ping 1.1.1.1     # 正确，CFM模式下`.`是普通字符
ping '1.1.1.1'   # 正确
3+5^2            # 错误，CFM模式下`+`是普通字符
:3+5^2           # 正确，临时跳过CFM模式
ls               # 正确， 执行命令
```



- 普通模式

> `lume -M`
```bash
ping 1.1.1.1     # 错误，`.`是运算符
ping '1.1.1.1'   # 正确
3+5^2            # 正确，`+`是运算符
ls               # 只是符号，不执行
ls _             # 执行
>ls              # 执行
```

  
6. 无AI模式
7. 无历史记录模式
