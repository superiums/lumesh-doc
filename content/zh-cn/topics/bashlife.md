---
title: Bash泥泞人生路
date : '2026-08-15T14:45:13+08:00'
weight: 1
highlight: true
layout: slides
fullWidth: true
showTableOfContents: false
---

{{< slide type="hero" tag="深入对比 · 2026" sub="让脚本回归自然和简洁" >}}
系统梳理 Bash 的常见陷阱与反直觉设计

并对比 Lume 的解决思路
{{< /slide >}}

{{< slide type="compare" title="一、语法基础 · 空格敏感性" >}}
{{< code side="bash" >}}
```bash
# ❌ 错误
if[$a==$b]; then ...

# ✅ 正确
if [ "$a" == "$b" ]; then ... fi
# [ 是命令，不是语法符号
# = 两侧必须有空格
```
{{< /code >}}
{{< code side="lume" >}}
```bash
a == b
if $a == $b { ... }
# 空格完全可选，表达式直接书写
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="语法基础 · 赋值语法" >}}
{{< code side="bash" >}}
```bash
# 错误
a = 1

# 正确
a=1
# 赋值不允许有空格，否则 a 会被当作命令执行
```
{{< /code >}}
{{< code side="lume" >}}
```bash
a=1
a = 1    # 等价，空格可选
# CFM 模式下需要空格
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="字符串与引号 · 变量隐式分割" caption="不加引号 = 隐式 split + glob" >}}
{{< code side="bash" >}}
```bash
file="a b"

rm $file
# 被解析为 rm a b，删除两个文件

rm "$file"
# 必须加引号护身符✅
```
{{< /code >}}
{{< code side="lume" >}}
```bash
rm $file   # 解析为 rm 'a b'，符合直觉
# 除非修改 IFS 和 LUME_IFS_MODE
# 否则不对参数中的空格分割
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="字符串与引号 · IFS 精细控制" >}}
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
{{< /slide >}}

{{< slide type="compare" title="字符串与引号 · 变量展开暗藏杀机" >}}
{{< code side="bash" >}}
```bash
input='$(rm -rf /)'
eval "echo $input"   # 炸
```
{{< /code >}}
{{< code side="lume" >}}
```bash
let input = '(rm -rf /)'
let safe = into.safe $input
eval_str `echo $safe`   # 打印出安全字符串 s'(rm -rf /)'
# StringSafe 类型，保证 eval 时不会意外炸雷
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="字符串与引号 · 字符串类型" >}}
{{< code side="bash" >}}
```bash
str='hello world'
# 单引号：原始字符串
"hello\nworld"
"hello $name"
# 双引号，兼具转义与插值



# 引号内的引号 -> 嵌套地狱
```
{{< /code >}}
{{< code side="lume" >}}
```bash
str='hello world'
# 单引号：原始字符串，仅转义 \'
"hello\nworld"
# 双引号：只负责 转义序列
`output: $var {var * 2}`
# 反引号：模板字符串，转义与变量插值

r#'...'#  r#"..."#
# 内部的引号无需转义
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="字符串与引号 · 引号地狱" >}}
{{< code side="bash" >}}
```bash
echo "Today is $(date "+%Y-%m-%d")"
sed "s/foo/bar \"$var\"/g" file.txt

curl -X POST -d \
"{\"name\":\"$name\", \
\"msg\":\"Hello '$msg'\"}" \
http://example.com
```
{{< /code >}}
{{< code side="lume" >}}
```bash
print `Today is {date '+%Y-%m-%d'}`
sed `s/foo/bar "$var"/g` file.txt

