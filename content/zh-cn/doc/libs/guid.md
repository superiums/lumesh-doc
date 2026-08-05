# Lume 内置函数使用指南

这份指南只讲"函数名/参数提示里看不出来，但用户必须知道"的通用规则，不重复 `help` 已经列出的具体函数说明。

---

### 1. 四种调用写法都合法

```bash
string.upper "abc"        # 命令式
string.upper("abc")       # 函数式
"abc".upper()             # 方法式（推荐用于链式操作）
"abc" | string.upper()    # 管道式
"abc" | .upper()          # 管道 + 省略库名
```

管道 `.func()` 省略库名时，Lume 会根据传入值的类型自动找对应模块（字符串→`string`，列表→`list`，整数/浮点→`math`，Map→`map`，Set→`bset`，Table→`table` 等）。所以只要你会用一个模块的方法链，其它模块用法完全一样，无需分别记忆。

### 2. 传 `<fn>` 参数时，一律用箭头函数

`filter`/`map`/`find`/`fold`/`sort_by` 等 hint 里写的 `<fn>` 都是这种语法：

```bash
list.filter(data, x -> x > 0)
data | .filter(x -> x > 0)
list.fold(data, (acc, x) -> acc + x, 0)
```

单参数可以省略括号（`x -> ...`），多参数必须加括号（`(acc, x) -> ...`）。hint 里写的形参名（如 `fn(item,acc)`）就是告诉你箭头函数按位置对应传入的值，不需要和 hint 里的名字一致，只需要顺序一致。

### 3. `<num1> <num2>... | <array>` 表示两种传参方式都行

`max`/`min`/`sum`/`average`/`select` 这类函数支持：

```bash
math.max(1, 2, 3)      # 多个位置参数
math.max([1, 2, 3])    # 或者直接传一个数组/列表
```

两种写法效果完全一样，看哪种更顺手。

### 4. 大多数"修改"函数不改原值，而是返回新值

Lume 的内置数据结构（List/Set/Map/Table）操作几乎都是不可变的：`insert`/`remove`/`sort_by`/`shuffle` 等返回的是**新副本**，原变量不会被就地修改。要保留结果需要重新赋值：

```bash
let s2 = s.insert(3)   # s 本身不变，s2 是插入后的新集合
```

### 5. 数字字面量支持进制前缀和下划线分隔符，全局生效

`0x`/`0o`/`0b` 前缀和 `_` 分隔符不是某个转换函数的专属能力，而是脚本语法本身支持的写法，任何地方写数字都能用：

```bash
let n = 0xff_80
let big = 1_000_000
```

`into.int`/`string.to_int` 这类函数除了识别这些字面量格式的字符串外，本身作用是"把其它类型（字符串/浮点/布尔）转成整数"。

### 6. 参数校验很严格，个数不对直接报错

内置函数不会像某些语言那样自动补 `None` 或忽略多余参数，参数个数和 hint 里的 `<必填>`/`[可选]` 不匹配会直接抛错终止执行。写脚本时如果报 "expected N args" 类错误，先核对参数个数。

### 7. 错误处理后缀可以直接接在函数调用后面

内置函数出错时，可以配合错误处理操作符控制行为，而不必用 `try/catch`：

```bash
fs.read("no_such_file") ?: "default content"   # 出错则用默认值
fs.read("no_such_file") ?.                     # 忽略错误，继续执行
risky_call() ?!                                 # 出错立即终止脚本/管道
```

### 8. 想查具体函数怎么用，直接问 `help`

---

# 各模块 精选函数说明

> 这里精选一些模块函数加以说明，作为`help`信息的辅助，让你快速掌握lume内置函数用法。

简单模块（`boolean`、`about`、`filesize`、`bytes`、`console`）用法从函数名即可判断，故略过。

### 顶层函数（无需前缀）

**`eval`/`exec` 处理的是已解析的表达式（`Group`/`Quote` 等），`eval_str`/`exec_str` 处理的是字符串源码**——两组容易混用：

