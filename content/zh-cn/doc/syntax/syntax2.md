---
title: Lumesh 语法手册2
subtitle: （函数、命令、管道、错误、交互）
date: 2025-06-11 19:16:45
highlight: true
tags:
 - syntax
categories:
 - wiki
 - syntax
---
> （函数、命令、管道、错误、交互）

## 六、函数

### **fn函数定义**
   - 使用 `fn` 定义，支持*默认参数*，支持*剩余参数收集*。
       ```bash
       fn add(a,b,c=10,*d) {
          return  a + b + c + len(c)
       }

       # 等价于：
       fn add(a,b,c=10,*d) {
          a + b + c + len(c)
       }

       echo add(2, 3)  # 输出 5
       echo add(2,3,4, 5)  # 输出 10
       ```


### **lambda表达式**
   - 使用 `->` 定义。

       ```bash
       let add = (x,y) -> x + y
       ```
  - lambda 与 普通函数的区别：
      + lambda 不支持默认参数和return语句，不支持剩余参数收集。
      + lambda *支持部分参数应用*，返回后续lambda
  - 相同的是：
      lambda和函数都 继承当前环境变量，在隔离的环境中运行，不会污染当前环境。


### 函数调用
   函数名后紧跟`()`或`!` 执行函数。

  ```bash
  # 自定义函数可以这样调用
  add(3,5)
  # 或者这样
  add! 3 5   #注意需要添加!后缀，用以区别于命令调用。
  ```

   区分函数调用和执行命令的方式，有助于避免同名函数覆盖命令，如：
  ```bash
  # 测试用例6：函数与命令同名覆盖
  fn ls() { echo "My ls" }
  ls -l                # 执行系统命令ls
  ls()                 # 调用函数
  ls!                  # 使用!后缀时,调用函数,这是一个将括号扁平化的语法糖。
  ```

**Tips**：
- 当参数是字面的lambda表达式时，请使用括号模式，平铺模式无法解析。
- 类似的，当参数是字母的&&等逻辑运算时，请使用括号模式，或使用括号分组后的平铺模式。


**边缘情况**：
   - 函数名冲突时，新定义会覆盖旧的。
   - 调用函数时，参数数量不匹配会报错。如：
   `[ERROR] arguments mismatch for function `add`: expected 3, found 1`

### 链式调用

  ```bash
  [1,3,5,6].sum()

  "hello world"
      .split(' ')
      .map(s -> s.to_upper())
      .join('-')
      .green()
  ```
对于常用数据类型，可以直接使用链式调用，包括：
`String, List, Map, Time, Integer, Float, Range`

### 装饰器
装饰器是一种特殊的高阶函数，可以在特定函数的执行前后，插入需要的逻辑。

```bash
# 在函数定义时使用装饰器
@timeit
fn test(n){
  for i in 0..n {
    n += i
  }
  print 'sum is' n
}

# decorator func
fn timeit(){
    fn wrapper(func_ti){
        (args_ti) -> {
            let start = Time.stamp_ms()                       # 函数执行前的逻辑
            func_ti(args_ti)                                  # 函数执行
            let end = Time.stamp_ms()                         # 函数执行后的逻辑
            print '>Time:'.green().bold() (end - start) 'ms'
        }
    }
}
```
**请注意**
- 同一个函数上应用的所有装饰器定义的变量，不能重名。
例如，要在上面的`test`函数上应用第二个装饰器，则该装饰器不能再使用 `func_ti`, `args_ti` 变量名。
这是综合考虑性能和便捷性的权衡，为了更高的性能，我们决定接受这个小小的不便。

## 七、运行系统命令

### 命令调用

在lumesh中，您可以像在其他shell中一样，便捷的运行程序，如
  ```bash
  ls
  ls -l
  ```

- 多条命令，前面的命令失败则后续的命令不会继续执行；
- 除非错误已经处理：

  ```bash
  ls '/0' ; ls -l         # 后者不会执行
  ls '/0' ?. ; ls -l      # 后者会执行
  ```
- 使用`^`后缀强制开启命令模式
  ```bash
  let id = 5
  id^ -u              # 告诉系统这个是命令
  ```
- 空参数命令
  ```bash
  notepad.exe             # 如果没有被识别为命令(比如Windows下，带扩展名的命令)
  notepad.exe _           # 传入空参数，强制识别为命令
  ```

