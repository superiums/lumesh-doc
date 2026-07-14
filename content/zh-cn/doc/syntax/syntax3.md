---
title: 语法：语句与控制结构
date: 2025-06-11 19:16:45
highlight: true
weight: 50
tags:
 - syntax
categories:
 - wiki
 - syntax
---
> 基础语法：语句与控制结构

## 四、语句

1. 语句块
    用 `{ }` 表示。一般用于流程控制语句。

2. 作用域隔离语句块
    用 `%{ }` 表示。其中的作用域与外部隔离。

2. 语句组(子命令捕获)
    用括号表示子命令,子命令不新建进程，不隔离变量作用域。
  `echo (len [5,6])`

3. 语句
    用 `;` 或 `enter` 分割

  - **换行符**： `;`或回车。

  - **续行符**：使用 `\` + 换行符跨行书写。

       ```bash
       let long_expr = 3 \
                    + 5  # 等价于 "3 + 5"

       let long_str = "Hello
                       World"  # 等价于 "Hello\n World"
       ```

    注意：*引号内的内容无须续行符*。

4. 注释
    注释以`#`开头

---


## 五、控制结构

### **条件语句**
#### **If 条件**
  支持嵌套：

  `if cond1 { ... } else if cond2 { ... } else { ... }`

  不使用 `then` 关键字，代码块用 `{}` 包裹。

  ```bash
  if True {1} else {if False {2} else {3}}

  if x > 10 {
      print("Large")
  } else if x == 10 {
      print("Equal")
  } else {
      print("Small")
  }
  ```

#### **Match 语句**
   替代bash的switch语句。
   支持在一个分支中匹配多个选项，多个选项之间用`;`或换行分隔。
   支持正则匹配。
   支持字面量直接匹配。并且不会被解读为变量。

  ```bash
  let fruit = "apple"
  match fruit {
    pea,cherry => print "is my favor"
    "*pple" => print "is my love"
    r'\d' => print "is number"
      _  => print "is others"
  }
  ```

### **循环语句**
#### **repeat 循环**
  ```bash
  repeat 10 {a += 1}   # 输出[1,2...10]
  ```
#### **For 循环**

  ```bash
  for i in 0..5 {    # 输出 0,1,2,3,4
      print(i)
  }

  for i,a in [1,5,8] { print i a }  # 带索引遍历

  for i in *.md {      #支持*扩展
    fs.cp i /tmp/
  }
  ```
  支持*扩展
#### **While 循环**
  ```bash
  let count = 0
  while count < 3 {
      print(count)
      count = count + 1
  }
  ```
#### **Loop 循环**
  ```bash
  let count = 0
  let result = loop {             #语句也可以作为表达式使用
    print(count)
    count = count + 1
    if count > 3 {
        break count
    }
  }
  print result
  ```

### 语句表达式

  - 控制语句也可以作为表达式使用：
       ```bash
       let a = if b>0 {5} else {-5}
       ```

  - 条件表达式
      ```bash
      a = c>0 ? t : f
      ```
  支持嵌套。