```bash
eval(quote(1+1))          # 对已解析的表达式求值
eval_str("1+1")           # 先解析字符串再求值，并打印 ">> Excuting: ..." 提示
exec_str("let x=1")       # 同 eval_str，但在新建的子环境中执行，不污染当前作用域
```
`eval_str`/`exec_str` 只接受 `String`/`Symbol`/`Variable`/`StringTemplate`（或包裹字符串的 `Group`），传其它类型报错。

**`include`/`import`/`use`

`include`/`import`区别只在于是否共享当前环境**：`include` 用 `env`（会污染/复用当前作用域变量），`import` 用 `env.fork()`（相当于独立命名空间导入）。

```bash
include("lib.lm")   # lib.lm 里定义的变量/函数直接进入当前作用域
import("lib.lm")    # 返回执行结果，但不污染当前作用域（想复用需要显式取返回值）
```
这两个函数都是为了兼容传统编写习惯，更严谨的做法是使用`use`语句导入模块。

区别是：
- `use`使用独立的命名空间
- `use`内的代码不会立即执行，直到显式调用某个函数
- `use`导入的命名空间仅有`fn`和模块依赖的`use`

**`cd -` 回到上一个目录**，依赖内部自动维护的 `LWD` 变量，每次成功 `cd` 都会把旧的当前目录存入 `LWD`。

**`len` 对字符串按 Unicode 字符数（`chars().count()`）而非字节数计算。


### 4. `dig`/`get`（top/list/map 共用）：点号路径可以混合访问 Map key 和 List 下标

路径按 `.` 切分，每一段既可能是 Map 的 key，也可能是 List/Range 的数字下标，取决于当前层级的实际类型。

```bash
dig(data, "users.0.name")   # data.users 是列表，取第0个元素的 name 字段
```
路径不存在会直接报错终止，而不是返回 `none`,如需容错请配合 `?.` 或 `?:`。

---

### 5. `format`：`{name}` 和 `{}` 可以混用

`{name}` 从当前作用域按变量名取值,`{}` 按顺序消费从第 2 个参数开始的位置参数,两种替换互不干扰、可以同时出现在一个模板里 。

```bash
let name = 'lume'
format('hi {name}, result={}', 1+1)   # hi lume, result=2
```
支持对齐/填充语法：
`{:*>10}`

- 其中`:`之前是变量名，省略则从位置参数中取
- `*`为填充符号，可以为任意char，省略则为' '
- `>` / `^` / `>` 分别为左对齐/居中对齐/右对齐
- `10`为对齐宽度，可以是任意整数

### 10. `assert` / `when`：失败终止 vs 条件才执行

```bash
assert(x > 0)                         # 条件为假时抛错终止
when(x > 0, print("positive"))        # 条件为真时才执行，条件为假直接跳过、不报错
```
`assert` 用来做前置校验（错了就停），`when` 用来做可选分支（不满足就静默跳过）。

---

## `string` 模块

**`words` vs `words_quoted`**：`words` 直接按空白切分；`words_quoted` 会先识别 `"..."`/`'...'` 包裹的片段作为整体，再按空白切分剩余部分,适合解析命令行式文本。

```bash
string.words 'a "b c" d'          # ["a", "\"b", "c\"", "d"]
string.words_quoted 'a "b c" d'   # ["a", "b c", "d"]
```

**`grep`**：只在字符串按行 (`\n`) 拆分后做子串匹配，返回匹配到的整行，不支持正则。想用正则请用 `reg.find_all`。

**`pad_start`/`pad_end`/`center`**：第三个可选参数只取其**首字符**作为填充符,传多字符字符串也只生效一个字符;若原字符串长度已达标,直接原样返回、不截断。

**`color`/`color_bg`**：颜色值支持三种写法——`#hex`、颜色名（查 `string.colors()`）、或 `r,g,b` 三段式,不支持 `rgb(...)` 这种带函数名的写法。

### 9. `string.slice`（`s[a:b]` 语法）越界行为一致：静默截断不报错

