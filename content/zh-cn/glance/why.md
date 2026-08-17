---
title: "为什么你需要 Lume"
layout: slides
date : '2026-08-15T14:45:13+08:00'
weight: 1
highlight: true
fullWidth: true
showTableOfContents: false  
---

{{< slide type="hero" tag="简单对比 · 2026" sub="停止和工具搏斗，开始专注于真正的问题" >}}
"凌晨两点，你和 Bash 脚本死磕了三个小时。它依然崩溃，理由只有冷冰冰的一句——不告诉你哪一行，不告诉你为什么。"
{{< /slide >}}

{{< slide type="compare" title="变量：一场俄罗斯轮盘赌" caption="告别猜忌，让代码回归直觉" >}}
{{< code side="bash" >}}
```bash
echo $x + $y
# 10 + 5 → "10 + 5"
# 字符串拼接，不是 15
rm $a
# 忘加引号 = 删错文件
```
{{< /code >}}
{{< code side="lume" >}}
```bash
x + y
# 15，就是你想的那样
# x是字符串时，也可以字符串拼接
rm $a
# 语义清晰，不会意外分词
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="数组：打满补丁的旧衣服" caption="使用数组，像呼吸一样自然" >}}
{{< code side="bash" >}}
```bash
${arr[@]}
${#arr[@]}
# 这些符号，花了多久记住？
# 不能嵌套 / 传参 / 管道
```
{{< /code >}}
{{< code side="lume" >}}
```bash
let arr = [10, "hello", true]
arr[-1]        # true
arr[1..3]      # 切片
user.profile.skills[1] # 嵌套访问
arr | .max()   # 管道
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="字符串处理：一百个咒语的噩梦" caption="让字符串，回归自然的本色" >}}
{{< code side="bash" >}}
```bash
${str^^}
[[ $str =~ regex ]]
# [[ ]] 还是 [ ]？
# 正则要不要加引号？
"\033[31;1merror\033[m"
# 着色代码，你能记住几个？
```
{{< /code >}}
{{< code side="lume" >}}
```bash
str.upper()
str.contains("world")
str ~: g'^hello'

"error".red().bold()
# 字符串直接穿戴颜色
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="错误处理：薛定谔的赌博" caption="把控制权，彻底交还给你" >}}
{{< code side="bash" >}}
```bash
set -e
# 在函数里静默失效
# 在 if 里突然失效
# Bash 最臭名昭著的陷阱
echo $?
# 唯一的救命稻草，但太脆弱
```
{{< /code >}}
{{< code side="lume" >}}
```bash
risky_command ?! | next
# 遇错立即终止

risky_command ?: (e) -> {
    println e.code
    default_value
}
# 10 种错误处理操作符
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="函数：没有参数名的黑盒" >}}
{{< code side="bash" >}}
```bash
my_func() {
    echo $1 $2 $3
}
# 半年后还看得懂吗？
```
{{< /code >}}
{{< code side="lume" >}}
```bash
fn greet(name, greeting="Hello") {
    return greeting + ", " + name
}

fn make_adder(base) { x -> x + base }
let add5 = make_adder(5)
add5(3)  # 8，闭包
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="管道：只能传文本的泥泞小路" >}}
{{< code side="bash" >}}
```bash
ls -l | awk '{print $5}'
# 第几列是大小？
# 脑子里装满N种语法
```
{{< /code >}}
{{< code side="lume" >}}
```bash
fs.ls -l
    | .filter(f -> f.size > 1M)
    | .sort('size')
    | .last(5)
# 结构化数据自由流淌
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="调试：靠 echo 大法的原始时代" caption="调试从折磨变成享受" >}}
{{< code side="bash" >}}
```bash
echo "DEBUG: x=$x"
# 对付 Bug 唯一的武器
# 原始时代的生存技巧
```
{{< /code >}}
{{< code side="lume" >}}
```bash
 7 ▏ } els {
   ▏   ^~~
SyntaxError: expected 'else'
# 编译器级错误提示
# 强大的调试工具 debug/assert/log
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="脚本复用：source 污染全局的定时炸弹" caption="清晰的命名空间，告别全局垃圾场" >}}
{{< code side="bash" >}}
```bash
source utils.sh
# MY_CONSTANT / my_func
# 在全局命名空间里裸奔
# 同名函数无声覆盖
```
{{< /code >}}
{{< code side="lume" >}}
```bash
use myutils as utils
utils::my_function()
utils::MY_CONSTANT

# 17 个内置模块，按需加载
# list / fs / time / regex / ui ...
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="交互体验：停留在 1989 年的终端" >}}
{{< code side="bash" >}}
```bash
# PS1 转义序列，改错就乱码
# 无原生语法高亮
# 无缩写展开，无 AI 补全
# 参数补全靠几百行脚本
```
{{< /code >}}
{{< code side="lume" >}}
```bash
set LUME_PROMPT_SETTINGS = { template }
set LUME_ABBREVIATIONS = { gp: 'git push' }
set LUME_HOT_BINDINGS = { CTRL_q: 'exit' }
set LUME_THEME = 'ayu_dark'
# 提示符/缩写/热键/AI 补全
```
{{< /code >}}
{{< /slide >}}

{{< slide type="perf" title="性能：够用就行的苍白借口" bashMs="2224" lumeMs="199" speedup="11.2" note="100万次循环求和" >}}
{{< /slide >}}

{{< slide type="compare" title="完整场景：找出最大的 5 个日志文件" caption="代码读起来，就像你在清晰地描述你想做的事" >}}
{{< code side="bash" >}}
```bash
files=$(find . -name "*.log" -size +1M)
echo "$files" | xargs ls -la \
    | sort -k5 -rn | head -5 \
    | awk '{print $5, $9}' > /tmp/x
# while 循环 + awk 拼接报告
# 记得清理临时文件
```
{{< /code >}}
{{< code side="lume" >}}
```bash
let files = fs.ls -lh *.log
    | where(size > 1M)
    | .sort('size') | .last(5)

(into.pretty $files) >> "report.txt"
# 无临时文件，无文本解析
```
{{< /code >}}
{{< /slide >}}

{{< slide type="cta" title="你值得更好的工具" slogan="Lightweight · Ultimate · Modern · Efficient" >}}
Bash 诞生于 1989 年，它为那个时代而生。
但现在，是 **2026 年**。
停止和工具搏斗，开始专注于真正的问题。

{{< code side="cta" >}}
```bash
fs.ls -lh | where(size > 5M) | .sort('modified')
```
{{< /code >}}

<div class="cta-links" style="max-width:900px;margin:0 auto 40px;">
  <a class="cta-link" href="https://github.com/superiums/lumesh" target="_blank">GitHub 仓库</a>
  <a class="cta-link" href="https://www.lumesh.cc.cd" target="_blank">官方文档</a>
  <a class="cta-link" href="/zh-cn/doc/quickstart">快速开始</a>
  <a class="cta-link" href="/zh-cn/topics/bashlife">进一步对比</a>
</div>

{{< /slide >}}
