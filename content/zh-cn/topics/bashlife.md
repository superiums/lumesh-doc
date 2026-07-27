---
title: Bash中的泥泞人生
date: 2026-07-27 21:00
---

> 本文系统梳理 Bash 的常见陷阱与反直觉设计，并对比 Lume 的解决思路。

## 一、语法基础

### 空格敏感性

Bash 中 `[` 是一个独立命令，条件表达式的空格不可省略：

```bash
# ❌ 错误
if[$a==$b]; then ...

# ✅ 正确
if [ "$a" == "$b" ]; then ...
```

- `[` 是命令，不是语法符号
- `=` 两侧必须有空格

**Lume 的轻松：**

空格完全可选，表达式直接书写：

```bash
a == b
if $a == $b { ... }
```

---

### 赋值语法

Bash 赋值不允许有空格，否则 `a` 会被当作命令执行：

```bash
# ❌
a = 1

# ✅
a=1
```

**Lume 的轻松：**

空格可选，两种写法均合法：

```bash
a=1
a = 1    # 等价
```

*CFM 模式下需要空格。*

---

### 字符串与引号系统

#### 变量展开与隐式分割

Bash 中不加引号的变量会触发隐式的单词分割（word splitting）和 glob 展开：

```bash
file="a b"

# ❌ 被解析为 rm a b，删除两个文件
rm $file

# ✅
rm "$file"
```

*不加引号 = 隐式 split + glob*

**Lume 的轻松：**

默认 IFS 为 `\n`，不对空格分割。分割行为由 `LUME_IFS_MODE` 掩码按场景精细控制：

```bash
rm $file   # 解析为 rm 'a b'，符合直觉
```

Bash 的 `IFS` 是全局的，影响所有字符串；Lume 使用 `LUME_IFS_MODE` 独立控制各场景：

```bash
# IFS affect: 0:never; 2:cmd args; 4:for; 8:string.split; 16:csv; 32:pick; 62:all
set LUME_IFS_MODE = 2    # 仅在命令参数中分割，其他场景不受影响
```

| 位 | 场景 | 含义 |
|-----|---------|---------|
| `1<<1` | `IFS_CMD` | 命令字符串参数分割 |
| `1<<2` | `IFS_FOR` | `for i in str` 迭代分割 |
| `1<<3` | `IFS_STR` | `string.split` 默认分隔符 |
| `1<<4` | `IFS_CSV` | CSV 解析 |
| `1<<5` | `IFS_PCK` | `ui.pick` 选项分割 |

#### 字符串类型

Bash 只有双引号（变量展开）和单引号（原始字符串），没有模板字符串：

```bash
str="hello world"
echo "$str" | tr '[:lower:]' '[:upper:]'
```

**Lume 的轻松：**

三种字符串类型，各司其职：

```bash
'c:\path\to\file'   # 单引号：原始字符串，仅转义 \'
"hello\nworld"      # 双引号：支持转义序列 \n \t \u{...} 及 ANSI 颜色码
`output: $var`      # 反引号：模板字符串，支持转义与变量插值
```

模板字符串支持任意表达式：

```bash
let name = "Alice"
let age = 25
`Hello, $name! You are {age * 2} years old in dog years.`
# → "Hello, Alice! You are 50 years old in dog years."
```

#### 引号地狱

Bash 的字符串插值本质上是"字符串生成规则"，嵌套超过两层便难以维护：

```bash
echo "Today is $(date "+%Y-%m-%d")"
sed "s/foo/bar \"$var\"/g" file.txt
curl -X POST -d "{\"name\":\"$name\",\"msg\":\"Hello '$msg'\"}" http://example.com
```

**Lume 的轻松：**

使用反引号进行插值，内部引号直接书写，无嵌套转义问题：

```bash
print `Today is {date '+%Y-%m-%d'}`
sed `s/foo/bar "$var"/g` file.txt
let data = {name, msg: `Hello '$msg'`}
curl -X POST -d into.json($data) http://example.com
```
#### 多行文本
2. Heredoc 与多行字符串

