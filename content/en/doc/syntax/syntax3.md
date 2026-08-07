---
title: "Syntax: Statements and Control Structures"
date: 2025-06-11 19:16:45
highlight: true
weight: 50
tags:
 - syntax
categories:
 - wiki
 - syntax
---
> Basic Syntax: Statements and Control Structures

## IV. Statements

1. Statement Block
    Represented by `{ }`. Generally used for control flow statements.

2. Scoped Isolated Block
    Represented by `%{ }`. Scope inside is isolated from outside.

    *To modify variables in parent scope, use set statement*

2. Statement Group (Subcommand Capture)
    Represented by parentheses, subcommands don't create new processes, don't isolate variable scope.
  `echo (len [5,6])`

3. Statement
    Separated by `;` or `enter`

  - **Newline**: `;` or Enter key.

  - **Line Continuation**: Use `\` + newline to write across lines.

       ```bash
       let long_expr = 3 \
                    + 5  # equivalent to "3 + 5"

       let long_str = "Hello
                       World"  # equivalent to "Hello\n World"
       ```

    Note: *Content inside quotes doesn't need line continuation marker*.

4. Comments
    Comments start with `#`

---


## V. Control Structures

### **Conditional Statements**
#### **If Condition**
  Supports nesting:

  `if cond1 { ... } else if cond2 { ... } else { ... }`

  No `then` keyword needed, code blocks wrapped in `{}`.

  ```bash
  if true {1} else {if false {2} else {3}}

  if x > 10 {
      print("Large")
  } else if x == 10 {
      print("Equal")
  } else {
      print("Small")
  }
  ```

#### **Match Statement**
   Replaces bash's switch statement.
   Supports matching multiple options in one branch, multiple options separated by `;` or newline.
   Supports regex matching.
   Supports literal direct matching. And won't be interpreted as variables.

  ```bash
  let fruit = "apple"
  match fruit {
    pea,cherry => print "is my favor"
    "*pple" => print "is my love"
    g'\d' => print "is number"
      _  => print "is others"
  }
  ```

### **Loop Statements**
#### **Repeat Loop**
  ```bash
  repeat 10 {a += 1}   # outputs [1,2...10]
  ```
#### **For Loop**

  ```bash
  for i in 0..5 {    # outputs 0,1,2,3,4
      print(i)
  }

  for i,a in [1,5,8] { print i a }  # iterate with index

  for i in *.md {      # supports * expansion
    fs.cp i /tmp/
  }
  ```
  Supports * expansion
#### **While Loop**
  ```bash
  let count = 0
  while count < 3 {
      print(count)
      count = count + 1
  }
  ```
#### **Loop Statement**
  ```bash
  let count = 0
  let result = loop {             # statement can also be used as expression
    print(count)
    count = count + 1
    if count > 3 {
        break count
    }
  }
  print result
  ```

### Statement Expressions

  - Control statements can also be used as expressions:
       ```bash
       let a = if b>0 {5} else {-5}
       ```

  - Conditional Expression
      ```bash
      a = c>0 ? t : f
      ```
  Supports nesting.