curl -X POST -d \
r#`{"name":"{name}", \
"msg": "Hello '$msg'"}`# \
http://example.com
# 反引号插值 + hashed string，内部引号直接书写

let data = {name, msg: `Hello '$msg'`}
curl -X POST -d into.json($data) \
http://example.com
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="字符串与引号 · 多行文本" >}}
{{< code side="bash" >}}
```bash
cat <<EOF
line1
line2
EOF
```
{{< /code >}}
{{< code side="lume" >}}
```bash
let a = 'line1
line2'
# 三种字符串引号均直接支持多行文本
# 无需here doc补丁
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="字符串与引号 · 大小写操作" >}}
{{< code side="bash" >}}
```bash
[[ "${a,,}" == "${b,,}" ]]
```
{{< /code >}}
{{< code side="lume" >}}
```bash
a.lower() == b.lower()
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="二、类型系统 · 默认字符串类型" >}}
{{< code side="bash" >}}
```bash
a=1
b=2
c=$a+$b
echo $c   # 1+2（字符串拼接，非加法）

# ✅ 必须
((c=a+b))
c=$((a+b))
```
{{< /code >}}
{{< code side="lume" >}}
```bash
# 变量有明确类型，运算直接书写，无需特殊语法
a + b
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="类型系统 · 数字类型" caption="整数、浮点全支持，溢出时明确报错" >}}
{{< code side="bash" >}}
```bash
echo $((1/2))   # 0（整除）
echo "scale=2; 1/2" | bc   # 0.50

echo $((99999999999999999999999))
# 莫名其妙的数（整数溢出静默发生）
```
{{< /code >}}
{{< code side="lume" >}}
```bash
1 / 2         # 0   整除
1 / 2.0       # 0.5 浮点除法
a - b / c * d ^ 2    # 自然书写，无需外部工具
math.sin(x)   # 高级运算，使用math库

echo 99999999999999999999999     # 报错
# syntax error: expect Integer,
# found error: number too large
# to fit in target type
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="类型系统 · 数组语法" >}}
{{< code side="bash" >}}
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
{{< /code >}}
{{< code side="lume" >}}
```bash
arr = [a, b, c]
print arr
print arr[0]
print arr.len()
print arr[1..-1]    # 切片
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="类型系统 · 下标重排" >}}
{{< code side="bash" >}}
```bash
arr=(a b c)
unset arr[1]
echo "${arr[@]}"   # a c（下标仍为 0 2，非 0 1）
```
{{< /code >}}
{{< code side="lume" >}}
```bash
arr = [a, b, c]
arr.remove_at(1)
# → [a, c]，下标为 0、1，自动重排
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="类型系统 · 关联数组必须提前声明" >}}
{{< code side="bash" >}}
```bash
map["a"]=1       # ❌ 普通数组
declare -A map
map["a"]=1       # ✅ 关联数组
```
{{< /code >}}
{{< code side="lume" >}}
```bash
a = [1, 2]          # 数组
m = {a: 1, b: 2}    # 映射
# 数组与映射类型独立，语法直观
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="类型系统 · 嵌套访问">}}
Bash 不支持嵌套数据结构的访问。Lume 支持 SQL 风格的 `select` 与点路径 `get`：

```bash
# select：从 List[Map] 中选取列（类似 SQL SELECT）
fs.ls -l | select name size modified

# get：点路径访问嵌套结构
let config = {db: {host: "localhost", port: 5432}}
config.db.host            # 直接访问属性 → "localhost"
config | get "db.host"    # 管道访问
get config "db.port"      # 函数访问

# 字面量随意嵌套
[1,24,5,[5,6,8]][3][1]     # 显示6
```
{{< /slide >}}

{{< slide type="text" title="类型系统 · 特殊类型">}}
Bash 中范围、正则、时间均以字符串表示，无专用类型；Lume 中所有类型均可参加运算。

```bash
# 范围类型
1..10       # 半开区间 [1, 10)，惰性 Range 对象
1..=10      # 闭区间 [1, 10]
1..10:2     # 步长为 2：1, 3, 5, 7, 9
_..5        # 从 Int::MIN 到 5
1..10 ~: 3  # true

# 文件大小与百分比字面量
50%         # → 0.5（Float）
3M          # → FileSize(3MB)
1.5G        # → FileSize(1.5GB)
3M > 1G         # → false
filesize.b(1K)  # → 1024

# 正则与时间字面量
g'\d+'
t'2026-7-23'
t'08:10' - t'08:09'  # 时间差(ms)：60000
```
{{< /slide >}}

