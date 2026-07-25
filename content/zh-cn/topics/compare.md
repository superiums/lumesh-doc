+++
date = '2026-07-25T13:47:17+08:00'
title = '从Bash到Lume（一）'
weight = 2
+++
 

## 一、类型系统：从"一切皆字符串"到强类型

Bash 的问题：所有变量本质上是字符串，数值运算需要  $(( ))  或  expr ，类型错误只在运行时才暴露，且错误信息极差。

Lume 的解决方案：拥有完整的原生类型系统，类型无需声明(由赋值时自动侦测)，类型之间支持自动强制转换（如  Integer + Float → Float ），并且运算符被重载以支持多类型语义（如  List + List  是拼接， BSet + BSet  是并集）。
 

## 二、变量与赋值：声明与赋值分离

Bash 的问题： x=1  和  x=hello  没有区别，没有声明语义， local  只在函数内有效。

Lume 的解决方案：

```bash 
let x = 10          # 声明（Declare）
x = 20              # 赋值（Assign）
set x = 30             # 修改父作用域变量（SetParent）
export PATH = ...   # 导出到系统环境变量
del x               # 删除变量
x := 2 + 3          # 延迟赋值（不立即求值，存储表达式本身）
eval(x)             # 手动求值延迟表达式 → 5
```

严格模式（`STRICT` flag）下，对未声明变量赋值会报错 `UndeclaredVariable`，类似 `set -u`，但粒度更细。  

---

## 三、解构赋值：JavaScript 风格

**Bash 的问题**：无法从数组或关联数组中一次性解包多个变量，需要手动索引。

**Lume 的解决方案**：

```bash
# 列表解构
let [a, b] = [1, 2]
let [first, *rest] = [1, 2, 3, 4]   # rest = [2, 3, 4]

# Map 解构
let user = {name: "lume", age: 25}
let {name, age} = user               # name="Alice", age=25
let {name, age: years} = user        # 重命名：years=25

# 多变量同时赋值
let a, b = 1
```

解构支持 `List`、`BSet`、`Map`、`HMap` 四种集合类型，并且在块作用域内（`IN_LOCAL` 标志）会自动使用局部变量存储。

---

## 四、函数系统：从原始到现代

**Bash 的问题**：函数没有默认参数、没有类型、没有闭包、没有高阶函数，参数只能通过 `$1 $2 $@` 访问。

**Lume 的解决方案**：

### 4.1 箭头 Lambda 与闭包

```bash
let double = x -> x * 2
let add = (x, y) -> x + y

# 闭包：自动捕获自由变量
let base = 10
let adder = x -> x + base   # 捕获 base
 

Lambda 通过  get_free_variables()  分析函数体，自动捕获所有自由变量到  captured_env 。 6

4.2 具名函数：默认参数 + 可变参数

 
fn greet(name, greeting="Hello") {
    print greeting name
}

fn sum(*nums) {    # 可变参数收集到 nums 列表
    nums | list.foldl((x, acc) -> x + acc, 0)
}
```

### 4.3 柯里化（部分应用）

```bash
let add = (x, y) -> x + y
let add5 = add(5)    # 返回新 Lambda，等待第二个参数
add5(3)              # → 8
```

### 4.4 装饰器（中间件模式）

```bash
@logger("debug")
@timer
fn my_function(x) {
    x * 2
}
```
 
装饰器返回  [before_fn, after_fn]  列表，执行顺序为： logger.before → timer.before → 函数体 → timer.after → logger.after ，装饰器环境中可访问  NAME 、 ARGS 、 RESULT  变量。 9

 

## 五、错误处理：7 种精细操作符

Bash 的问题：只有  || 、 && 、 set -e 、 trap ERR ，无法区分"忽略错误"、"捕获错误信息"、"转换为布尔值"等场景，错误信息也极其有限。

Lume 的解决方案：7 种后缀错误捕获操作符：

```bash
cmd ?.          # 忽略错误，返回 none
cmd ?:  handler # 将错误信息（Map）传给 handler 函数
cmd ?+          # 打印错误到 stdout，返回 none
cmd ??          # 打印错误到 stderr（红色），返回 none
cmd ?>          # 将错误转为 Map 结构体，可编程处理
cmd ?!          # 遇错终止整个进程
cmd ?~          # 成功→true，失败→false（用于条件判断）
```

- 实用模式
`validate() ?~ && process() ?~ || cleanup()   # 类 bash 的 && ||`
`result = risky() ?: (e) -> { print e.msg; default_value }`
 

错误 Map 包含  code 、 msg 、 expr 、 ast 、 type 、 depth  字段，可编程检查。
 

## 六、管道系统：从文本流到结构化数据

Bash 的问题：管道只能传递文本字节流，结构化数据（JSON、CSV）需要借助  jq 、 awk  等外部工具，且无法在管道中传递数组或字典。

Lume 的解决方案：4 种管道类型：

```bash
data | process              # 标准管道：支持结构化数据（List/Map 直接传递）
data | positional a _ c     # 位置管道：_ 是占位符，数据注入指定位置
data |> transform           # 分发管道：对集合每个元素分别应用右侧函数
data |^ interactive         # PTY 管道：用于 vi/ssh/htop 等交互式程序

# 结构化管道示例
ls -l | .to_table() | where(size > 5K)
[1,2,3,4,5] | list.filter(x -> x > 2) | list.map(x -> x * x)
ls -1 |> cp -r _ /tmp/     # 对每个文件执行 cp
```


---

## 七、控制流：表达式化 + 模式匹配

**Bash 的问题**：`if`/`for`/`while` 是语句，不返回值；`case` 只能匹配字符串模式；没有 `match` 表达式。

**Lume 的解决方案**：

