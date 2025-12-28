# Lume Vs Bash

---

| 对比项目|    lume       |     bash      |     dash      |     fish      |
|---------|---------------|---------------|---------------|---------------|
| 速度(百万循环)    |     *****     |     ***       |     ****      |    *          |
| 交互    |     ****      |     **        |     *         |    *****      |
| 语法    |     *****     |     **        |     *         |    ****       |
| 体积    |     ****      |     ***       |     *****     |    **         |
| 错误提示|     *****     |     *         |     *         |    ***        |
| 错误处理|     *****     |     *         |     *         |    *        |

---

| 对比项目|    lume       |     bash      |     dash      |     fish      |
|---------|---------------|---------------|---------------|---------------|
| 内置库  |     *****     |               |               |    *         |
| 按键绑定|     ☑      |               |               |      ☑        |
| 结构化管道|     ☑      |               |               |              |
| AI交互  |     ☑        |               |               |               |


---

 - 体积与内存占用

|         |    lume       |     bash      |     dash      |     fish      |
|---------|---------------|---------------|---------------|---------------|
| 版本    |    v0.7.1     |    v5.3.0   |    v0.5.12    |   v4.0.2      |
| 体积    |    6.6 MB     |    9.54 MB     |    153.8 KiB  |   21.64 MB    |
| 内存占用    |    7 MB     |    8 MB     |    2 MB  |   12 MB    |

---

### 性能测试
| ![highlight](/images/mem_chart.png) | ![highlight](/images/time_chart.png) |
|------------------------|------------------------|


百万次循环: 比Bash快10多倍, 且内存占用小于Bash

---

### 管道效率
```bash
# 以lf文件管理器中，提取所有命令为例
lf -remote `query $id cmds` >> /tmp/cmds
```

- Bash
```bash
# /tmp/cc.sh
for ((i=0;i<=100;i++))
do
	cat /tmp/cmds | awk -F'\t' 'NR>1 {print $1}'
done
```

- Lume
```bash
# /tmp/cc.lm
for i in 0..100{
  Fs.read /tmp/cmds | String.lines() | List.drop(1) \
    | List.map(x -> {String.split("\t\t", $x) \
    | List.first()}) | print
}
```

---

管道执行效率对比(100次)

```bash
# Bash
> time bash /tmp/cc.sh
________________________________________________________
Executed in  511.61 millis    fish           external
   usr time  230.11 millis    1.69 millis  228.42 millis
   sys time  454.95 millis    0.00 millis  454.95 millis
```

```bash
# Lume
> time lume /tmp/cc.lm
________________________________________________________
Executed in   32.25 millis    fish           external
   usr time   25.65 millis  859.00 micros   24.79 millis
   sys time    6.79 millis  588.00 micros    6.20 millis

```

---

# 语法对比

---

### 变量使用

- Bash

```bash
declare -i a = 10
echo $a

if [ "$a" -gt 0 ]; then
    echo "变量 a 大于零"
fi

```

- Lume

```bash
let a = 10                # 非严格模式，可以省略let
echo $a                   # 非严格模式，可以省略$

if a>0 {
    echo "变量 a 大于零"
}
```

---

### 算术运算

- Bash

```bash
$((a + b))                # 算术加法
[ "$a" == "$b" ]          # 相等比较
"$a$b"                    # 字符串连接
[[ "str" =~ "pattern" ]]  # 包含比较
```

- Lume

```bash
a + b                  # 算术加法
a == b                 # 相等比较
`$a$b`                 # 字符串连接, 也可以使用 a+b
"str" ~: "pattern"     # 包含比较
# 非严格模式下，$符号可以省略
```

---

### 多条件判断

- Bash

```bash
if [[ "$a" -gt 0 && "$b" -lt 0 ]]; then # 注意双括号和引号和分号
    echo "变量 a,b 都大于零"
fi

```

- Lume

```bash
if a>0 && b<0 {
    echo "变量 a,b 都大于零"
}
```

---

### 数组使用

- Bash

```bash
my_array=(1 2 3)
echo "长度：" ${#my_array[@]}       # 符号记得住不？
for value in "${my_array[@]}"; do
    echo $value
done
```

- Lume

```bash
let my_array=[1,2,3]
print "长度：" my_array.len()
for value in my_array{
    print value
}
```

---

### 字典使用

- Bash

```bash
declare -A my_dict
my_dict["key1"]="value1"
my_dict["key2"]="value2"

for key in "${!my_dict[@]}"; do    # 符号啊
    echo "$key: ${my_dict[$key]}"
done
```

- Lume

```bash
let my_dict = {key1:value1, key2:value2}
for key in my_dict.keys() {
    print key my_dict[key]
}
```


---

### 循环语句

- Bash

```bash
sum=0
for (( i=1; i<=10000; i++ )); do     #注意双括号和分号
    sum=$((sum + i))
done

echo "从 1 到 10000 的总和是: $sum"
```

- Lume

```bash
sum=0
for i in 1..10000{
    sum += i
}
print `从 1 到 10000 的总和是: $sum`
```

---

### 匹配语句

- Bash