{{< slide type="compare" title="三、运算与条件判断 · [ ] vs [[ ]] vs (( ))" caption="忘记繁琐的括号吧">}}
{{< code side="bash" >}}
```bash
[[ "$a" =~ ^[0-9]+$ ]]
[[ "$a" > 0 && "$b" < 0 ]]
# 能用 [[ ]] 就不用 [ ]

[ "$a" -eq "123" ]   # 整数比较
[ "$a" = 123 ]       # 字符串比较
(( a == 123 ))       # 更清晰
```
{{< /code >}}
{{< code side="lume" >}}
```bash
a ~: g'\d+'     # 正则匹配
a > 0 && b < 0  # 逻辑运算
# 无中括号，无双括号语法
 

a == b          # 统一比较所有类型，无需任何括号
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="运算与条件判断 · 正则与通配符" >}}
{{< code side="bash" >}}
```bash
[[ "$a" == *.log ]]     # 通配符
[[ "$a" =~ \.log$ ]]    # 正则
```
{{< /code >}}
{{< code side="lume" >}}
```bash
ls *.log
for f in *.log { ... }

$a ~: '.log'        # 字符串包含
$a ~: r'.log$'      # 正则匹配
# ~: 还可检测集合/列表/范围/字典 key 是否包含右侧表达式
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="运算与条件判断 · 返回值语义" caption="反直觉">}}
{{< code side="bash" >}}
```bash
grep "abc" file.txt
echo $?   # 0 表示找到

if grep "abc" file.txt; then
    echo "found"
fi
# true = 0，false ≠ 0
```
{{< /code >}}
{{< code side="lume" >}}
```bash
let found = fs.read file.txt | .grep('abc')
if found {     # 等价于 if !found.is_empty()
    echo 'found'
}
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="四、流程控制 · 模式匹配" >}}
{{< code side="bash" >}}
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
{{< /code >}}
{{< code side="lume" >}}
```bash
match x {
    1, 2, 3      => "small"          # 多值匹配
    4..10        => "medium"         # 范围匹配
    r'^\d+$'     => "numeric str"    # 正则匹配
    "none", none => "empty"          # 字符串 + none
    _            => "other"          # 兜底
}
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="流程控制 · 作为表达式">}}
Bash 中流程控制是语句，无法作为表达式赋值。Lume 中流程控制可作为表达式使用：

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
{{< /slide >}}

{{< slide type="compare" title="五、函数系统 · 参数传递与返回值" >}}
{{< code side="bash" >}}
```bash
foo() {
    echo "$1"   # 无参数名、无类型、无默认值
}

foo() {
    return 100  # 只能返回 0-255 的退出码
}

foo() { echo "hello"; }
res=$(foo)
# 返回字符串只能通过全局变量或命令替换

time() { echo "my time"; }
time          # 调用函数，覆盖了同名外部命令
command time  # 强制调用外部命令
```
{{< /code >}}
{{< code side="lume" >}}
```bash
fn greet(name, greeting="Hello") {
    println greeting ", "  name "!"
}
greet("Alice")        # Hello, Alice!
greet("Bob", "Hi")    # Hi, Bob!

fn sum(*nums) {
    nums | list.fold((acc, x) -> acc + x, 0)
}
sum(1, 2, 3, 4, 5)   # 15

time()   # 调用函数，不影响外部命令 time
time _   # 调用外部命令
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="函数系统 · Lambda、闭包与柯里化" >}}
Bash 不支持 Lambda
{{< code side="lume" >}}
```bash
# Lambda
let double = x -> x * 2
let add = (x, y) -> x + y

let base = 10
let adder = x -> x + base  # 闭包：自动捕获自由变量

fn make_adder(base) {
    x -> x + base          # 返回记住 base 的 Lambda
}
let add5 = make_adder(5)
add5(3)    # 8
add5(10)   # 15