### 通配符扩展
在lumesh中,同样支持`~` 目录扩展和`*`扩展：

  ```bash
  ls ~/**/*.md
  ```
*但不支持bash中的{}扩展*

### 后台运行 和 输出控制符
- 和bash一样，使用`&`符号让程序后台运行。
- 输出控制符`&`：我们采用更简洁的方式关闭命令的输出。
- 输出控制符仅适用于命令，不适用于函数。

  ```bash
  thunar &         # run in background, and shutdown stdout and stderr
  ls &-            # shutdown stdout
  ls /o &?         # shtudown stderr
  ls  /o '/' &+            # shutdown stdout and stderr

  ls  /o '/' &? | bat            # shutdown stderr and pipe stdout to next cmd.

  ```
下面是和bash的语法对比：

| 任务         |  lumesh  | bash               |
|--------------|----------|--------------------|
| 后台运行     |   cmd &  |cmd &               |
| 关闭标准输出 |   cmd &- |cmd 1> /dev/null    |
| 关闭错误输出 |   cmd &? |cmd 2> /dev/null    |
| 关闭所有输出 |   cmd &. |cmd 2>&1 > /dev/null|

### 输出通道

- 标准输出 （定义和bash一样，可以用`&-`关闭标准输出）
- 错误输出 （定义和bash一样，可以用`&?`关闭标准输出，可以用错误处置符处理错误）
- 结构化数据通道 （lumesh特有，可以在配置中关闭）

  在配置文件中设置 `let LUME_PRINT_DIRECT= False` 可关闭结构化数据通道

  ```bash
  ❯ ls
  Documents  Downloads  dprint.json  typescript        # 标准输出


  ❯ ls /x
  ls: cannot access '/x': No such file or directory    # 错误输出
  [ERROR] command `ls` failed: "exit status: 2"        # Lumesh错误捕获，错误处置符处理的目标

  ❯ 3 + 5
  8                           # 标准输出
    >> [Integer] <<           # 结构化通道 数据类型提示
  8                           # 结构化通道（运算结果）

  ```

**输出打印**

- print 消费掉运算结果，打印到标准输出
- tap   打印到标准输出，但同时保留运算结果

  ```bash
  ❯ print 3+5
  8

  ❯ tap 3+5
  8

    >> [Integer] <<
  8

  ```

## 八、管道与重定向

### 管道
1. 管道简介

lumesh采用和bash一样的管道符，但更加强大：

- 智能管道 `|`
    自动判断应有行为方式，可传输结构化数据。

    + 左侧：可以自动从运算结果或标准输出读取；
      > *读取原则*：
      > 对于函数和内置命令、运算，读取结构化数据通道。
      > 对于系统三方命令，读取标准输出。

    + 右侧：
      > *输出原则*：
      > 如果是三方命令则作为标准输入传入数据
      > 如果是函数则作为末尾参数传递。

|   数据          |  函数、运算、内置命令  |     三方命令       |
|-----------------|------------------------|--------------------|
|   输人（左侧）  |   读取结构化数据通道   |    读取标准输出    |
|   输出（右侧）  |   输出到最后一个参数   |    输出到标准输入  |


- 位置管道 `|_`
    强制使用参数位置管道，管道到右侧函数的指定位置，以`_`为占位符，如果未指定，则附加到参数末尾。
    多数情况下不需要手动指定。使用`|`足够。
    但如果右侧的命令无法读取标准输入，或需要指定参数位置时，需要使用此管道。

  ```bash
  3 | print a _ b               # 打印结果： a 3 b
  ```

- 循环派发管道 `|>`
    用于将左侧列表任务，循环派发给右侧命令。
    同样支持`_`占位符。

  ```bash
    0...8 |> print lineNo          # 将打印8行
    ls -1 *.txt |> cp _ /tmp/            # 将拷贝列出的文件
  ```


- PTY管道 `|^`
    强制右侧命令使用PTY模式。
    某些程序需要完全的终端控制权限，才能正常工作，因此需要pty模式。
    智能管道维护着一个此类程序的列表，因此一般无须强制开启pty模式，但如果您发现某个程序无法正常工作，可以尝试强制开启PTY模式。

2. 管道基础用法

传统的bash管道为了兼容更多命令，只能处理字节流。字节流是由三方命令输出的文本数据，shell通过管道派发给下一个程序处理。这种模式极大的方便了数据在不同程序之间的传输。

