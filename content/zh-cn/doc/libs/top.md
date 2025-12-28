---
title: Lumesh 内置函数库
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - top
categories:
 - wiki
 - libs
---

### Shell 控制函数

#### 进程控制
**`exit [status]`** - 退出 shell 程序
- **参数**：
  - `status` (可选): `Integer` - 退出状态码，默认为 0
- **示例**：
  - `exit` - 正常退出
  - `exit 1` - 以状态码 1 退出

#### 目录导航
**`cd [path]`** - 切换当前工作目录
- **参数**：
  - `path` (可选): `String|Symbol` - 目标目录路径，支持 `~` 家目录展开，默认为当前目录
- **示例**：
  - `cd "/home/user"` - 切换到绝对路径
  - `cd "~/Documents"` - 切换到家目录下的 Documents
  - `cd` - 切换到家目录

**`pwd`** - 显示当前工作目录
- **参数**：无
- **返回**：`String` - 当前目录的绝对路径

#### 环境变量管理
**`set <var> <val>`** - 在根环境中定义变量
- **参数**：
  - `var` (必需): `String|Symbol` - 变量名
  - `val` (必需): `Any` - 变量值
- **示例**：`set "PATH" "/usr/bin:/bin"`

**`unset <var>`** - 从根环境中删除变量
- **参数**：
  - `var` (必需): `String|Symbol` - 要删除的变量名

### 输入输出函数

#### 标准输出
**`print <args>...`** - 打印参数到标准输出，参数间用空格分隔，不换行
- **参数**：
  - `args` (可变): `Any...` - 要打印的值列表
- **示例**：`print "Hello" "World" 123`

**`println <args>...`** - 打印参数到标准输出，参数间用空格分隔，末尾添加换行
- **参数**：
  - `args` (可变): `Any...` - 要打印的值列表
- **示例**：`println "Line1" "Line2"`

**`tap <args>...`** - 打印参数并返回结果，用于调试管道
- **参数**：
  - `args` (可变): `Any...` - 要打印和返回的值
- **返回**：单个参数时返回该值，多个参数时返回列表
- **用途**：在管道中间插入调试输出而不影响数据流

#### 错误输出
**`eprint <args>...`** - 输出到标准错误流，不换行
**`eprintln <args>...`** - 输出到标准错误流，每个参数一行
- **参数**：
  - `args` (可变): `Any...` - 要输出的值列表
**`throw <string>`** - 抛出错误
- **参数**：
  - `msg` (必需): `String` - 要抛出的错误消息


#### 调试和格式化输出
**`debug <args>...`** - 打印参数的调试表示形式（类似 Rust 的 `{:?}` 格式）
- **参数**：
  - `args` (可变): `Any...` - 要调试打印的值
- **用途**：显示数据的内部结构，便于调试

**`pprint <list>|<map>`** - 美化打印结构化数据（列表、映射等）
- **参数**：
  - `data` (必需): `List|Map` - 要美化打印的结构化数据
- **用途**：以易读格式显示复杂数据结构

#### 用户输入
**`read [prompt]`** - 获取用户输入
- **参数**：
  - `prompt` (可选): `String` - 输入提示信息
- **返回**：`String` - 用户输入的内容
- **示例**：`read "Enter your name: "`

### 数据操作函数

#### 数据访问
**`get <path> <map|list|range>`** - 使用点号路径从嵌套结构中获取值
- **参数**：
  - `path` (必需): `String|Symbol|Integer` - 访问路径，支持点号分隔
  - `data` (必需): `Map|List|Range` - 要访问的数据结构
- **示例**：
  - `get "user.name" data` - 获取嵌套映射中的值
  - `get "items.0.title" data` - 获取列表中第一个元素的 title 字段

    ```bash
    let nested = {a: {b: {c: [42,43]}},r:[0..3,10..15]};
    get "a.b.c.0" nested  # 返回 42
    get "a.r.0" nested    # 返回 0..3
    get "a.r.0.1" nested  # 返回 1
    ```

**`type <value>`** - 获取数据类型
- **参数**：
  - `value` (必需): `Any` - 要检查类型的值
- **返回**：`String` - 类型名称
- **示例**：`type 123` 返回 "Integer"

**`len <collection>`** - 获取集合长度
- **参数**：
  - `collection` (必需): `String|List|Map|HMap|Bytes|Range` - 要计算长度的集合