Bash 的 heredoc 语法繁琐：

```bash
cat <<EOF  
line1  
line2  
EOF
```

**Lume 的轻松：**
lume的三种字符串引号均直接支持多行文本
```bash
let a = 'line1
line2'
```

#### 大小写操作

Bash 大小写转换语法晦涩：

```bash
[[ "${a,,}" == "${b,,}" ]]
```

**Lume 的轻松：**

```bash
a.to_lower() == b.to_lower()
```

---

## 二、类型系统

### 默认字符串类型

Bash 中所有变量默认是字符串，算术运算需要特殊语法：

```bash
a=1
b=2
c=$a+$b
echo $c   # 1+2（字符串拼接，非加法）
```

✅ 必须：

```bash
((c=a+b))
c=$((a+b))
```

**Lume 的轻松：**

变量有明确类型，运算直接书写，无需特殊语法。

---

### 数字类型

#### 浮点数

Bash 不支持浮点运算，只能借助外部工具：

```bash
echo $((1/2))   # 0（整除）
echo "scale=2; 1/2" | bc   # 0.50
```

#### 整数溢出

Bash 整数溢出静默发生，不报错：

```bash
echo $((99999999999999999999999))   # 莫名其妙的数
```

**Lume 的轻松：**

整数、浮点全支持，溢出时明确报错：

```bash
3 + 5
20 / 2.0
a - b / c * d ^ 2
math.sin(x)

echo 99999999999999999999999
# syntax error: expect Integer, found error: number too large to fit in target type
```

---

### 数组与映射

#### 语法繁琐

Bash 数组需要记忆大量特殊符号：

```bash
arr=(a b c)

echo $arr              # a（只取第一个元素）
echo ${arr[@]}         # a b c
echo "${arr[0]}"       # a
echo "${arr[-1]}"      # 最后一个元素（Bash 4.3+）
echo "${#arr[@]}"      # 数组长度
echo "${arr[@]:1:2}"   # 切片：从第 1 个开始取 2 个

echo "${map["name"]}"
echo "${!map[@]}"      # 所有 key
echo "${#map[@]}"      # 元素个数
```

**Lume 的轻松：**

数组语法直观简洁：

```bash
arr = [a, b, c]
print arr
print arr[0]
print arr.len()
print arr[1..-1]    # 切片
```

#### 下标不自动重排

```bash
arr=(a b c)
unset arr[1]
echo "${arr[@]}"   # a c（下标仍为 0 2，非 0 1）
```

**Lume 的轻松：**

删除元素后索引自动重排：

```bash
arr = [a, b, c]
arr.remove_at(1)
# → [a, c]，下标为 0、1
```

#### 关联数组必须提前声明

```bash
map["a"]=1       # ❌ 普通数组
declare -A map
map["a"]=1       # ✅ 关联数组
```

**Lume 的轻松：**

数组与映射类型独立，语法直观：

```bash
a = [1, 2]          # 数组
m = {a: 1, b: 2}    # 映射
```

#### 嵌套访问

Bash 不支持嵌套数据结构的访问。

**Lume 的轻松：**

支持 SQL 风格的 `select` 与点路径 `get`：

```bash
# select：从 List[Map] 中选取列（类似 SQL SELECT）
fs.ls -l | select name size modified

# get：点路径访问嵌套结构
let config = {db: {host: "localhost", port: 5432}}
config | get "db.host"    # → "localhost"
get config "db.port"      # → 5432
```

---

### 特殊类型

Bash 中范围、正则、时间均以字符串表示，无专用类型。

**Lume 的轻松：**

#### 范围类型

```bash
1..10       # 半开区间 [1, 10)，惰性 Range 对象
1..=10      # 闭区间 [1, 10]
1..10:2     # 步长为 2：1, 3, 5, 7, 9
_..5        # 从 Int::MIN 到 5
```

#### 文件大小与百分比字面量

