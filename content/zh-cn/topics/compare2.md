+++
date = '2026-07-25T14:12:13+08:00'
title = '从Bash到Lume（二）'
weight = 3
+++

## 一、变量系统：四层语义 vs bash 的混乱


bash 的变量系统只有两个维度（全局/ local ），且  local  只在函数内有效。lume 有四个明确语义的关键字：

```bash
let x = 1       # Declare：在当前作用域声明（块内自动为局部变量）
x = 2           # Assign：修改当前或父作用域已有变量
set x = 3       # SetParent：穿透块边界修改外层变量
export x = 4    # Export：写入根环境 + 同步到系统环境变量
```

 set  的实现会沿  Environment  父链向上查找并修改，而  let  在  IN_LOCAL  状态下写入  State  的局部变量表，完全不影响外层：


实际效果：

```bash
let i = 1
{
    let i = 2       # 局部 i，不影响外层
    i = 3           # 修改局部 i
    set i = 4       # 修改外层 i
}
# 外层 i 现在是 4
```

bash 中要实现类似效果需要 `declare -g`、`local`、`nameref` 等多种机制混用，且行为在函数外完全不同。


---


## 二、延迟赋值（`:=`）：元编程的基础


bash 没有任何延迟求值机制（`eval` 只能处理字符串）。lume 的 `:=` 将表达式本身（AST 节点）存储为 `Quote`，不立即求值：


```bash
let x := 2 + 3      # x 存储的是 AST "2 + 3"，不是 5
str(x)              # → "2 + 3"（显示表达式文本）
eval(x)             # → 5（手动触发求值）


# 实用场景：存储命令模板
let cmd := ls -la $dir    # 存储命令，dir 在执行时才绑定
```


---


## 三、alias 系统：比 bash alias 强大得多


bash 的 `alias` 只是简单的文本替换，不能携带参数，不能是函数。lume 的 `alias` 可以绑定任意表达式，并在调用时智能合并参数：


```bash
alias fls = fs.ls('-l')       # 带预设参数的函数调用
alias each = list.map()       # 模块函数
alias int = into.int()        # 类型转换
alias cds = sys.cds()         # 系统函数
alias doc = xdg-open https://lumesh.codeberg.page  # 命令+参数
```

调用时，alias 的参数和用户传入的参数会被合并：

alias 存储在独立的线程局部  ALIAS_MAP  中，与变量环境完全隔离，不会被子作用域覆盖：

 


## 四、IFS 精细控制：位掩码而非全局开关


bash 的  IFS  是全局的，影响所有分词行为，经常导致意外的字符串分割。lume 用  LUME_IFS_MODE  位掩码精确控制 IFS 在哪些场景生效：


```bash
# IFS affect: 0:never; 2:cmd args; 4:for; 8:string.split; 16:csv; 32:pick; 62:all
set LUME_IFS_MODE = 2    # 只在命令参数中分割，其他场景不受影响
```


各场景独立控制：


| 位 | 场景 | 说明 |
|----|------|------|
| `1<<1` | `IFS_CMD` | 命令字符串参数分割 |
| `1<<2` | `IFS_FOR` | `for i in str` 迭代分割 |
| `1<<3` | `IFS_STR` | `string.split` 默认分隔符 |
| `1<<4` | `IFS_CSV` | CSV 解析 |
| `1<<5` | `IFS_PCK` | `ui.pick` 选项分割 |


---

## 五、索引与切片：统一且强大


bash 的数组索引只支持非负整数，字符串切片用 `${var:start:len}` 语法，不支持负数索引。lume 的索引/切片系统统一且支持负数：


