---
title: 语法：管道与错误
date: 2025-06-11 19:16:45
highlight: true
weight: 70
tags:
 - syntax
categories:
 - wiki
 - syntax
---

> 管道、重定向、错误、日志

## 九、管道与重定向

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
      > 如果是函数则作为 *首位* 参数传递。

|   数据          |  函数、运算、内置命令  |     三方命令       |
|-----------------|------------------------|--------------------|
|   输人（左侧）  |   读取结构化数据通道   |    读取标准输出    |
|   输出（右侧）  |   输出到第一个参数     |    输出到标准输入  |


  **占位符`_`**
  
    管道到右侧函数的指定位置，以`_`为占位符，如果未指定，则插入到第一个参数位置。
    多数情况下不需要手动指定。使用`|`足够。
    但如果右侧的命令无法读取标准输入，或需要指定参数位置时，需要使用此管道。

  ```bash
  2 | print 1 _ 3               # 打印结果： 1 2 3
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
  print 3+5 | bat             # print语句打印到标准输出，同时传递出none作为运算结果，bat捕捉到的是print语句的运算结果none

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
  fs.ls -l | where(size > 5K) | select(name,size,modified)

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
  fs.ls -l | where( fs.diff('d',modified) > 3 ) | sort(size,name)

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
  fs.ls -l | group 'type'     # type是函数名，所以引号不能省略

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
> 可以使用from.cmd 或 into.table 或链式调用 .table()，将文本转换结构化数据。可接收列名序列作为参数。

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


## 十、错误处理

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
  `fs.ls -l | ui.pick ?! |_ cp _ /tmp`
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
  如需进一步调试，可以使用`debug` 和 `ddebug` , `typeof` 命令。

  ```bash
  # 简单数据调试：
  let c = 0..8
  debug c                     # 输出：Range〈0..9,1〉

  # 复杂语句调试：
  let a = fs.ls -l
  /home/tix |> debug a

  # 输出：
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
- Log 模块
  Log模块的使用也有利于错误调试，具体请参考[Log模块文档](/zh-cn/doc/libs/log)