```bash
50%         # → 0.5（Float）
3M          # → FileSize(3MB)
1.5G        # → FileSize(1.5GB)

3M > 1G         # → false
filesize.b(1K)  # → 1024
```

#### 正则与时间字面量

```bash
r'\d+'
t'2026-7-23'
```

---

## 三、运算与条件判断

### 条件判断：`[ ]` vs `[[ ]]` vs `(( ))`

- `[ ]` 是外部命令，不支持正则和 `&&` `||`
- `[[ ]]` 是 Bash 内置，支持正则和逻辑运算符
- `(( ))` 用于整数运算

✅ *能用 `[[ ]]` 就不用 `[ ]`*

```bash
[[ "$a" =~ ^[0-9]+$ ]]
[[ "$a" > 0 && "$b" < 0 ]]
```

整数比较用 `-eq`，字符串比较用 `=`，语义容易混淆：

```bash
[ "$a" -eq "123" ]   # 整数比较
[ "$a" = 123 ]       # 字符串比较
(( a == 123 ))       # 更清晰
```

**Lume 的轻松：**

无需任何括号，表达式直接书写，`==` 统一比较所有类型：

```bash
a ~: r'\d+'     # 正则匹配
a > 0 && b < 0  # 逻辑运算
a == b          # 统一比较
```

---

### 正则与通配符

Bash 中正则和通配符语法不同，容易混淆：

```bash
[[ "$a" == *.log ]]     # 通配符
[[ "$a" =~ \.log$ ]]    # 正则
```

**Lume 的轻松：**

通配符仅用于命令和循环，比较中使用正则或字符串：

```bash
ls *.log
for f in *.log { ... }

$a ~: '.log'        # 字符串包含
$a ~: r'.log$'      # 正则匹配
# `~:` 还可检测集合/列表/范围/字典 key 是否包含右侧表达式
```

---

### 返回值语义

Bash 以退出码 0 表示成功、非 0 表示失败，与人类直觉相反。`if` 判断的是退出码，而非布尔值：

```bash
grep "abc" file.txt
echo $?   # 0 表示找到

if grep "abc" file.txt; then
    echo "found"
fi
```

✅ `true` = 0，`false` ≠ 0

**Lume 的轻松：**

直接判断内容，无需关心退出码：

```bash
let found = fs.read file.txt | .grep('abc')
if found {                # 等价于 if !found.is_empty()
    echo 'found'
}
```

---

## 四、流程控制

### 模式匹配

Bash 的 `case` 是通配符驱动的跳转表，存在诸多陷阱：

1. 每个分支必须以 `;;` 结尾（极易遗漏）
2. 还有 `;&` 和 `;;&` 两种鲜为人知的变体
3. 使用通配符而非正则（极易混淆）
4. 空字符串不匹配 `*`
5. 只能做等值/模式匹配，不能做逻辑组合

```bash
case $1 in
    a)
        echo "a"
        ;;&
    b|c)
        echo "bc"
        ;;
    (b|c)
        echo "ok"
        ;;
esac
```

**Lume 的轻松：**

`match` 语句支持多值、范围、正则等丰富匹配模式：

```bash
match x {
    1, 2, 3      => "small"          # 多值匹配
    4..10        => "medium"         # 范围匹配
    r'^\d+$'     => "numeric str"   # 正则匹配
    "none", none => "empty"          # 字符串 + none
    _            => "other"          # 兜底
}
```

---

### 流程控制作为表达式

Bash 中流程控制是语句，无法作为表达式赋值。

**Lume 的轻松：**

流程控制可作为表达式使用：

```bash
# 语句上下文：无返回值
for i in 1..5 { print i }

# 赋值上下文：返回 List
let squares = for i in 1..5 { i * i }   # → [1, 4, 9, 16, 25]

# 管道上下文：返回 List 并继续流动
for i in 1..10 { i * 2 } | list.filter(x -> x > 10)

# if 表达式
let result = if x > 0 { "positive" } else { "non-positive" }
```