```bash
let arr = [10, 20, 30, 40, 50]
arr[0]          # → 10（正向索引）
arr[-1]         # → 50（负数索引，从末尾）
arr[1..3]       # → [20, 30]（Range 切片）
arr[-2.._]      # → [40, 50]（负数切片到末尾）
arr[_.._:2]     # → [10, 30, 50]（步长切片）


"hello"[1..4]   # → "ell"（字符串切片）
"hello"[-3.._]  # → "llo"（字符串负数切片）


map[key]        # Map 键访问
map.key         # Property 访问（不求值 key）
```


`Property`（`.`）和 `Index`（`[]`）是两个不同的 AST 节点，前者不对右侧求值（适合 Map 键访问），后者完全求值： 


---


## 六、`~:` 包含操作符：统一的成员测试


bash 没有统一的包含测试，需要 `[[ $str == *substr* ]]`、`[[ " ${arr[@]} " =~ " $elem " ]]` 等不同语法。lume 用 `~:` 统一处理所有容器：


```bash
"hello" ~: "ell"        # 字符串包含子串 → true
"hello" ~: r'h.l'       # 字符串匹配正则 → true
[1,2,3] ~: 2            # 列表包含元素 → true
S{1,2,3} ~: 3           # 集合包含元素 → true
1..10 ~: 5              # Range 包含整数 → true
{a:1} ~: "a"            # Map 包含键 → true
     !~:                     # 取反版本
```


---


## 七、`%` 百分比字面量 和 `FileSize` 字面量


bash 完全没有这两种字面量。lume 在解析器层面直接支持：


```bash
50%             # → 0.5（Float）
3M              # → FileSize(3MB)
1.5G            # → FileSize(1.5GB)
512K            # → FileSize(512KB)


# FileSize 支持比较和单位转换
3M > 1G         # → false
3M == 3M        # → true
filesize.mb(3M) # → 3.0（转为 MB 数值）
filesize.b(1K)  # → 1024（转为字节数）
```

`FileSize` 实现了 `PartialOrd`，可以直接用 `>` `<` 比较，内部统一转换为字节数： 


---


## 八、`^` 后缀：绕过内置，强制调用系统命令


bash 没有这个问题，因为 bash 没有内置函数库。lume 有大量内置函数（如 `sort`、`rev`），当用户需要调用同名系统命令时，用 `^` 后缀：


```bash
sort list.lm        # 调用 lume 内置 list.sort
sort^ list.lm       # 强制调用系统 /usr/bin/sort
rev^ file.txt       # 强制调用系统 rev 命令
```

---


## 九、`!` 后缀：命令风格调用函数


lume 函数可以用两种语法调用，`!` 后缀允许用命令风格（空格分隔参数）调用函数：


```bash
string.green("hello")      # 函数调用风格
string.green! "hello"      # 命令风格（等价）
println! "hello" "world"   # 内置函数命令风格
```

---


## 十、`match` 的多模式分支


bash 的 `case` 每个分支只能有一个模式（或用 `|` 分隔的字符串模式）。lume 的 `match` 每个分支可以有多个模式（逗号分隔），且支持正则、Range、精确值混合：


```bash
match x {
    1, 2, 3 => "small"          # 多值匹配
    4..10 => "medium"           # Range 匹配
    r'^\d+$' => "numeric str"  # 正则匹配
    "none", none => "empty"     # 字符串+none
    _ => "other"                # 通配
}
```

---


## 十一、模板字符串：支持任意表达式


bash 的 `${}` 只能展开变量（加上有限的参数展开）。lume 的反引号模板支持任意表达式：


```bash
let name = "world"
`hello $name`                    # 简单变量插值
`result: {2 + 3}`               # 表达式插值 → "result: 5"
`files: {fs.ls('.') | len()}`   # 管道表达式插值
`time: {time.now()}`            # 函数调用插值
```


## 十二、 printf  命名参数


bash 的  printf  只支持位置参数（ %s 、 %d ）。lume 的  format 支持命名参数和位置参数混合：


```bash
let name = 'lumesh'
let born = 2025
format 'hi,{name} was borned on {born}, age is {}' 2026 - 2025
# 输出: hi,lumesh was borned on 2025, age is 1
```

