---
title: 语法：函数与命令
date: 2025-06-11 19:16:45
highlight: true
weight: 60
tags:
 - syntax
categories:
 - wiki
 - syntax
---
> 函数、命令

## 六、函数

### **fn函数定义**
   - 使用 `fn` 定义，支持*默认参数*，支持*剩余参数收集*，支持装饰器。
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
      + lambda 不支持装饰器。
      + lambda *支持部分参数应用*，返回后续lambda
      + lambda 支持闭包捕获。
      
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
`string, list, map, time, integer, float, range`

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
**装饰器函数必须返回[before,after]列表，如为空可以用占位符`_`替代**

## 七、运行系统命令

### 命令调用

在lumesh中，您可以像在其他shell中一样，便捷的运行程序，如
  ```bash
  ls                  # 仅限CFM模式
  ls -l
  ```

- 多条命令，前面的命令失败则后续的命令不会继续执行；
- 除非错误已经处理：

  ```bash
  ls '/0' ; ls -l         # 后者不会执行
  ls '/0' ?. ; ls -l      # 后者会执行
  ```
- 使用`^`后缀强制绕过变量解析
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
  ls  /o '/' &.            # shutdown stdout and stderr

  ls  /o '/' &? | bat            # shutdown stderr and pipe stdout to next cmd.

  ```
下面是和bash的语法对比：

| 任务         |  lumesh  | bash               |
|--------------|----------|--------------------|
| 后台运行     |   cmd &  |cmd &               |
| 关闭标准输出 |   cmd &- |cmd 1> /dev/null    |
| 关闭错误输出 |   cmd &? |cmd 2> /dev/null    |
| 关闭所有输出 |   cmd &. |cmd 2>&1 > /dev/null|
| 输出错误到标准* |   cmd &+ |cmd 2>&1      |

[^1]: *todo

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

## 八、内置函数库

Lumesh内置大量实用的函数库, 以实现便捷的函数式编程，如
- **集合操作**: `list.reduce, list.map`
- **文件系统**: `fs.ls, fs.read, fs.write`
- **字符串处理**: `string.split, string.join`、正则模块、格式化模块
- **时间操作**: `fs.now, fs.format`
- **数据转换**: into模块,from模块
- **数学计算**: 完整的数学函数库
- **日志记录**: log模块
- **ui操作**: `ui.pick, ui.confirm`

可通过 `help` 命令查看可用模块和函数。
可通过 `help string` 命令查看具体模块的函数。

> 内置函数支持三种调用方式
  ```bash
  string.red(msg)
  string.red msg
  string.red! msg
  ```
以及链式调用和管道方法调用：
  ```bash
  msg.red()
  msg | .red()
  ```

详细内容，请继续阅读:
 - [内置Lib库](/zh-cn/doc/libs/)