实际上结构化管道的效率更高，因为它省去了从普通文本转化为结构化数据的工作；甚至有的还可以省去和输入输出设备打交到的时间。

**结构化管道效率更高**
例如：
  ```bash
  # --这是文本流管道--
  echo 3+5 | bat              # 这是三方命令，且需要管道从标准输出读取, 效率双重降低

  # --这是结构化管道-- *推荐用法*
  3+5 | bat                   # 运算结果直接传送到下一个程序

  # --这是不正确用法--
  print 3+5 | bat             # print语句打印到标准输出，同时传递出None作为运算结果，bat捕捉到的是print语句的运算结果None

  # --这是结构化管道--
  tap 3+5 | bat               # tap语句打印到标准输出，同时将结果向下传递，bat正确捕捉到这个运算结果
  3+5 | tap | bat             # 等价上一句
  ```
从上面的例子可以看出，
  + 运算结果如不需要打印，只需向下传递，应直接使用管道。
  + 运算结果如果需要打印，并向下传递，应使用tap后再管道。
  + 运算结果打印后不需要向下传递，应使用print。
  + 应避免使用echo，除非需要echo -e 等高级选项。

3. 管道高级用法

- **筛选**
  ```bash
  # 按大小筛选数据，并显示指定列：
  Fs.ls -l | where(size > 5K) | select(name,size,modified)

  # 输出
  +--------------------------------------+
  | MODIFIED          NAME          SIZE |
  +======================================+
  | 2025-06-02 06:26  Cargo.lock    46K  |
  | 2025-06-02 04:40  CHANGELOG.md  9K   |
  +--------------------------------------+

  ```

- **排序**
  ```bash
  # 按时间筛选数据，并按指定列排序
  Fs.ls -l | where( Fs.diff('d',modified) > 3 ) | sort(size,name)

  # 输出
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

- **分组**
  ```bash
  # 按类型分组
  Fs.ls -l | group 'type'     # type是函数名，所以引号不能省略

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

- **兼容系统的三方命令**
> 可以使用From.cmd 或 Into.table 或链式调用 .table()，将文本转换结构化数据。可接收列名序列作为参数。

> 但比较数据时，需要手动转换类型。

  ```bash
  ls -l --time-style=long-iso | .table() | where(int C4>1000)

  # 输出
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

### 重定向
- `<<` 读取
- `>>` 追加输出
- `>!` 覆盖输出
`1 + 2 >> result.txt`

*错误重定向：结合错误处置符*
具体用法请参考 错误处理章节。

| 重定向类型               | Lume                  | Bash                    |
|--------------------------|-----------------------|-------------------------|
| 标准输出，追加           | cmd >> out.txt        | cmd  >> out.txt         |
| 标准输出，覆盖           | cmd >! out.txt        | cmd  > out.txt          |
| 错误替代标准输出         |  cmd ?> >> out.log    | command 2>&1 >> out.log |
| 单独的错误输出           |  cmd ?? >> out.log    | cmd 2>> out.log         |
| 合并标准输出和错误输出   |  cmd ?+ >> out.log    | cmd >> out.log 2>&1     |


## 九、错误处理

### 错误捕获： `?:`
  语句后跟 `?: expr`，

  - 通常expr 可以是一个lambda函数，该函数可以接受一个Error对象。该Error对象是一个Map对象，可以用`.`或`@`或`[]`索引，获取具体属性。
      该对象包含三个属性：
      > + `code`
      > + `msg`
      > + `expr`

 - 如果expr是常规类型，则在异常时提供默认值。

### 错误忽略： `?.`
  相当于 `?: {}`

### 错误打印： `?+` `??` `?>`
  - 错误打印到标准输出： `?+`
  相当于 `?: e -> echo e.msg`

  - 错误打印到错误输出： `??`
  相当于 `?: e -> eprint e.msg`

  - 错误作为运算结果输出： `?>`
  相当于 `?: e -> e.msg`

### 错误终止： `?!`
  - 遇到错误时，静默终止管道
  `Fs.ls -l | ui.pick ?! |_ cp _ /tmp`
  > 当用户放弃选择时，遇错终止后续管道操作。


  *以上方式可以作用于任何语句或函数末尾*。
  *错误处置符的优先级高于管道*。

  ```bash

  6 / 0 ?.    # ignore err
  6 / 0 ?+    # print err to stdout
  6 / 0 ??    # print err to stderr
  6 / 0 ?!    # use this err msg as result

  let e = x -> echo x.code
  6 /0 ?: e   # deeling the err with a function/lambda

  # also funcions could use err handling too.
  fn divide(x,y){
      x / y
  }?: e

  ```
**Tips**
- 利用错误处置符的特性，还可以为计算失败时提供默认值：

  ```bash
  let e = x -> 0
  let result = 6 /0 ?: e
  echo result              # 输出默认值: 0

  # 等同于
  let result = 6 /0 ?: 0
  echo result              # 输出默认值: 0
  ```

- 在函数定义时，使用错误处理
  ```bash
  let e = x -> {print x.code}

  fn div(a,b){
      a / b
  } ?: e

  div(3,0)                # the defined error handling will be executed here.
  ```
### 错误调试：
- 错误提示
  一般情况下，观察错误提示，足以发现问题所在。
如：
  ```bash
  [PARSE FAILED]                        # 语法解析错误
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
  [ERROR]                                # 运行时错误
  Message   [1]: type error, expected Symbol as command, found 0...8: List
  Expression[1]: 0...8  x

  SyntaxTree[1]:
  Cmd 〈0...8〉
  〖
    Symbol〈"x"〉
  〗
  ```


- `debug` 命令
  如需进一步调试，可以使用debug命令。

  ```bash
  # 简单数据调试：
  let c = 0..8
  debug c                     # 输出：Range〈0..9,1〉

  # 复杂语句调试：
  let a := if a {3 + 5} else { print a is False}
  debug a

  # 输出：
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
- Log 模块
  Log模块的使用也有利于错误调试，具体请参考[Log模块文档](/zh-cn/libs/log)