---

## 十三、`eval`/`exec`/`eval_str`/`exec_str`：四种动态执行


bash 只有一个 `eval`（字符串求值，在当前环境）。lume 有四种：


```bash
eval(x)         # 对 AST 表达式求值（用于延迟赋值），当前环境
exec(x)         # 对 AST 表达式求值，新环境（隔离）
eval_str("...")  # 对字符串解析并求值，当前环境
exec_str("...")  # 对字符串解析并求值，新环境（隔离）
```

`exec_str` 是热键系统的核心，用于从历史记录或书签中执行命令字符串： 

---

## 十四、交互式特性：bash 完全没有的能力


### 14.1 缩写展开（Abbreviations）


类似 fish shell 的 abbr，在输入空格时自动展开：


```bash
set LUME_ABBREVIATIONS = {
    xi: 'doas pacman -S',
    xup: 'doas pacman -Syu',
}
# 输入 "xi " 自动展开为 "doas pacman -S "
```

### 14.2 可编程热键


热键绑定到任意 lume 代码字符串，支持修饰键组合：


```bash
set LUME_HOT_BINDINGS = {
    CTRL_q: 'exit',
    ALT_m: save_cmdmark,
    CTRL_SHIFT_M: select_cmdmark,
    CTRL_SHIFT_D: select_dirmark,
    ALT_e: fix_typos,
    CTRL_SHIFT_t: timestamp,
    'CTRL_/': menu,
}

``` 

### 14.3 可编程slash cmd（斜杠命令）
```bash
    set LUME_SLASH_BINDINGS = {
        sm: save_cmdmark,
        sd: save_dirmark,
        m: select_cmdmark,
        d: select_dirmark,
        g: fuzzy_go,
        o: open_file,
        e: edit_file,
        sc: search_content,
        cm: git_commit,
    }
```

### 14.4 可编程提示符（Lambda 提示符）


提示符可以是 Lambda 函数，每次显示时动态计算，接收 `dir` 和 `ctx`（包含 `cfm`、`strict` 等状态）：


```bash
set LUME_PROMPT_TEMPLATE = (dir, ctx) -> {
    string.blue($dir) + ' |'.green().bold()
    + ($ctx.cfm ? 'CFM'.green() + '|' : '')
    + (if (fs.exists '.git') {git branch --show-current | .cyan()} else '')
    + '> '.green().bold()
}
```

### 14.5 语法高亮主题


内置三套主题（`one_dark`、`ayu_dark`、`light`），可通过 Map 自定义覆盖：


```bash
LUME_THEME = 'ayu_dark'
LUME_THEME_CONFIG = { keyword: COLOR.GREEN }
```

### 14.6 自动补全/提示
命令/参数/路径/历史/内置函数 均可自动补全/提示

### 14.7 AI 补全

- 通过`LUME_AI_CONFIG`配置ai后端
- `Alt+i` ai提示
- `Alt+o`或`Alt+Enter` ai生成


---


## 十五、内置模块库：bash 需要外部工具的功能


lume 有 20+ 个内置模块，全部懒加载： 


| 模块 | 功能 | bash 对应 |
|------|------|-----------|
| `string` | 50+ 字符串操作 + 颜色/样式 | `sed`/`awk`/`tr` + `tput` |
| `list` | `map`/`filter`/`foldl`/`sort`/`group`/`unique`... | 无原生，需 `awk` |
| `fs` | 文件读写/列目录/复制/移动/删除 | 外部命令 |
| `regex` | `find`/`find_all`/`capture`/`captures`/`capture_name` | `grep`/`sed` |
| `time` | 时间解析/格式化/运算 | `date` |
| `math` | 数学函数 | `bc` |
| `ui` | 交互式 picker/confirm/多选 | `fzf`/`dialog` |
| `rand` | 随机数/随机选择 | `$RANDOM` |
| `into` | 类型转换（`to_int`/`to_table`/`to_csv`...） | 无 |
| `filesize` | FileSize 单位转换 | 无 |
| `bset` | 集合操作（交/并/差） | 无 |
| `log` | 日志记录 | `logger` |
| `sys` | 系统信息/进程控制 | 外部命令 |
| `console` | 终端控制/密码输入 | `stty` |


