---
title: "Why You Need Lume"
layout: slides
date : '2026-08-15T14:45:13+08:00'
weight: 1
highlight: true
fullWidth: true
showTableOfContents: false
---

{{< slide type="hero" tag="Simple Comparison · 2026" sub="Stop fighting tools, start focusing on real problems" >}}
"At 2 AM, you've been wrestling with a Bash script for three hours. It still crashes, with only a cold, uninformative error message—not telling you which line, or why."

{{< /slide >}}

{{< slide type="compare" title="Variables: A Russian Roulette" caption="Say goodbye to guesswork, let code return to intuition" >}}
{{< code side="bash" >}}
```bash
echo $x + $y
# 10 + 5 → "10 + 5"
# String concatenation, not 15
rm $a
# Forgot quotes = deleted wrong file
```
{{< /code >}}
{{< code side="lume" >}}
```bash
x + y
# 15, as expected
# Can still concatenate strings when x is a string
rm $a
# Semantic clarity, no accidental tokenization
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Arrays: Patched-Up Old Clothes" caption="Use arrays, as natural as breathing" >}}
{{< code side="bash" >}}
```bash
${arr[@]}
${#arr[@]}
# How long did these symbols take to memorize?
# Can't nest / pass arguments / pipe
```
{{< /code >}}
{{< code side="lume" >}}
```bash
let arr = [10, "hello", true]
arr[-1]        # true
arr[1..3]      # slice
user.profile.skills[1] # nested access
arr | .max()   # pipeline
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="String Processing: A Nightmare of 100 Spells" caption="Let strings return to their natural essence" >}}
{{< code side="bash" >}}
```bash
${str^^}
[[ $str =~ regex ]]
# [[ ]] or [ ]?
# Do regex need quotes?
"\033[31;1merror\033[m"
# How many escape codes can you remember?
```
{{< /code >}}
{{< code side="lume" >}}
```bash
str.upper()
str.contains("world")
str ~: g'^hello'

"error".red().bold()
# Strings can directly wear colors
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Error Handling: Schrödinger's Gamble" caption="Return control, completely to you" >}}
{{< code side="bash" >}}
```bash
set -e
# Silently disabled inside functions
# Suddenly fails in if statements
# Bash's most notorious trap
echo $?
# The only lifeline, but too fragile
```
{{< /code >}}
{{< code side="lume" >}}
```bash
risky_command ?! | next
# Terminate immediately on error

risky_command ?: (e) -> {
    println e.code
    default_value
}
# 10 error handling operators
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Functions: Parameterless Black Boxes" >}}
{{< code side="bash" >}}
```bash
my_func() {
    echo $1 $2 $3
}
# Can you still read this in six months?
```
{{< /code >}}
{{< code side="lume" >}}
```bash
fn greet(name, greeting="Hello") {
    return greeting + ", " + name
}

fn make_adder(base) { x -> x + base }
let add5 = make_adder(5)
add5(3)  # 8, closure
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Pipeline: A Muddy Path That Only Transmits Text" >}}
{{< code side="bash" >}}
```bash
ls -l | awk '{print $5}'
# Which column is size?
# Mind full of N syntax variations
```
{{< /code >}}
{{< code side="lume" >}}
```bash
fs.ls -l
    | .filter(f -> f.size > 1M)
    | .sort('size')
    | .last(5)
# Structured data flows freely
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Debugging: The Primal Age of echo Magic" caption="Debugging transforms from torture to enjoyment" >}}
{{< code side="bash" >}}
```bash
echo "DEBUG: x=$x"
# The only weapon against bugs
# Primal survival skills
```
{{< /code >}}
{{< code side="lume" >}}
```bash
 7 ▏ } els {
   ▏   ^~~
SyntaxError: expected 'else'
# Compiler-level error messages
# Powerful debugging tools debug/assert/log
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Script Reuse: source Pollutes Global Scope Like a Time Bomb" caption="Clear namespaces, say goodbye to global junkyard" >}}
{{< code side="bash" >}}
```bash
source utils.sh
# MY_CONSTANT / my_func
# Running naked in global namespace
# Silent function name collisions
```
{{< /code >}}
{{< code side="lume" >}}
```bash
use myutils as utils
utils::my_function()
utils::MY_CONSTANT

# 17 built-in modules, load on demand
# list / fs / time / regex / ui ...
```
{{< /code >}}
{{< /slide >}}

{{< slide type="compare" title="Interactive Experience: Terminal Stuck in 1989" >}}
{{< code side="bash" >}}
```bash
# PS1 escape sequences, wrong edits = gibberish
# No native syntax highlighting
# No abbreviation expansion, no AI completion
# Argument completion relies on hundreds of lines of scripts
```
{{< /code >}}
{{< code side="lume" >}}
```bash
set LUME_PROMPT_SETTINGS = { template }
set LUME_ABBREVIATIONS = { gp: 'git push' }
set LUME_HOT_BINDINGS = { CTRL_q: 'exit' }
set LUME_THEME = 'ayu_dark'
# Prompt / abbreviations / hotkeys / AI completion
```
{{< /code >}}
{{< /slide >}}

{{< slide type="perf" title="Performance: Pale Excuse That 'Just Enough Is Fine'" bashMs="2224" lumeMs="199" speedup="11.2" note="1 million iteration sum" >}}
{{< /slide >}}

{{< slide type="compare" title="Full Scenario: Find the 5 Largest Log Files" caption="Code reads like you're clearly describing what you want to do" >}}
{{< code side="bash" >}}
```bash
files=$(find . -name "*.log" -size +1M)
echo "$files" | xargs ls -la \
    | sort -k5 -rn | head -5 \
    | awk '{print $5, $9}' > /tmp/x
# while loop + awk to build report
# Remember to clean up temp files
```
{{< /code >}}
{{< code side="lume" >}}
```bash
let files = fs.ls -lh *.log
    | where(size > 1M)
    | .sort('size') | .last(5)

(into.pretty $files) >> "report.txt"
# No temp files, no text parsing
```
{{< /code >}}
{{< /slide >}}

{{< slide type="cta" title="You Deserve Better Tools" slogan="Lightweight · Ultimate · Modern · Efficient" >}}
Bash was born in 1989, made for its era.
But now, it's **2026**.
Stop fighting tools, start focusing on real problems.

{{< code side="cta" >}}
```bash
fs.ls -lh | where(size > 5M) | .sort('modified')
```
{{< /code >}}

<div class="cta-links" style="max-width:900px;margin:0 auto 40px;">
  <a class="cta-link" href="https://github.com/superiums/lumesh" target="_blank">GitHub Repository</a>
  <a class="cta-link" href="https://www.lumesh.cc.cd" target="_blank">Official Documentation</a>
  <a class="cta-link" href="/doc/quickstart">Quick Start</a>
  <a class="cta-link" href="/topics/bashlife">Further Comparison</a>
</div>

{{< /slide >}}