let multiply = (x, y) -> x * y
let double = multiply(2)   # 柯里化（部分应用）
# 返回新 Lambda，等待第二个参数
double(7)    # 14
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="函数系统 · 装饰器" caption="执行顺序：logger.before → timer.before → 函数体 → timer.after → logger.after">}}
{{< code side="bash" >}}
```bash
# Bash 不支持装饰器
```
{{< /code >}}
{{< code side="lume" >}}
```bash
@logger("debug")
@timer
fn my_function(x) {
    x * 2
}
# 装饰器返回 [before_fn, after_fn] 列表
# 装饰器环境中可访问 NAME、ARGS、RESULT 变量
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="六、作用域与变量 · 变量默认全局" >}}
{{< code side="bash" >}}
```bash
func() {
    a=1
}
func
echo $a   # 1（全局污染）
```
{{< /code >}}
{{< code side="lume" >}}
```bash
let a = 5
fn add() { a = 1 }
add()
print a   # 5（函数内的 a 是局部变量，修改父级需显式 set）
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="作用域与变量 · 未定义变量" >}}
{{< code side="bash" >}}
```bash
rm -rf /$undefined_dir
# 若 undefined_dir 为空，等同于 rm -rf /
# set -u 能救你一命
```
{{< /code >}}
{{< code side="lume" >}}
```bash
rm -rf /$undefined_dir
# 识别为字面量：rm -rf '/$undefined_dir'
rm -rf `/$undefined_dir`
# 报错：undeclared variable `undefined_dir`
# 未定义变量默认值为 none，而非空字符串
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="七、进程与管道 · 子 Shell 陷阱">}}
Bash 管道右侧、命令替换 `$()`、以及 `()` 都运行在子 Shell 中，变量修改不会传回父进程：
{{< code side="bash" >}}
```bash
count=0
seq 10 | while read i; do
    ((count++))
done
echo "$count"   # 0（变量修改丢失）

# ✅ 替代方案
while read x; do a=1; done <<< "123"
```

```bash
a=1
b=$(a=2; echo $a)
echo "$a"   # 1（子 Shell 改不了父 Shell 变量）

(a=1)
echo "$a"   # 空（子 Shell 中的修改丢失）
```
{{< /code >}}

子 Shell 改不了父 Shell 变量，这是 Bash 的铁律。此外，Bash 管道只能从 `stdout`/`stdin` 传递文本字节流，必须 `echo` 才能传递出去：

```bash
echo "hello" | wc
```
{{< /slide >}}

{{< slide type="text" title="进程与管道 · Lume 的管道系统">}}
四种管道类型，支持结构化数据，无需 `echo`，也不启动子进程：

{{< code side="lume" >}}
```bash
data | process              # 标准管道：支持结构化数据（List/Map 直接传递）
data | positional a _ c     # 位置管道：_ 为占位符，数据注入指定位置
data |> transform           # 分发管道：对集合每个元素分别应用右侧函数
data |^ interactive         # PTY 管道：用于 vi/ssh/htop 等交互式程序

"hello" | wc

# 结构化数据
fs.ls -lh | where(size > 5K)
[1,2,3,4,5] | .filter(x -> x > 2) | .map(x -> x * x)

# 数据不丢失
(a=1)
print $a    # 1

# 循环派发
ls -1 |> cp -r _ /tmp/     # 对每个文件执行 cp
```

链式调用（比管道更方便的数据流动）：

```bash
"hello world".split(' ').join(',')    # → "hello,world"
[3,1,2].sort().rev()
data | .filter(x -> x > 0)
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="八、IO 与字符串处理 · 输出命令" >}}
{{< code side="bash" >}}
```bash
echo "-n"   # 被当作参数处理

# ✅ 更安全
printf "%s\n" "$var"
```
{{< /code >}}
{{< code side="lume" >}}
```bash
print "-n"
# print 语句比第三方 echo 更快、更安全
```
{{< /code >}}
{{< /slide >}}