```bash
if [ $# -eq 0 ]; then          # 注意$#符号
    echo "请提供一个参数。"; exit 1
fi
case "$1" in
    start)                     # 注意单括号语法
        echo "启动服务..."
        ;;                     # 注意双分号语法
    stop)
        echo "停止服务..."
        ;;
    *)
        echo "无效的参数: \$1"
        ;;
esac
```

---

- Lume

```bash
if $argv.len()==0 {
    print "请提供至少一个参数。"; exit 1
}

match $arv[0]{
    start => print "启动服务..."
    stop => print "停止服务..."
    _ => print "无效参数" $argv[0]
}
```

---

### 函数定义

- Bash

```bash
function add{
    a=$1; b=$2            # 无法定义形参，只能从$#中读取变量
    local result=$((a+b))
    echo $result          # 无法返回执行结果，只能打印到标准输出
}
sum=$(add 5 10)           # 从标准输出捕获结果
```


- Lume
```bash
fn add(a,b=0){    # 可直接定义形参，并可以设置默认值
    a + b         # 可直接返回执行结果(最后一句的return可省略)
}
sum=add(5,10)     # 直接接收结果
# Lume还支持高阶函数，装饰器 等 高级功能; 也支持Lambda等简便函数
```

---

### 错误处理

- Bash

```bash
ls /non_exist
# 无法捕获错误，只能通过检测错误码判断执行情况
if [ $? -ne 0 ]; then
    echo "错误: 目录不存在."
fi

```

- Lume

```bash
ls /none_xist ?: e -> pprint e
# Lume支持错误捕获，并提供详细的错误信息:
# 如错误码，错误消息，语法树，表达式等.
# 同时支持错误打印、错误忽略等便捷方式
```

---

### 管道

- Bash
```bash
# bash管道只能处理文本流数据，因此不得不借助三剑客
# 找到tmpfs类型。(还好其他列没有tmpfs字样。)
df -h | awk -F' ' '/tmpfs/{print $1,$2}' 
# 找到已用>1G的。(但如果有1TB呢？)
df -h | awk '$3 ~ /G/ && $3+0 > 1'       
```

- Lume
```bash
# Lume管道支持结构化数据流，因此无须借助三方程序即可高效完成任务
df -h  | Into.table(fs,size,used,rest,up,mp)  \
  | where(fs=='tmpfs') | select(mp,size)
df -h  | Into.table(fs,size,used,rest,up,mp) | .drop(1) \
  | where(used.to_filesize() > 1G )
```

---

- Lume 特有的高级管道
```bash
# 位置管道
1...6 |_ print 'all data come here:' _ 'instead of end'

# 循环派发管道
1...6 |> print 'one data come here:' _ 'instead of end'
#     从此可以避免xargs的复杂参数了！
```

---


### 延迟赋值，多变量赋值

- 延迟赋值
```bash
let a := b+3
let c := ls -l
eval c
```

- 多变量定义
```bash
let a,b,c=1,2,3
let a,b,c=5
```

---

### 更多便捷

- 解构赋值
```bash
let [a,b,*c]=[1,2,3,4,5]
let {name:user,age}={name:wang,age:25}
```
- 提供默认值
```bash
let a = $argv[0] ?: print "no args" && exit  # 无参数直接退出
let a = $argv[0] ?: 0                        # 提供默认值
```

---

- 更多类型
```bash
let p = r'\w+'          # 正则类型
let day = t'2025-7-20'  # 日期类型
let s = 3000M           # 文件大小类型
let range = 0..10       # 区间
let arr = 0...10        # 数组
```

---

### 重载的运算符
```bash
# 利用常规四则运算符处理更多常见任务：
[1,2] + 3         # 数组追加
[1,2] + [3,4,5]   # 数组合并
{a:b} + c         # 字典追加
{a:b} + {c:d}     # 自动合并
"1" + 23          # 自动转字符串，"123"
23 + "1"          # 自动转数字，   24
```

---

### 函数式编程


内置大量实用的函数库, 以实现函数式编程，如
- 集合操作:     `List`
- 文件系统:     `Fs`
- 字符串处理:   `String`, `Regex`
- 时间操作:     `Time`
- 数据转换:     `Into`, `From`
- 数学计算:     `Math`, `Rand`
- UI操作:       `Ui`
- 日志记录:     `Log`

```bash
0...10 | List.filter(x -> x % 2 == 0)
0..10 | .map(x -> x * 2)               # 管道方法
```

---

### 链式调用

```bash
"hello world".split(' ').join(',').to_upper().green()
# 等同于：
String.split(' ',"hello world") | List.join(',') | String.to_upper() | String.green()

data | .filter(x -> x > 0) | .join(',')
# 等同于：
List.filter(x -> x>0, data) | List.join(',')
```
链式调用可以省略相应库名，且可连续调用。

---

# 更多对比
- 语法高亮
- 自动提示
- 本地AI交互
- 详细的错误提示
- 按键绑定，常用命令收藏
- 更多特性，等待您的发现

---

### Thanks

- 文档   [https://lumesh.codeberg.page](https://lumesh.codeberg.page)
- 源代码 [https://codeberg.org/santo/lumesh](https://codeberg.org/santo/lumesh)