---

## 五、函数系统

### 参数传递与返回值

Bash 函数没有形参，只有位置参数，且返回值只能是 0–255 的退出码：

```bash
foo() {
    echo "$1"   # 无参数名、无类型、无默认值
}

foo() {
    return 100   # 只能返回整数
}

# 返回字符串只能通过全局变量或命令替换
foo() { echo "hello"; }
res=$(foo)
```

此外，函数名与外部命令同名时会覆盖命令：

```bash
time() { echo "my time"; }
time           # 调用函数
command time   # 强制调用外部命令
```

**Lume 的轻松：**

函数支持具名参数、默认值、可变参数，调用使用括号，不与命令冲突：

```bash
fn greet(name, greeting="Hello") {
    println greeting + ", " + name + "!"
}
greet("Alice")        # Hello, Alice!
greet("Bob", "Hi")    # Hi, Bob!

fn sum(*nums) {
    nums | list.foldl((acc, x) -> acc + x, 0)
}
sum(1, 2, 3, 4, 5)   # 15

time()   # 调用函数，不影响外部命令 time
```

---

### Lambda、闭包与柯里化

Bash 不支持 Lambda、闭包和柯里化。

**Lume 的轻松：**

```bash
# Lambda
let double = x -> x * 2
let add = (x, y) -> x + y

# 闭包：自动捕获自由变量
let base = 10
let adder = x -> x + base

fn make_adder(base) {
    x -> x + base    # 返回记住 base 的 Lambda
}
let add5 = make_adder(5)
add5(3)    # 8
add5(10)   # 15

# 柯里化（部分应用）
let multiply = (x, y) -> x * y
let double = multiply(2)   # 返回新 Lambda，等待第二个参数
double(7)    # 14
```

---

### 装饰器

Bash 不支持装饰器。

**Lume 的轻松：**

```bash
@logger("debug")
@timer
fn my_function(x) {
    x * 2
}
```

装饰器返回 `[before_fn, after_fn]` 列表，执行顺序为：`logger.before → timer.before → 函数体 → timer.after → logger.after`。装饰器环境中可访问 `NAME`、`ARGS`、`RESULT` 变量。

---

## 六、作用域与变量

### 变量默认全局

Bash 函数内的变量默认是全局的，局部需要显式声明 `local`：

```bash
func() {
    a=1
}
func
echo $a   # 1（全局污染）
```

**Lume 的轻松：**

变量归属于声明时的作用域，函数默认隔离。修改父级作用域需显式使用 `set`：

```bash
let a = 5
fn add() { a = 1 }
add()
print a   # 5（函数内的 a 是局部变量）
```

---

### 未定义变量

Bash 中未定义变量默认展开为空字符串，极易引发灾难：

```bash
rm -rf /$undefined_dir   # 若 undefined_dir 为空，等同于 rm -rf /
```

`set -u` 能救你一命。

**Lume 的轻松：**

未定义变量默认值为 `none`，而非空字符串。路径中的 `$var` 不会自动展开，需显式使用模板字符串：

```bash
rm -rf /$undefined_dir    # 识别为字面量：rm -rf '/$undefined_dir'
rm -rf `/$undefined_dir`  # 报错：undeclared variable `undefined_dir`
```

---

## 七、进程与管道

### 管道子 Shell 陷阱

Bash 管道右侧运行在子 Shell 中，变量修改不会传回父进程：

```bash
count=0
seq 10 | while read i; do
    ((count++))
done
echo "$count"   # 0（变量修改丢失）
```

✅ 替代方案：

```bash
while read x; do a=1; done <<< "123"
```

---

### 命令替换与 `()` 的歧义

命令替换 `$()` 同样运行在子 Shell 中：

```bash
a=1
b=$(a=2; echo $a)
echo "$a"   # 1（子 Shell 改不了父 Shell 变量）
```

Bash 中 `()` 是子 Shell，`{}` 是当前 Shell（但前后需要空格，末尾需要分号）：