{{< slide type="compare" title="IO 与字符串处理 · 重定向" >}}
{{< code side="bash" >}}
```bash
cmd > out.txt   # 静默覆盖文件
# ✅ 防止误操作
set -o noclobber

command > all.log 2>&1
command > /dev/null 2>&1
# 符号密集，语义反直觉
ommand 2>&1 > out.log   # 错误
```
{{< /code >}}
{{< code side="lume" >}}
```bash
cmd _ >! out.txt
# 使用 >! 替代 >，更醒目
# 追加操作 >> 不变

command &+ > all.log      # 合并
command &.                # 忽略
# 错误重定向，简单直接
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="九、通配符与文件操作 · 未匹配通配符" >}}
{{< code side="bash" >}}
```bash
# 若无 .log 文件

rm *.log
# 尝试删除名为 '*.log' 的文件

# 解决方案：防御
shopt -s nullglob
```
{{< /code >}}
{{< code side="lume" >}}
```bash
# 若无 .log 文件

rm *.log
# 报错：wildcard not matched: `*.log`

# 解决方案：忽略或捕获
rm *.log ?.             # 忽略错误，继续执行
rm *.log ?: do_handler  # 处理异常后方可继续
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="通配符与文件操作 · for 循环与文件名" >}}
{{< code side="bash" >}}
```bash
for f in *; do
    echo "$f"   # 变量永远需要引号
done
```
{{< /code >}}
{{< code side="lume" >}}
```bash
for f in ./* {
    print $f
}
# 变量无需额外引号
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="十、模块导入" >}}
{{< code side="bash" >}}
```bash
# utils.sh
MY_CONSTANT="hello"
my_func() { echo "util function"; }

# main.sh
source utils.sh
# 现在，MY_CONSTANT 和 my_func
# 都在全局命名空间里裸奔。
# 如果两个库都定义了同名函数，
# 后者会无情地覆盖前者，且没有任何警告。
# 没有模块系统，没有命名空间。
# 大型项目的 bash 脚本，最终都会演变成
# 一个巨大的全局命名空间垃圾场。
```
{{< /code >}}
{{< code side="lume" >}}
```bash
# 使用模块，拥有清晰的命名空间，干净利落
use myutils as utils
utils::my_function()

# 17个内置模块，按需加载，绝不污染全局环境
list.map(...)                 # 列表操作
string.split(...)             # 字符串操作
fs.read(...)                  # 文件操作
time.now()                    # 时间操作
math.sqrt(16)                 # 数学函数
regex.find(g'\d+', text)      # 正则操作
ui.pick("选择一个:", options)  # 交互式选择
# 习惯了简单并入？lume也满足你：include
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="十一、错误处理与调试 · 默认不报错、不停止" >}}
{{< code side="bash" >}}
```bash
# Bash 默认不报错、不停止、不提示
# 强烈建议脚本开头加：
set -euo pipefail
# -e：命令失败即退出
# -u：未定义变量报错
# -o pipefail：管道中任意命令失败即失败
```
{{< /code >}}
{{< code side="lume" >}}
```bash
# 编译器级别的错误提示，开箱即用：
# 错误前后各 3 行上下文
# 精确的行号和列号
# 红色高亮错误位置，^~~~ 指示箭头
# 具体的错误描述与修复建议
# 遇到错误自动终止，除非异常已被处理
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="错误处理与调试 · 调试工具" caption="tap 是管道调试利器，打印中间结果但不打断数据流">}}
{{< code side="bash" >}}
```bash
echo "DEBUG: x=$x"
echo $?
set -x    # 噪音极大，如同海啸
# bash: syntax error near unexpected token '('
# 你知道是哪一行吗？
```
{{< /code >}}
{{< code side="lume" >}}
```bash
# 调试专用语句：
# debug、ddebug、typeof、assert、condition
# 日志模块：log