`string` 模块内置完整的终端颜色支持（8色/256色/TrueColor/命名颜色），可直接链式调用：


```bash
"error".red().bold()
"info".color("steelblue")
"warn".clr(214)
```

---


## 十六、`for` 的上下文感知返回值


bash 的 `for` 永远不返回值。lume 的 `for` 在赋值或管道上下文中自动收集结果为 List：


```bash
# 语句上下文：不收集结果
for i in 1..5 { print i }


# 赋值上下文：返回 List
let squares = for i in 1..5 { i * i }   # → [1, 4, 9, 16, 25]


# 管道上下文：返回 List 并继续管道
for i in 1..10 { i * 2 } | list.filter(x -> x > 10)
```

---


## 十七、`select` 和 `get`：类 SQL 操作


bash 完全没有结构化数据操作。lume 内置：


```bash
# select：从 List[Map] 中选择列（类 SQL SELECT）
let data = fs.ls -l
select data name size modified    # 只保留这三列


# get：点路径访问嵌套结构
let config = {db: {host: "localhost", port: 5432}}
get config "db.host"    # → "localhost"
get config "db.port"    # → 5432
```


---


## 十八、`throw`：主动抛出错误


bash 没有异常机制，只能用 `exit 1` 或 `return 1`。lume 可以抛出带消息的运行时错误，并被 `?:` 捕获：


```bash
fn validate(x) {
    if x < 0 { throw "value must be non-negative" }
    x
}


validate(-1) ?: (e) -> { println "Error:" e.msg }
```

---


## 十九、`IS_INTERACTIVE` / `IS_LOGIN`：环境感知


bash 需要通过 `[[ $- == *i* ]]` 或检查 `$0` 是否以 `-` 开头来判断。lume 直接提供布尔变量：


```bash
if IS_INTERACTIVE {
    # 只在交互模式下执行的配置
    set LUME_PROMPT_TEMPLATE = ...
}
if IS_LOGIN {
    set PATH = '/usr/local/bin:/usr/bin:/bin'
}
```

---


## 二十、错误信息质量：编译器级别 vs 无意义输出


bash 的错误信息极其简陋（如 `bash: syntax error near unexpected token`，不指出具体位置）。lume 的语法错误显示：
- 错误前后各 3 行上下文
- 精确的行号和列号
- 红色高亮错误位置
- `^~~~` 指示箭头
- 具体的错误描述和修复建议 


运行时错误包含 `code`、`msg`、`expr`（表达式文本）、`ast`（AST 表示）、`type`、`depth` 六个字段，可编程处理。

---

## 总结：核心设计哲学差异


维度              Bash                    Lume
─────────────────────────────────────────────────────────
数据模型          字符串流                结构化值（12+类型）
变量语义          全局/local(函数内)      4层(let/assign/set/export)
延迟求值          eval字符串              :=存储AST，eval触发
错误处理          exit code + trap        7种操作符 + 结构化错误Map
管道数据          字节流                  结构化值（List/Map直传）
函数能力          位置参数$1..$N          默认值/可变参数/闭包/装饰器/柯里化
模式匹配          case(字符串glob)        match(值/正则/Range/多模式)
集合操作          数组(有限)              List/Map/HMap/BSet + 高阶函数
交互特性          readline基础            缩写/热键/AI补全/主题/Lambda提示符
内置库            极少                    20+模块，含颜色/UI/时间/正则/文件
跨平台            Unix only               Linux/macOS/Windows