```bash
# if 是表达式，可赋值
let result = if x > 0 { "positive" } else { "non-positive" }

# for 在赋值/管道上下文中返回 List
let squares = for i in 1..10 { i * i }

# 带索引的 for
for i, item in my_list { print i item }

# match 支持多种模式
match value {
    1 => "one"
    2..5 => "two to four"
    r'\d+' => "a number"    # 正则匹配
    _ => "other"
}

# loop 无限循环 + break 返回值
let x = loop {
    let v = get_value()
    if v > 10 { break v }
}
```

---

## 八、方法链式调用

**Bash 的问题**：字符串操作需要嵌套命令替换 `$(echo $(echo $x | tr ...))` 或多行管道，可读性极差。

**Lume 的解决方案**：

```bash
"hello world".split(' ').join(',')    # → "hello,world"
data | .filter(x -> x > 0)           # 管道中的链式方法
[3,1,2].sort().rev()                  # 链式集合操作
```

 Chain  表达式将基础值与一系列  ChainCall  组合，每步结果作为下一步的输入。
 

## 九、模块系统

Bash 的问题：只有  source  命令，没有命名空间，容易产生变量/函数名冲突。

Lume 的解决方案：

```bash
use mymodule as mm
mm::my_function()
mm::sub::nested_func()    # 嵌套模块路径

# 17 个内置模块
list.map(...)
string.split(...)
fs.read("file.txt")
time.now()
math.sqrt(16)
regex.match(r'\d+', text)
```

模块通过 `LazyModule` 懒加载，首次使用时才初始化，减少启动开销。

---

## 十、范围操作符

**Bash 的问题**：`{1..10}` 只能用于 brace expansion，不能作为值传递，不支持步长（需要 `seq`）。

**Lume 的解决方案**：

```bash
1..10           # 半开区间 [1, 10)，惰性 Range 对象
1..=10          # 闭区间 [1, 10]
1..10:2         # 步长为 2：1, 3, 5, 7, 9
_..5            # 从 Int::MIN 到 5
1.._            # 从 1 到 Int::MAX
_.._            # 全整数范围

# Range 可直接迭代、切片、管道
for i in 1..100 { ... }
let evens = 0..20:2 | list.filter(x -> x % 3 == 0)
arr[1..5]       # 切片
arr[1.._:2]     # 步长切片
```

---

## 十一、CFM（命令优先模式）：解决 Shell 二义性

**Bash 的问题**：bash 本身就是命令优先，但这导致编程语法极其受限（如 `1+2` 不能直接计算，`[` 是命令而非语法）。

**Lume 的解决方案**：双模式 tokenizer：

- **CFM 模式**（交互式默认）：`ping 1.1.1.1`、`chmod +x file` 等命令行用法自然工作，`1.1.1.1` 被识别为 Symbol 而非浮点数
- **普通模式**（脚本默认）：完整编程语法，`1 + 2`、`3.14` 等正常解析
- 可用 `:` 前缀强制普通模式，`>` 前缀强制 CFM 模式  

---

## 十二、自定义操作符

**Bash 的问题**：完全不支持自定义操作符。

**Lume 的解决方案**：

```bash
let ..+ = (x, y) -> x + y + x * y
3 ..+ 5    # → 23，调用自定义二元操作符
```

以  ..  开头的标识符可作为自定义中缀操作符注册到环境中。
 

## 十三、后台执行与 IO 控制

Bash 的问题：只有  &  后台执行，stderr/stdout 重定向语法晦涩（ 2>&1 ）。

Lume 的解决方案：后缀修饰符：


```bash
long_task &         # 后台执行
noisy_cmd &?        # 静默 stderr
verbose_cmd &-      # 静默 stdout
both_cmd &.         # 静默 stdout 和 stderr
merge_cmd &+        # 合并 stderr 到 stdout
```

---

## 十四、作用域系统

**Bash 的问题**：默认全局作用域，`local` 只在函数内有效，没有块级作用域。

**Lume 的解决方案**：两层作用域系统：

1. **Environment**：父子链式词法作用域，`fork()` 创建子作用域
2. **State 局部变量**：`IN_LOCAL` 标志激活块级局部变量，`for` 循环变量、lambda 参数、块内 `let` 都是局部的  

---

## 十五、字符串系统

**Bash 的问题**：只有双引号（变量展开）和单引号（原始），没有模板字符串，转义规则复杂且不一致。

**Lume 的解决方案**：三种字符串类型：

```bash
'C:\path\to\file'   # 单引号：原始字符串，只转义 \'
"hello\nworld"      # 双引号：支持转义序列 \n \t \u{...} ANSI 颜色码
`output: $var`      # 反引号：模板字符串，支持转义和变量插值
```

---

## 总结对比

 
```bash
特性                    Bash        Lume
─────────────────────────────────────────────
类型系统                字符串      12+ 原生类型
解构赋值                ✗           ✓ (List/Map/HMap/BSet)
Lambda/闭包             ✗           ✓ (自动捕获自由变量)
默认参数                ✗           ✓
可变参数                $@          ✓ (*collector)
柯里化                  ✗           ✓ (自动)
装饰器                  ✗           ✓ (before/after hooks)
错误处理操作符          2种(&&/||)  7种
结构化管道              ✗           ✓
管道类型                1种         4种(|/|>/|^/重定向)
模式匹配                case(字符串) match(多类型+正则+范围)
Range 类型              ✗           ✓ (步长/无界/切片)
模块系统                source      use + 命名空间 + 懒加载
方法链式调用            ✗           ✓
自定义操作符            ✗           ✓ (..前缀)
块级作用域              ✗           ✓
PTY 管道                ✗           ✓ (|^)
AI 补全                 ✗           ✓
```