[1, 2, 3] | list.map(x -> x * 2) \
| tap | list.filter(x -> x > 3)
#  ↑ 打印中间结果，数据继续无损流动
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="错误处理与调试 · 错误捕获机制">}}
Bash 依赖退出码判断成败，错误处理粗糙。Lume 提供 7 种后缀错误捕获操作符：
{{< code side="lume" >}}

```bash
# 成败钩子（成功/失败时）
risky_call() &: next       # 成功时执行函数
risky_call() ?: handler    # 出错时执行函数或返回默认值（惰性求值）

# 流程控制（忽略/终止）
risky_call() ?.        # 忽略错误
risky_call() ?!        # 出错终止（管道中才需要）

# 输出转换
risky_call() ?~        # 执行成败转布尔
risky_call() ?>        # 以错误信息替代返回值

# 打印辅助
risky_call() ?+        # 出错打印到标准输出
risky_call() ??        # 出错打印到标准错误

# 空值钩子
risky_call() _: handler   # 遇空值时执行
risky_call() _! | next    # 遇空值时终止（管道中才需要）
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="错误处理与调试 · 错误捕获机制">}}
实用模式：
{{< code side="lume" >}}

```bash
# 类 bash 的 && ||
validate() &: process() ?: cleanup()
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
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="十二、后台任务" >}}
{{< code side="bash" >}}
```bash
sleep 1000 &
exit   # sleep 仍在运行

# ✅ 正确姿势
trap 'kill $(jobs -p)' EXIT
```
{{< /code >}}
{{< code side="lume" >}}
```bash
sleep 1000 &
exit        # sleep 随之退出

jobs        # 查看后台任务
jobs -k id  # 终止后台任务
# 主进程结束时所有后台任务自动退出
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="十三、交互与 UI · 颜色与显示" >}}
{{< code side="bash" >}}
```bash
"\033[31;1merror\033[m"
# 颜色显示需要手写 ANSI 转义码


read -p "your choose:"
# 交互依赖文本问答
```
{{< /code >}}
{{< code side="lume" >}}
```bash
'hi lume'.green().bold()
COLOR.red + 'hello'
STYLE.BOLD + 'lume'

fs.ls -lh | ui.pick 'select a file'
# 内置颜色函数与 COLOR 常量，集成交互式 UI
```
{{< /code >}}
{{< /slide >}}

{{< slide type="text" title="交互与 UI · 现代交互能力" caption="Bash 完全不具备以下现代交互能力">}}

{{< code side="lume" >}}

**缩写展开**         空格即展开

**可编程热键**        热键可修改当前行输入，但不修改 `env`

**可编程斜杠命令**     可修改 `env`，但不可修改输入行

**可编程提示符**       支持自定义函数，支持 `starship`

**语法高亮主题**       可更换主题或单个定制

**自动补全**：        命令、参数、路径、历史、内置函数均可自动补全。

**AI 补全**          支持 openai 兼容的 api
{{< /code >}}

{{< /slide >}}

{{< slide type="perf" title="十四、性能：循环求和 100 万次" bashMs="2224" lumeMs="199" speedup="11.2" note="100万次循环求和" >}}
{{< /slide >}}

{{< slide type="text" title="小结">}}
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

{{< /slide >}}

{{< slide type="text" title="小结">}}

| 维度 | Bash | Lume |
|------|------|------|
| 类型系统 | 一切皆字符串 | 完整类型系统 |
| 默认行为 | 宽松，需手动防御 | 严格，安全优先 |
| 算术运算 | `$(( ))` / `bc` | 直接书写 |
| 条件判断 | `[ ]` / `[[ ]]` / `(( ))` | 直接书写 |
| 管道 | 文本流，子进程 | 结构化数据，无子进程 |
| 错误处理 | 退出码，需 `set -e` | 自动终止，10 种捕获操作符 |
| 函数 | 位置参数，退出码返回 | 具名参数，任意类型返回 |
| 字符串插值 | 引号地狱 | 反引号模板，无嵌套转义 |

Bash 的生存法则是"凡是允许省略的，最终都会炸"；Lume 的设计目标是让最自然的写法成为正确的写法。
{{< /slide >}}