## 十、运行模式

1. REPL 交互模式
  用户交互模式，处理用户输入输出，高亮代码。

   **自动提示**

   - 输入部分单词，lumesh将自动提示匹配的 内置函数、三方命令、别名、内置语法、历史命令

   - 输入`模块名+点+空格`：`Into. ` 会提示该模块内的全部可用函数。

   **自动补全**：

   - 按下`Tab`会触发自动补全。

   补全模式：

     + 第一个单词，会触发 *命令补全*，包括系统可执行命令、内置函数、别名、内置语法。

     + 带路径符号的单词，如`.`,`/` (win则是`\`), 会触发*路径补全*。

     + 超过二个单词，且不是路径，则触发 *AI自动补全*。

     _要使用AI补全，请先配置AI接口_ 默认的是ollam的默认配置，运行ollama后即可连接。


   **常用按键**：

   - `Right` 或 `Ctrl+J`：接受补全建议（支持路径和历史命令）。
   - `Alt+J` : 接受单个单词的补全建议。
   - `Ctrl+D`：终止输入。
   - `Ctrl+C`：终止当前操作。

   其他快捷键请参考 [快捷键](/zh-cn/keys)

   **自定义快捷键绑定**：
   可在配置文件配置自定义绑定。
   请参看 [配置文件](#配置文件) 章节。

   **历史记录**：保存在默认配置目录,可通过配置文件配置。
   - `UP/DOWN` 或 `Ctrl+N/P`：切换历史记录。


2. 脚本解析模式

  运行脚本：

  ```bash
  lume ./my.lm
  ```

  如果您特别关注性能，可以使用命令解析专属lite版本：`lumesh`

3. LOGIN-SHELL模式

  启动shell，应先在config文件里，配置好系统环境变量。类似.bashrc的内容。

4. 严格模式

严格模式下，变量应先申明，且不能重复申明。

使用变量时，应使用 `$` 前缀。这是为了提升脚本解析速度。

非严格模式下，允许不带`$`直接访问变量。

两种模式都允许隐式转换数据类型。


## 十一. 参数与环境变量
### 1. **命令行参数**：
   - 脚本参数通过 `argv` 列表访问。
   ```bash
   # 运行 lumesh script.lm Alice tom
   echo argv  # 输出 "[Alice, tom]"
   echo argv@0  # 输出 "Alice"
   ```
### 2. **环境变量**
   ```bash
   PATH             #系统环境变量
   HOME             #系统环境变量

   env              #列出所有当前环境变量
   IS_LOGIN         #是否LOGIN-SHELL
   IS_INTERACTIVE   #是否交互模式
   IS_STRICT        #是否严格模式
   ```
### 3. **IFS**
这是一个特殊的环境变量，用于内部字段分割。

- IFS功能控制的三种状态行为表格

|掩码| 语法类型 | 掩码功能位未启用 | 启用但IFS值未设置 | 启用且IFS值已设置 |
|----|----------|------------------|-------------------|-------------------|
| 2 | **命令字符串参数** | 将整个字符串作为单个参数传递 | 使用`换行符`作为默认分隔符 | 使用IFS值作为分隔符分割 |
| 4 | **for循环/循环派发管道分割** | 依次尝试按`行、空格、分号、逗号`分割 | 依次尝试按`行、空格、分号、逗号`分割 | 使用IFS值分割字符串 |
| 8 | **String.split函数** | 使用`空白符`分割(`split_whitespace`) | 使用`空格(" ")`作为默认分隔符 | 使用IFS值作为分隔符 |
| 16| **From.csv/Into.csv函数** | 使用`逗号(",")`作为CSV分隔符 | 使用`逗号(",")`作为默认分隔符 | 使用IFS值的首字符作为CSV分隔符* |
| 32| **Ui.pick函数**  | 使用`换行符("\n")`分割选项 | 使用`换行符("\n")`作为默认分隔符 | 使用IFS值分割选项列表 |

_*注：CSV函数有特殊处理，如果IFS设置为"\n"，仍使用逗号作为分隔符。_

- 控制逻辑流程

1. **第一层检查**：通过  检查 `LUME_IFS_MODE` 位掩码是否启用对应功能
2. **第二层检查**：如果功能已启用，检查 `IFS` 变量是否设置为有效字符串值
3. **第三层执行**：根据检查结果执行相应的分割逻辑

这种三层控制设计提供了最大的灵活性和向后兼容性。系统初始化时会确保IFS变量存在。

- 配置说明

- **LUME_IFS_MODE**: 位掩码，控制哪些语法使用IFS
- **IFS**: 实际的分隔符字符串
- **默认值**: LUME_IFS_MODE默认为2，只影响命令参数分割

- 使用示例

要启用所有IFS功能，可以设置：
  ```bash
  let LUME_IFS_MODE = 62  # 2+4+8+16+32 = 62，启用所有功能
  ```

要只启用特定功能的组合，可以将对应的位值相加。例如：
- 只启用命令参数和字符串分割：`LUME_IFS_MODE = 10` (2+8)
- 启用for循环和CSV解析：`LUME_IFS_MODE = 20` (4+16)


> IFS（Internal Field Separator）是一个shell概念，在Lumesh中被实现为可配置的字符串分割机制。通过位掩码系统，用户可以精确控制哪些语法上下文使用IFS分割，哪些使用默认行为。这种设计提供了向后兼容性，同时允许用户根据需要自定义字符串处理行为。

---

## 十二、配置文件

 - 支持的配置

 1. 支持对不同的模式分别配置。
 通过检查`IS_LOGIN`，`IS_INTERACTIVE`实现对不同模式使用不同的配置。

 2. AI接口配置。
 ```bash
 # ====== default AI Helper settings. following is default.
 let LUME_AI_CONFIG = {
     host: "localhost:11434",
     complete_url: "/completion",
     chat_url: "/v1/chat/completions",
     complete_max_tokens: 10,
     chat_max_tokens: 100,
     model: "",
     system_prompt: "you're a lumesh shell script helper",
 }
 ```
 3. 快捷键绑定命令配置。
 ```bash
 # ====== key bindings
 # NONE:0, SHIFT:2, ALT:4, CTRL:8,
 # ALT_SHIFT:6, CTRL_SHIFT: 10, CTRL_ALT:12, CTRL_ALT_SHIFT:14
 let LUME_HOT_MODIFIER = 4
 let LUME_HOT_KEYS = {
     q: "exit",
     c: "clear",
     h: "Fs.read ~/.cache/.lume_history | String.lines() | Ui.pick('select history:') ?! | exec_str()",
     x: "Fs.read /tmp/bookmark | String.lines() | Ui.pick('select bookmark:') ?! | exec_str()",
     m: 'let cmd := "$CMD_CURRENT";let s = Into.str(cmd); if s {s+"\n" >> /tmp/bookmark;println "\t[MARKED]"}',
 }
 ```
 4. 命令缩写配置。
 ```bash
 # ====== abbreviations
 let LUME_ABBREVIATIONS = {
     xi: 'doas pacman -S',
     xup: 'doas pacman -Syu',
     xq: 'pacman -Q',
     xs: 'pacman -Ss',
     xr: 'doas pacman -Rs',
 }
 ```
 5. 命令别名配置。
 ```bash
 # ====== alias
 alias int = Into.int()
 alias str = Into.str()
 alias each = List.map()
 alias sort = List.sort()
 alias group = List.group()
 alias table = Parse.cmd()
 alias format = String.format()
 alias ll = Fs.ls -l
 alias lsx = ls -l --time-style=long-iso
 alias pls = Parse.cmd(mode,hlink,user,group,size,mday,mtime,name)
 alias join = List.join()
 alias chars = String.chars()
 alias open = Fs.read()
 ```
 6. 历史记录路径配置。
 ```bash
 # ====== history file
 let LUME_HISTORY_FILE = "/tmp/lume_histroy"

 ```
 7. prompt配置。
    ```bash
        # MODE: 1=use template; 2=use starship; 0=use default.
        let LUME_PROMPT_SETTINGS = {
            MODE: 1,
            TTL_SECS: 2
        }
    ```
    命令行提示符，可以工作在如下模式：
    - 0，默认模式
    - 1, 模板模式, 模板可以是普通表达式，或函数。

      TTL用于控制缓存更新频率，以秒为单位。

      通常，普通表达式更节省资源；
      ```bash
      # 模板支持如下变量: $CWD, $CWD_SHORT
      let LUME_PROMPT_TEMPLATE = (String.blue($CWD_SHORT) + String.yellow(String.bold(">> ")))
      ```
      函数则可以用来渲染更复杂的提示符，比如git分支显示。函数模板会比普通模板消耗稍微多一点的资源。

      ```bash
      let LUME_PROMPT_TEMPLATE := x -> {
          String.format "{} {}{}{} " String.blue(x) String.green(String.bold("|")) \
          (if (Into.exists '.git') {git branch --show-current | String.cyan()} else "") \
          String.green(String.bold(">"))
      }

      ```
    - 2, starship 模式

 8. 欢迎信息
 ```bash
 # ====== welcome msg
 let LUME_WELCOME= "Welcome to Lumesh!"
 ```

 9. 启用VI模式
 ```bash
 LUME_VI_MODE = true
 ```

 10. 启用严格模式
 ```bash
    # ====== default strict mode
    let STRICT = true
 ```
 该设置具有最低优先级，可能被命令参数`-s`覆盖，或脚本指定模式覆盖。

 11. 控制直接打印模式
 ```bash
    # ====== default strict mode
    let STRICT = true
 ```
 该模式控制是否直接打印算术通道的运算结果和类型。默认为启用。例如，输入`5`回车后您将看到：
  ```bash
  >> [Integer] <<
  5
  ```

 12. sudo命令补全
 ```bash
    # ====== default strict mode
    let LUME_SUDO_CMD = "doas"
 ```
 按下`Alt+s`会自动添加sudo命令，如果您使用`doas`或其他sudo命令，可配置此项。默认值为`sudo`

 13. 配置`PATH`环境变量
 ```bash
 PATH = "~/.local/bin:" + $PATH
 ```

 14. 配置`IFS`
 ```bash
 IFS = "\n"
 # IFS affect: 0:never; 2:cmd args; 4:for;  8:string.split; 16:csv; 32:pick; 62:all
 let LUME_IFS_MODE=2
 ```
 后者用于细化控制IFS在哪些地方启用。

 15. 配置全局模块路径
 ```bash
 let LUME_MODULES_PATH=/opt/mods
 ```
当导入模块时，会优先从当前路径的`./mods/`,`./`下搜寻，最后从全局路径搜寻。

 16. 配置最大循环深度
 ```bash
 let LUME_MAX_SYNTAX_RECURSION = 100   # 语法嵌套深度
 let LUME_MAX_RUNTIME_RECURSION = 800  # 执行深度
 ```
 该设置不会影响循环语句的执行。仅当您看到系统提示需要增加深度时才需要设置。

 13. 高亮主题配置
 ```bash
 # ====== base theme: 'one_dark', 'ayu_dark', 'light'
 LUME_THEME = 'ayu_dark'

 # ====== theme modify
 LUME_THEME_CONFIG = {
    keyword: "\x1b[38;5;170m",      # 紫色 (#C678DD)
    #    ...
 }

 ```
 `LUME_THEME`用于设置基础主题；

 `LUME_THEME_CONFIG` 用于对基础主题做修改，其中
 可配置的项有：
 ```bash
 # One Dark 核心语法颜色 - 每种都使用不同的颜色
  keyword,
  value_symbol,
  operator,
  operator_prefix,
  operator_infix,
  operator_postfix,

  # 字符串相关颜色
  string_raw,
  string_template,
  string_literal,
  string_error,

  # 数字和字面量
  number_literal,
  number_error,
  integer_literal,
  float_literal,

  # 符号和标识符
  symbol_none,
  builtin_cmd,
  symbol,

  # 注释和标点
  comment,
  punctuation,

  # REPL 和交互相关
  command_valid,
  hint,
  completion_cmd,
  completion_ai,

  # Time token 颜色
  time,

  # Regex token 颜色
  regex,
 ```

详细配置项可查看默认配置。

 - 配置文件路径
 如果没有在命令行指定配置文件路径，lume会读取默认配置文件，在默认路径下的子路径 `lumesh/config.lm`.

默认路径:

|平台 | 路径                                 | 举例                                  |
| ------- | ------------------------------------- | ---------------------------------------- |
| Linux   | `$XDG_CONFIG_HOME` or `$HOME`/.config | /home/alice/.config                      |
| macOS   | `$HOME`/Library/Application Support   | /Users/Alice/Library/Application Support |
| Windows | `{FOLDERID_RoamingAppData}`           | C:\Users\Alice\AppData\Roaming           |

执行 `Into.dirs()` 可以查看您的默认路径。


- 历史记录路径
如果没有在配置文件中指定，将被保存在默认路径下。

linux/macos平台的文件名为 `.lume_history`.
windows平台的文件名为 `lume_history.log`.

默认路径 :

|平台 | 路径                               | 举例                      |
| ------- | ----------------------------------- | ---------------------------- |
| Linux   | `$XDG_CACHE_HOME` or `$HOME`/.cache | /home/alice/.cache           |
| macOS   | `$HOME`/Library/Caches              | /Users/Alice/Library/Caches  |
| Windows | `{FOLDERID_LocalAppData}`           | C:\Users\Alice\AppData\Local |

执行 `Into.dirs()` 可以查看您的默认路径。


## 十三、内置函数库

Lumesh内置大量实用的函数库, 以实现便捷的函数式编程，如
- **集合操作**: `List.reduce, List.map`
- **文件系统**: `Fs.ls, Fs.read, Fs.write`
- **字符串处理**: `String.split, String.join`、正则模块、格式化模块
- **时间操作**: `Fs.now, Fs.format`
- **数据转换**: Into模块,From模块
- **数学计算**: 完整的数学函数库
- **日志记录**: Log模块
- **UI操作**: `Ui.pick, Ui.confirm`

可通过 `help` 命令查看可用模块和函数。
可通过 `help String` 命令查看具体模块的函数。

> 内置函数支持三种调用方式
  ```bash
  String.red(msg)
  String.red msg
  String.red! msg
  ```
以及链式调用和管道方法调用：
  ```bash
  msg.red()
  msg | .red()
  ```

详细内容，请继续阅读:
 - [内置Lib库](/zh-cn/libs/index)

## 十四、模块导入
当需要多个脚本文件协同完成复杂任务时，可导入其他模块。
语法：
  ```bash
  use module_path
  use module_path as alias
  ```

**请注意**
- 模块名应唯一：
    所有导入的模块（包括子孙模块），不能重名。如有，请使用`as name`重新命名。

    这是综合考虑性能和便捷性的权衡，为了更高的性能，我们决定接受这个小小的不便。

- 模块应作为工具函数使用：
    导入模块时，只会读取`fn`定义和`use`语句，函数定义之外的语句将被忽略。以避免意外的代码运行。

详细内容，请继续阅读:
 - [内置Lib库](../libs)
 - [Bash语法对比](/zh-cn/glance/bash)