```bash
(a=1)
echo "$a"   # 空（子 Shell 中的修改丢失）
```

✅ 子 Shell 改不了父 Shell 变量，这是 Bash 的铁律。

**Lume 的轻松：**

管道与命令捕获均不启动子进程，数据不会丢失：

```bash
[1,2,3,4,5] | .filter(x -> x > 2) | .map(x -> x * x)

(a=1)
print $a    # 1
```

---

### Lume 的管道系统

Bash 管道只能传递文本流，无法传递结构化数据，且管道右侧只能通过 stdin 接收数据。

**Lume 的轻松：**

四种管道类型，支持结构化数据：

```bash
data | process              # 标准管道：支持结构化数据（List/Map 直接传递）
data | positional a _ c     # 位置管道：_ 为占位符，数据注入指定位置
data |> transform           # 分发管道：对集合每个元素分别应用右侧函数
data |^ interactive         # PTY 管道：用于 vi/ssh/htop 等交互式程序
```

管道不启动子进程，结构化数据直接流动：

```bash
fs.ls -lh | where(size > 5K)
[1,2,3,4,5] | .filter(x -> x > 2) | .map(x -> x * x)
ls -1 |> cp -r _ /tmp/     # 对每个文件执行 cp
```

链式调用：

```bash
"hello world".split(' ').join(',')    # → "hello,world"
[3,1,2].sort().rev()
data | .filter(x -> x > 0)
```

---

## 八、IO 与字符串处理

### 输出命令

`echo` 无法安全打印所有字符串：

```bash
echo "-n"   # 被当作参数处理
```

✅ 更安全：

```bash
printf "%s\n" "$var"
```

**Lume 的轻松：**

`print` 语句比第三方 `echo` 更快、更安全：

```bash
print "-n"
```

---

### 重定向

`>` 会静默覆盖文件：

```bash
cmd > out.txt
```

✅ 防止误操作：

```bash
set -o noclobber
```

**Lume 的轻松：**

使用 `>!` 替代 `>`，追加操作 `>>` 不变：

```bash
cmd _ >! out.txt
```

---

## 九、通配符与文件操作

### 未匹配通配符

Bash 中通配符未匹配时原样保留，可能引发灾难：

```bash
rm *.log   # 若无 .log 文件，尝试删除名为 '*.log' 的文件
```

✅ 防御：

```bash
shopt -s nullglob
```

**Lume 的轻松：**

通配符未匹配时直接报错，处理异常后方可继续：

```bash
rm *.log        # 报错：wildcard not matched: `*.log`
rm *.log ?.     # 忽略错误，继续执行
```

---

### for 循环与文件名

Bash 中 `for f in *` 遇到含空格的文件名需要引号保护：

```bash
for f in *; do
    echo "$f"   # 变量永远需要引号
done
```

**Lume 的轻松：**

变量无需额外引号：

```bash
for f in ./* {
    print $f
}
```

---

## 十、模块导入

### Bash 的折磨
```bash
# utils.sh
MY_CONSTANT="hello"
my_func() { echo "util function"; }

# main.sh
source utils.sh
# 现在，MY_CONSTANT 和 my_func 都在全局命名空间里裸奔。
# 如果两个库都定义了同名函数，后者会无情地覆盖前者，且没有任何警告。
# 没有模块系统，没有命名空间。
# 大型项目的 bash 脚本，最终都会演变成一个巨大的全局命名空间垃圾场，充满了命名冲突的定时炸弹。
```

### Lume 的轻松
```bash
# 使用模块，拥有清晰的命名空间，干净利落
use myutils as utils
utils::my_function()
utils::MY_CONSTANT

# 17个内置模块，按需加载，绝不污染全局环境
list.map(...)       # 列表操作
string.split(...)   # 字符串操作
fs.read(...)        # 文件操作
time.now()          # 时间操作
math.sqrt(16)       # 数学函数
regex.find(r'\d+', text)  # 正则操作
ui.pick("选择一个:", options)  # 交互式选择
```