```bash
"hello".slice(2)        # 从索引2到末尾 -> "llo"
"hello".slice(2, 100)   # 结束索引越界，自动截到字符串末尾，不报错
"hello".slice(-3, -1)   # 支持负数从末尾计数
```
`slice` 起止索引越界或反向（start >= end）时返回空字符串而非报错，与语言内置的 `s[a..b]` range 切片语法行为一致。两者可以互换使用，选哪个纯粹是风格偏好。

### 10. `string.remove_prefix`/`remove_suffix`：找不到就原样返回，不是报错

```bash
"hello.txt".remove_suffix(".txt")   # -> "hello"
"hello.txt".remove_suffix(".md")    # 后缀不匹配，原样返回 "hello.txt"（不报错）
```

---

## `list` 模块

**`map`/`filter` 的双参数回调，参数顺序是 `(index, item)`**：

```bash
list.map(data, (i, x) -> i)        # i 是下标，x 是元素
list.filter(data, (i, x) -> i > 0) # 同样先下标后元素
```

单参数形式 `x -> ...` 仍是只传元素，依据 `map`/`filter` 内部把回调参数按位置 `[index, item]` 传入。

**`fold`/`foldr` 回调参数顺序是 `(acc, item)`**：

```bash
list.fold([1,2,3], (acc, x) -> acc + x, 0)   # x是元素，acc是累加值
```
`init` 参数省略时默认 `0`，用在非数值场景（比如拼列表）容易出错，必须显式传初始值。

**`sort` 的三种写法互斥，按参数类型/个数自动分派**：
`sort` 支持三种第二参数形态：`fn(a,b)->int|bool`、单个字段名字符串、多个字段名（列表或多参数），实现按类型分派 。
`key_fn` 返回 `Integer`（比较结果 -1/0/1）或 `Boolean`（true 表示 a>b），两种返回类型都认。

其中`+` `-`可作为字段前缀表示升序或降序。

`list`/`table`/`string` 中的`sort`内在实现统一，但显然string中没有字段名，list中可以用列索引。

```bash
string.sort(data)                     # 1参：自然排序
list.sort(data, (a,b) -> a - b)     # 2参：比较函数，返回负/0/正整数或布尔
table.sort(data, "name")             # 2参：单字段名，对Map列表按该字段排
list.sort(data, "-name")            # 2参：单字段名，对Map列表按该字段降序排列
list.sort(data, ["a","b"])          # 2参：字段名列表，多级排序
list.sort(data, "a", "b")           # 3+参：等价于多字段名列表
```
比较函数返回 `Integer` 或 `Boolean` 都可以，但语义不同：整数遵循传统 `<0/0/>0`，布尔则 `true`=greater。

**`group`**：key 既可以是 `fn(item)->key` 也可以是字段名字符串，结果统一转成字符串作为分组键。

```bash
list.group(rows, "dept")             # 按字段名分组，要求每行必须是 Map/HMap，否则报错
list.group(items, x -> x % 2)        # 按函数分组，适用任意类型元素
```

**`chunks`**：最后一组不满 `size` 也会保留（不会被丢弃）。

**`zip`**：结果长度取两个列表的较短者，多出的部分直接丢弃，不报错、不补 `none`。

**`remove`/`remove_at`**：找不到匹配值或索引越界时**静默返回原列表**，不会报错，写脚本判断"是否删除成功"时不能靠是否抛错来判断。


### 1. `list.find` / `bset.find` / `map.find` 返回**元素本身**，`list.position` / `table.position`/`rposition` 返回**索引**

`find` 系列（bset/map 模块）是"给条件，拿命中的那条数据"；`table.position`/`rposition` 是"给条件，拿命中的行号"。

`list.find` vs `list.find_last`：都能传值或函数，且都能指定起始下标

```bash
list.find(s, x -> x > 10)                # 返回第一个满足条件的元素值
list.position(s, x -> x > 10)            # 返回第一个满足条件的**索引**
table.position(t, row -> row.age > 18)   # 返回第一个满足条件的行索引(整数)
table.rposition(t, row -> row.age > 18)  # 返回最后一个满足条件的行索引
```
拿到索引后可以配合 `list.get` / `table.get`/`get_cell` 再取具体行/单元格。