- **返回**：`Integer` - 集合的长度
- **支持类型**：字符串（字符数）、列表、映射、哈希映射、字节数组、范围

#### 数据修改
**`insert <key/index> <value> <collection>`** - 向集合中插入项
- **参数**：
  - `key` (必需): `String|Integer` - 插入位置（映射的键或列表的索引）
  - `value` (必需): `Any` - 要插入的值
  - `collection` (必需): `Map|List` - 目标集合
- **返回**：修改后的新集合
    ```bash
    insert 0 "X" ["A", "B"]  # 返回 ["X" "A" "B"]
    insert "key" 42 {}      # 返回 {key: 42}
    ```

**`rev <string|list|bytes>`** - 反转序列
- **参数**：
  - `sequence` (必需): `String|List|Bytes` - 要反转的序列
- **返回**：反转后的新序列

**`flatten <collection>`** - 展平嵌套结构
- **参数**：
  - `collection` (必需): `List|Map` - 要展平的嵌套结构
- **返回**：`List` - 展平后的列表
- **用途**：将嵌套的列表或映射展开为单层列表

#### 数据查询
**`where <condition> <list[map]>`** - 按条件过滤行
- **参数**：
  - `condition` (必需): `Expression` - 过滤条件表达式
  - `data` (必需): `List[Map]` - 要过滤的映射列表
- **返回**：`List[Map]` - 符合条件的行
- **特殊变量**：
  - `LINENO` - 当前行号（从 0 开始）
  - `LINES` - 总行数
- **示例**：`where (age > 18) users`
    ```bash
    Fs.ls -l | where(size<1K)
    Fs.ls -l | where(LINENO>1)
    ```

**`select <columns>...<list[map]>`** - 从映射列表中选择列
- **参数**：
  - `columns` (可变): `String...` - 要选择的列名
  - `data` (必需): `List[Map]` - 源数据（映射列表）
- **返回**：`List[Map]` - 只包含指定列的新列表
- **示例**：`select "name" "age" users`

    ```bash
      Fs.ls -l | select(name,size)
    ```

### 执行控制函数

#### 表达式求值
**`eval <expr>`** - 求值表达式
- **参数**：
  - `expr` (必需): `Expression` - 要求值的表达式
- **用途**：动态执行表达式

**`exec_str <string>`** - 求值字符串
- **参数**：
  - `string` (必需): `String` - 包含代码的字符串
- **用途**：执行字符串形式的代码

**`exec <expr>`** - 在当前环境中求值
- **参数**：
  - `expr` (必需): `Expression` - 要在当前环境中执行的表达式

**`repeat <count> <expr>`** - 重复执行表达式指定次数
- **参数**：
  - `count` (必需): `Integer` - 重复次数
  - `expr` (必需): `Expression` - 要重复执行的表达式
- **返回**：`List` - 包含所有执行结果的列表
- **用途**：重复执行表达式并收集结果
- **示例**：`repeat 3 (Math.rand())` - 生成3个随机数的列表


#### 文件执行
**`include <path>`** - 在当前环境中执行文件
- **参数**：
  - `path` (必需): `String` - 要执行的文件路径
- **用途**：将文件内容在当前环境中执行，变量修改会影响当前环境

**`import <path>`** - 在新环境中执行文件
- **参数**：
  - `path` (必需): `String` - 要执行的文件路径
- **用途**：在独立环境中执行文件，不影响当前环境

如需模块化编程，建议使用`use`语句。

`include`和`import`相当于直接把代码嵌入当前文件。

而`use`采用了命名空间。

### 帮助系统

**`help [module]`** - 显示帮助信息
- **参数**：
  - `module` (可选): `String` - 要查看帮助的模块名
  - `libs/tops`     - 列出模块/顶级函数
- **行为**：
  - 无参数：显示所有可用模块列表
  - 有参数：显示指定模块的详细函数列表
- **示例**：
  - `help` - 显示所有模块
  - `help Math` - 显示数学模块的函数

**动态提示**： 输入`String. ` 会动态提示该模块的可用函数

## Notes

这些顶级内置函数构成了 Lumesh shell 的核心功能框架。参数类型说明中，`<>` 表示必需参数，`[]` 表示可选参数，`...` 表示可变参数。所有函数都有完整的参数验证和错误处理机制，确保类型安全和用户友好的错误提示。