习惯了简单并入？lume也满足你：`include`


## 十一、错误处理与调试

### 默认不报错、不停止

Bash 默认不报错、不停止、不提示，强烈建议脚本开头加：

```bash
set -euo pipefail
```

- `-e`：命令失败即退出
- `-u`：未定义变量报错
- `-o pipefail`：管道中任意命令失败即失败

**Lume 的轻松：**

编译器级别的错误提示，开箱即用：

- 错误前后各 3 行上下文
- 精确的行号和列号
- 红色高亮错误位置，`^~~~` 指示箭头
- 具体的错误描述与修复建议

遇到错误自动终止，除非异常已被处理。

---

### 调试工具

Bash 调试手段原始：

```bash
echo "DEBUG: x=$x"
echo $?
set -x    # 噪音极大，如同海啸
bash: syntax error near unexpected token '('   # 不知道是哪一行
```

**Lume 的轻松：**

调试专用语句：`debug`、`ddebug`、`typeof`、`assert`、`condition`；日志模块：`log`。

`tap` 是管道调试利器，打印中间结果但不打断数据流：

```bash
[1, 2, 3] | list.map(x -> x * 2) | tap | list.filter(x -> x > 3)
#                                   ↑ 打印中间结果，数据继续无损流动
```

---

### 错误捕获机制

Bash 依赖退出码判断成败，错误处理粗糙。

**Lume 的轻松：**

7 种后缀错误捕获操作符：

```bash
cmd ?.          # 忽略错误，返回 none
cmd ?: handler  # 将错误信息（Map）传给 handler 函数
cmd ?+          # 打印错误到 stdout，返回 none
cmd ??          # 打印错误到 stderr（红色），返回 none
cmd ?>          # 合并错误到 stdout
cmd ?!          # 遇错终止管道
cmd ?~          # 成功→true，失败→false（用于条件判断）
```

实用模式：

```bash
# 类 bash 的 && ||
validate() ?~ ? process() : cleanup()

# 捕获错误信息，编程式处理
risky_command ?: (e) -> {
    println "操作失败"
    println "错误码：" e.code
    println "错误信息：" e.msg
    println "出错位置：" e.expr
    default_value    # 返回默认值，优雅降级
}

# 读取配置文件，失败时回退到默认值
let config = fs.read "config.json" ?: "{}"

# 主动抛出错误
fn divide(a, b) {
    if b < 0 { throw "除数不能为负" }
    a / b
}
divide(10, -1) ?: (e) -> { println e.msg }
```

---

## 十二、后台任务

Bash 中后台任务不会随主进程退出而退出：

```bash
sleep 1000 &
exit   # sleep 仍在运行
```

✅ 正确姿势：

```bash
trap 'kill $(jobs -p)' EXIT
```

**Lume 的轻松：**

主进程结束时所有后台任务自动退出：

```bash
sleep 1000 &
exit   # sleep 随之退出
```

---

## 十三、交互与 UI

### 颜色与显示

Bash 颜色显示需要手写 ANSI 转义码，交互依赖文本问答。

**Lume 的轻松：**

内置颜色函数与 COLOR 常量，集成交互式 UI：

```bash
'hi lume'.green()
COLOR.red + 'hello'

fs.ls -lh | ui.pick 'select a file'
```

---

### 现代交互能力

Bash 完全不具备以下现代交互能力：

**缩写展开（Abbreviations）**

```bash
set LUME_ABBREVIATIONS = {
    xi: 'doas pacman -S',
}
# 输入 "xi " 自动展开为 "doas pacman -S "
```

**可编程热键**

```bash
set LUME_HOT_BINDINGS = {
    CTRL_q: 'exit',
    ALT_m: save_cmdmark,
    'CTRL_/': menu,
}
```

**可编程斜杠命令**

```bash
set LUME_SLASH_BINDINGS = {
    sm: save_cmdmark,
    m: select_cmdmark,
    cm: git_commit,
}
```