### 1. `list.remove` vs `list.remove_at`：按值删 vs 按索引删

```bash
list.remove([1,2,3,2], 2)         # 删除第一个值为2的元素 -> [1,3,2]
list.remove([1,2,3,2], 2, true)   # 第三参数true=删除所有匹配值 -> [1,3]
list.remove_at([1,2,3,4], 1)      # 按索引删，删第1位(0起) -> [1,3,4]
list.remove_at([1,2,3,4], 1, 2)   # 从索引1起连续删2个 -> [1,4]
```
`remove` 找不到匹配值时**静默返回原列表**，不报错；`remove_at` 索引越界同样静默返回原列表 [1](#32-0) [2](#32-1) 。


---

### `bset` 模块

**`first`/`last` 空集合时报错，而不是返回 `none`**，这与很多 list 函数"越界返回 none/空"的惯例不同 ：

```bash
bset.first(s)          # 空集合直接抛错终止脚本
bset.first(s) ?.       # 忽略错误
if bset.is_empty(s) { ... } else { bset.first(s) }
```

**`add`/`remove` 返回全新集合，不修改原集合**（`clone()` 后再 `insert`/`remove`），如果忘记接收返回值，原变量不会变化。

**`BTreeSet` 天然有序**，所以 `first`/`last` 拿到的是"最小值"/"最大值"而非"插入顺序的首/尾项"，`to_list` 转换后的顺序同样是排序后的，不是插入顺序。


---

### `map`/`hmap` 模块

**`merge` 是深度合并，`union` 是浅覆盖**——两者容易混淆但语义完全不同：

```bash
map.union({a:1, b:{x:1}}, {b:{y:2}, c:3})   # {a:1, b:{y:2}, c:3}  -- b直接被整体替换
map.merge({a:1, b:{x:1}}, {b:{y:2}, c:3})   # {a:1, b:{x:1,y:2}, c:3} -- b递归合并
```
`union`/`intersect`/`difference` 只做顶层 key 比较，遇到嵌套 `Map` 值不会递归；`merge` 专门递归处理值也是 `Map`/`HMap` 的字段，其余类型（含冲突类型）直接用后者覆盖前者。`merge` 支持传入 2 个以上的 map，按顺序逐个合并到前一个结果上；若某参数不是 map 类型会被静默当成空 map 处理，不报错。

**`from_list` 只接受 `[[k,v],[k,v],...]` 这种严格二元组列表**，元素不是长度为 2 的 `List` 会报错 ：

```bash
map.from_list([["a",1],["b",2]])   # {a:1, b:2}
map.from_list([["a",1,"x"]])       # 报错
```


---

## `table` 模块

### 1. `where` / `table.filter`：条件里可直接用列名和 `NR`/`NF`

`where` 的第二个参数是**惰性表达式**，执行时会把当前行拆成局部变量再求值：行按 key 展开成同名变量，并统一注入 `NR`（行号）

```bash
fs.ls -lh | .to_table() | where(size > 5K)      # size 是列名，直接当变量用
fs.ls -l | where(NR > 1)                       # 跳过表头行
```

不要把 `where` 的条件写成字符串或函数，它是直接内联表达式，变量名来自数据本身。

如想使用函数，可使用 `table.filter`函数，下面的两种方式可以得到相同结果：

```bash
fs.ls -l | table.filter( x -> x.type == 'file')
fs.ls -l | where(type == 'file')
```


### 7. `select`/`get_column`（table 模块）与顶层 `select`：只对 `Map` 行生效

`select`用于选择多列，返回table。
`get_column`用于选择单列，返回list。

```bash
fs.ls -l | select(name, size)        # 返回table
fs.ls -l | get_column(name)          # 返回list
fs.ls -l | get_column(0)
```


### 2. `table.grep` 是纯文本搜索，`table.filter` 是条件表达式过滤

`grep` 不需要写 `fn`，直接传字符串在整行文本里做子串匹配；`filter` 需要 `fn(row_map)->bool`。

```bash
t | .grep("error")                         # 任意 列中包含 "error" 的行
t | .filter(row -> row.status == "error")  # 精确判断某一列
```
两者容易被当作同义词混用，但 `grep` 无法做数值比较，`filter` 也无法做模糊子串搜索。


### 8. `table.get_cell` / `table.get_column` / `table.select`：单元格、整列、多列

```bash
t.get_cell(row_idx, col)     # 单个值
t.get_column(col)            # 一整列(list)
t.select(col1, col2)         # 多列，仍是 table
```
三者的返回类型依次是"值 → 列表 → 表"，按需要的粒度选择，不要用 `select` 单列后当值用。

---

### 9. `table.from_maps`：表头顺序取决于首次出现顺序，而非字母序

```bash
table.from_maps([{a:1,b:2}, {c:3,a:9}])
# 表头顺序: a, b, c（按各 map 中第一次出现的 key 顺序合并，缺失字段自动填 None）
```
如果几条记录字段不完全一致，结果表会自动补齐缺失列为 `none`，不会报错。


---

### `fs` 模块（`ls`）

`Fs.ls` 的参数是**位置无关的短选项字符串 + 一个路径**，不支持长选项，选项与路径可以任意顺序混写，最后一个非 `-` 开头的参数才被当作路径 ：

```bash
Fs.ls -l -a /tmp        # 等价于
Fs.ls /tmp -l -a
Fs.ls -la /tmp          # 错误！不支持短选项合并写法，必须是 -l -a 分开
```

`-l`（详细信息）会额外带出 `type`/`size`/`modified` 字段，`-t` 控制 `modified` 是时间戳还是 `DateTime`，`-u`/`-m` 分别独立追加 `user`/`mode` 字段（不依赖 `-l`），`-p` 追加完整 `path` 字段——这些标志相互独立叠加，不是互斥的："先选类别再选字段"这种理解是错的，每个标志只负责往结果 map 里加一个字段。

---


### `from` 模块

**`jq` 的 `select(...)` 只支持单一数字比较**（形如 `.field>数字`），不支持逻辑组合（`and`/`or`）、字符串比较或嵌套 `select`，且只能作用于数组元素为对象的场景：

```bash
from.jq('{"list":[{"n":1},{"n":5}]}', '.list|select(.n>2)')   # OK
from.jq(data, '.list|select(.n>2 and .n<10)')                  # 不支持，会静默返回 null
```
`jq` 参数顺序是 `<json_data> <query_string>`，且 `input` 必须是字符串（不能传已解析好的 Map/List），内部会重新 `parse::<JsonValue>()`。

**`from.json`/`from.csv` 对空字符串静默返回 `None`，不报错**，而格式错误时才会报 parser error，二者容易混淆（"没数据"和"数据错"处理方式不同）。

**`from.csv` 遵循当前 `IFS` 环境变量作为分隔符**（默认逗号），如果之前用 `set IFS ';'` 修改过全局分隔符，`csv` 解析结果也会跟着变，容易造成"同一段代码换个环境跑出不同结果"的困惑。

**`from.script` 只解析成表达式（AST），不执行**，需要配合 `eval`/顶层直接调用才会运行：

```bash
let ast = from.script("1+1")
eval(ast)     # 才真正得到 2
```
---

### `reg`（正则）模块

`is_match` 判断的是**整个正则能否在文本中命中**（非锚定整串相等）；想判断"整串完全等于"需要自己在 pattern 前后加 `^`/`$`。

```bash
reg.is_match("2025-01-01", g'\d{4}-\d{2}-\d{2}')     # 只判断整体是否匹配 -> true/false
```


### 3. `reg.find` / `reg.find_all`：单个匹配 vs 全部匹配

```bash
regex.find(p, text)       # 只返回第一个匹配: {start,end,found}
regex.find_all(p, text)   # 返回所有匹配的列表: [{start,end,found}, ...]
```

### 4. `reg.capture` / `reg.captures` / `reg.named_captures`：分组的三种取法

```bash
regex.capture(p, text)          # 第一个匹配的分组列表 [full, g1, g2, ...]
regex.captures(p, text)         # 所有匹配的分组列表 [[full,g1,...], ...]
regex.named_captures(p, text)   # 按 (?<name>...) 命名分组，返回 map
```
用位置分组还是命名分组取决于正则里有没有写 `(?<name>...)`，三者不是互相替代关系。
`regex.named_captures`：模式必须用 `(?<name>...)` 具名捕获组语法

只有命名捕获组会出现在结果 Map 里，普通 `(...)` 捕获组会被忽略 。

```bash
regex.named_captures(r'(?<y>\d{4})-(?<m>\d{2})', '2025-07')
# => {y: "2025", m: "07"}
```

**第一个参数是 `Regex` 还是 `String` 都可以**，`find`/`is_match`/`capture`/`split`/`replace` 都自动识别文本和正则的顺序（谁是 `Regex` 类型谁是 pattern），不强制固定参数位置。

---

### `math` 模块

**`gt`/`ge`/`lt`/`le`/`eq`/`ne` 与运算符 `>`/`<`/`==` 完全等价，只是函数形式**，方便在管道 里当作参数传递：

```bash
x | math.gt(0)    # 等价于 x > 0，但可以直接传函数引用
```
整数和浮点混用比较不会报类型错。

**`bit_shl`/`bit_shr` 的移位量必须在 `0..=63`，越界直接报错**，不像有些语言里越界移位是未定义行为或自动取模：

```bash
math.bit_shl(1, 64)   # Error: shift amount out of range (0-63)
``` 

**`min`/`max`/`sum`/`average` 只要有一个参数是 `Float`，结果就整体变成 `Float`**

`sum` 甚至保留"先累加整数、遇到浮点再切换"的优化逻辑，纯整数输入才会得到整数结果：

```bash
math.sum(1, 2, 3)        # 6 (Integer)
math.sum(1, 2, 3.0)      # 6.0 (Float)
math.min(1, 2)           # 2.0 -- 注意：min/max 内部统一转 f64 计算，永远返回 Float！
```

**`log(base, number)` 参数顺序是"底数在前"**，容易和习惯的 `log(number, base)` 搞反：

lume中的base一般都在第一位，这是为了支持管道。

```bash
math.log(2, 8)   # log_2(8) = 3
``` 

---

### `rand`随机数模块
### 5. `rand.choose` / `rand.sample`：取一个 vs 不重复取多个

```bash
rand.choose(list)       # 随机取 1 个（可能重复调用取到相同结果）
rand.sample(list, 3)    # 一次性无放回取 3 个，结果不重复
```
需要"抽奖不放回"场景用 `sample`，需要"随手挑一个"用 `choose`。


### 11. `rand.ratio` 重载：概率写法因参数个数而不同

```bash
rand.ratio()          # 0参：等价于抛硬币 50%
rand.ratio(0.3)       # 1参：float，表示概率30%
rand.ratio(1, 3)      # 2参：两个整数，表示 1/3 的精确有理数概率
```
1 参和 2 参走的是完全不同的底层算法（`random_bool` vs `random_ratio`），2 参形式对于像 `1/3` 这种浮点无法精确表示的概率更准确 [13](#32-12) 。

---

### 12. `rand.int` 的区间开闭规则依参数个数而变

```bash
rand.int()          # 0参：任意i64（正负都可能）
rand.int(10)        # 1参：闭区间 [0,10]
rand.int(-5)        # 1参负数：闭区间 [-5,0]
rand.int(1, 10)     # 2参：半开区间 [1,10)，10取不到
```
1 参用的是 `..=`（闭区间），2 参用的是 `..`（半开区间），这是最容易踩坑的地方——同一个函数名，参数个数不同导致边界行为不一致 [14](#32-13) 。

---

### `sys` 模块

**`has` 只查当前作用域，`defined` 沿作用域链一直查到根**——容易被当作同义词：

```bash
let x = 1
fn f() { sys.has("x") }      # false，f 的局部作用域里没有 x
fn f() { sys.defined("x") }  # true，向上查到外层作用域找到了 x
``` 

**`max_syntax`/`max_runtime`/`max_usemode` 是同一函数身兼 getter/setter**：不传参数返回当前值，传一个整数则设置新值并返回 `None`：

```bash
sys.max_runtime()      # 查看当前运行时递归上限
sys.max_runtime(2000)  # 调大，脚本递归较深时可以调用它避免 "max recursion" 报错
``` 

**`set_cfm`/`set_strict`/`set_pdm` 会打印 ON/OFF 提示，且立即全局生效**（改的是线程级状态，不是当前作用域变量），在脚本里频繁切换要注意这是**进程级**开关而非局部开关。


---

### `console` 模块

**`mode_raw`/`mode_normal`、`screen_alternate`/`screen_normal` 是成对的手动开关，不会自动恢复**：进入 raw mode 或备用屏幕后，如果脚本中途报错退出，终端会卡在该模式下（不像 `ui.pick` 内部会话结束自动清理），务必确保成对调用或配合 `?:` 兜底。

**`cursor_up`/`down`/`left`/`right` 是同一宏批量生成的，参数含义都是"移动距离"而非"目标坐标"**，和 `cursor_to(x,y)` 的绝对定位语义不同，别混用。

**`keys()` 返回的转义序列表，可以直接和 `read_key()`/`read_line()` 读到的字符串比较**，用来判断用户按了哪个特殊键：

```bash
let k = console.read_key()
let arrows = console.keys()
if k == arrows.up { print("you pressed up") }
```
`read_key` 内部会自动临时开关 raw mode（无需你手动调用 `mode_raw`），一次性读取单个按键后自动恢复 ；而 `read_line`/`read_password` 走的是标准输入行读取，不需要 raw mode。

---


### `ui` 模块

**`pick`/`multi_pick` 的参数个数决定 cfg 位置**：1 个参数纯选项，2 个参数第二个是 msg 字符串或 cfg map，3 个以上参数时最后一个固定当 cfg，其余全部拼成选项列表 ：

```bash
ui.pick(["a","b","c"])                       # 1参：纯选项
ui.pick(["a","b","c"], "choose:")            # 2参：第二个是msg
0...15 | ui.pick({msg:"choose:", page_size:5})  # 2参：第二个是cfg map
ui.pick("a","b","c", {msg:"choose:"})        # 3+参：最后一个固定是cfg,前面全部是选项
```

配置 Map 里能传 `page_size`、`starting_cursor` 等字段，字段名即 `inquire` 库的配置项，具体可用字段：

- `page_size`: Integer
- `starting_cursor`: Integer
- `vim_mode`: Boolean
- `reset_cursor`:  Boolean
- `filter_input_enabled`:  Boolean
- `help_message`: String
- `starting_filter_input`: String

- `formatter`： fn(index,value) -> String
<!--- `scorer`: fn(input,option) -> Integer-->
- `sorter`: fn(integer,integer) -> Integer  : positive for Greater, negative for Lesser

**only for `multi-pick`**
- `all_selected_by_default`: Boolean
- `keep_filter`: Boolean
- `validator`: fn(selected_items) -> Boolean/String : string for invalid msg.


**字符串作为选项时会按 `IFS` 分隔符切分**，而不是整串当一个选项；若开启 `IFS_PCK` 且设置了 `IFS` 变量，用该变量值切分，否则默认按换行 `\n` 切分 ：

```bash
ui.pick("a\nb\nc")     # 等价于 ui.pick(["a","b","c"])
```

**`widget`/`joinx`/`joiny`/`join_flow` 的宽高是自动推算的**：不传 `width`/`height` 时会根据内容和标题长度自动计算，只有需要固定尺寸时才需要显式传参。

---
---