**可编程提示符**

```bash
set LUME_PROMPT_TEMPLATE = (dir, ctx) -> {
    string.blue($dir) + ' |'.green().bold()
    + ($ctx.cfm ? 'CFM'.green() + '|' : '')
    + (if (fs.exists '.git') {git branch --show-current | .cyan()} else '')
    + '> '.green().bold()
}
```

**语法高亮主题**

```bash
LUME_THEME = 'ayu_dark'
LUME_THEME_CONFIG = { keyword: COLOR.GREEN }
```

**自动补全**：命令、参数、路径、历史、内置函数均可自动补全。

**AI 补全**：通过 `LUME_AI_CONFIG` 配置 AI 后端，`Alt+i` 提示，`Alt+o` 或 `Alt+Enter` 生成。

---

## 十四、性能

Bash 循环性能低下：

```bash
# 循环求和 100 万次：约 2200 毫秒
start_time=$(($(date +%s%N)/1000000))
sum=0
for ((i=1; i<1000000; i++)); do
    sum=$((sum + i))
done
end_time=$(($(date +%s%N)/1000000))
echo "所需时间: $((end_time - start_time)) 毫秒"
# 所需时间: 2224 毫秒
```

**Lume 的轻松：**

```bash
# 循环求和 100 万次：约 200 毫秒（快 10 倍以上）
let start = time.stamp_ms()
let sum = 0
for i in 0..1000000 { sum += i }
let end = time.stamp_ms()
print "所需时间: " end - start "毫秒"
# 所需时间: 199 毫秒
```

---

## 小结

Bash 的种种陷阱并非偶然，而是其设计哲学的必然结果：**一切皆文本，一切皆命令**。这一哲学在 Unix 诞生之初极具革命性，但在现代脚本编程的需求面前，代价日益显现:

- **类型缺失**：字符串、整数、数组、布尔值在底层没有区别，导致运算需要特殊语法，比较需要不同操作符，稍有不慎便语义混乱。
- **隐式行为**：变量展开、单词分割、glob 展开默认开启，省略引号就是埋雷。
- **子 Shell 隔离**：管道、命令替换、`()` 均创建子进程，变量修改无法传回，数据流动受阻。
- **防御性编程**：`set -euo pipefail`、`shopt -s nullglob`、引号、`[[ ]]`——每一条都是用血泪换来的经验。

Lume 的设计从另一个方向出发：**安全默认，显式优于隐式**。

- **类型系统**：整数、浮点、数组、映射、范围、正则、时间字面量各有其类型，运算直接书写，无需括号魔法。
- **安全默认**：未定义变量报错，通配符未匹配报错，命令失败自动终止——不需要手动开启防御模式。
- **一致性**：空格可选，`==` 统一比较，`~:` 统一匹配，无需记忆 `-eq` / `=` / `==` 的区别。
- **现代语言特性**：Lambda、闭包、柯里化、装饰器、模式匹配、流程控制表达式——复杂逻辑不再需要"换语言"。
- **结构化管道**：管道传递结构化数据，不启动子进程，数据不丢失。

| 维度 | Bash | Lume |
|------|------|------|
| 类型系统 | 一切皆字符串 | 完整类型系统 |
| 默认行为 | 宽松，需手动防御 | 严格，安全优先 |
| 算术运算 | `$(( ))` / `bc` | 直接书写 |
| 条件判断 | `[ ]` / `[[ ]]` / `(( ))` | 直接书写 |
| 管道 | 文本流，子进程 | 结构化数据，无子进程 |
| 错误处理 | 退出码，需 `set -e` | 自动终止，7 种捕获操作符 |
| 函数 | 位置参数，退出码返回 | 具名参数，任意类型返回 |
| 字符串插值 | 引号地狱 | 反引号模板，无嵌套转义 |

Bash 的生存法则是"凡是允许省略的，最终都会炸"；Lume 的设计目标是让正确的写法也是最自然的写法。
