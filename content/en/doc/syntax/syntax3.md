---
title: Statements and Control Structures
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

1. **Statement Blocks**
    Represented by `{}`. Generally used for flow control statements.

2. **Statement Groups (Subcommand Capture)**
    Subcommands are represented in parentheses; subcommands do not create new processes and do not isolate variable scopes.
  `echo (len [5,6])`

3. **Statements**
    Separated by `;` or `enter`.

  - **Newline**: `;` or enter.

  - **Continuation**: Use `\` + newline to write across lines.

       ```bash
       let long_expr = 3 \
                    + 5  # equivalent to "3 + 5"

       let long_str = "Hello
                       World"  # equivalent to "Hello\n World"
       ```

    Note: *Content within quotes does not require a continuation character*.

4. **Comments**
    Comments start with `#`.

---

## V. Control Structures

### **Conditional Statements**
#### **If Condition**
  Supports nesting:

  `if cond1 { ... } else if cond2 { ... } else { ... }`

  Does not use the `then` keyword; code blocks are wrapped in `{}`.

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

#### **Match Statement**
   Replaces the switch statement in bash.
   Supports matching multiple options in one branch, separated by `;` or newlines.
   Supports regex matching.
   Supports literal direct matching, which will not be interpreted as variables.

  ```bash
  let fruit = "apple"
  match fruit {
    pea,cherry => print "is my favor"
    "*pple" => print "is my love"
    r'\d' => print "is number"
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

  for i in [1,5,8] { print i }

  for i in *.md {      # supports * expansion
    fs.cp i /tmp/
  }
  ```
  Supports * expansion.
#### **While Loop**
  ```bash
  let count = 0
  while count < 3 {
      print(count)
      count = count + 1
  }
  ```
#### **Loop Loop**
  ```bash
  let count = 0
  let result = loop {             # statements can also be used as expressions
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
[1] https://www.lix.polytechnique.fr/~liberti/public/computing/prog/c/C/SYNTAX/statements.html
[2] https://en.wikipedia.org/wiki/Block_(programming)
[3] https://mc-stan.org/docs/2_19/reference-manual/statement-blocks-and-local-variable-declarations.html
[4] https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/block
[5] https://www.alooba.com/skills/concepts/programming/programming-concepts/control-structures/
[6] https://www.unison-lang.org/docs/language-reference/blocks-and-statements/
[7] https://documentation.sas.com/doc/hi/pgmsascdc/v_042/grstatug/p11cgiqvwqcmchn14bgmjnx4d7h2.htm
[8] https://www.ibm.com/docs/en/i/7.5.0?topic=statements-block
[9] https://docs.oracle.com/javase/specs/jls/se7/html/jls-14.html
[10] https://www.scribd.com/document/842472631/Basic-Syntax-and-Control-Structures
[11] https://www.geeksforgeeks.org/dsa/control-structures-in-programming-languages/
[12] https://www.php.net/manual/en/language.control-structures.php
[13] https://docs.getdbt.com/reference/dbt-jinja-functions/statement-blocks
[14] https://en.wikibooks.org/wiki/BASIC_Programming/Beginning_BASIC/Control_Structures/IF...THEN...ELSEIF...ELSE
[15] https://www.reddit.com/r/learnprogramming/comments/y4jmc0/what_are_control_flow_control_statements_and/
[16] https://www.alice.org/resources/lessons/control-structures-overview/
[17] https://help.deltek.com/product/ajera/8/content/About_statement_groups.htm
[18] https://userapps.support.sap.com/sap/support/knowledge/en/2084915
[19] https://medium.com/@shirhabeel_awan/control-structures-loops-conditional-statements-and-functions-bf10231519b3
[20] https://metana.io/blog/javascript-control-structures/
[21] https://www.restaurant-hospitality.com/new-restaurants/gina-luari-launches-the-statement-group-in-connecticut
[22] https://us-kb.sage.com/portal/app/portlets/results/view2.jsp?k2dockey=222924150017120
[23] https://community.qualtrics.com/survey-platform-54/how-do-i-make-statement-group-titles-show-in-the-matrix-question-31700?postid=108288
[24] https://www.w3schools.com/sql/sql_groupby.asp
[25] https://www.alooba.com/skills/concepts/csharp-243/syntax-and-structure/
[26] https://www.thestatementgroup.com/
[27] https://docs.particle.io/reference/device-os/api/language-syntax/control-structures/
[28] https://www.cs.fsu.edu/~myers/c++/notes/control1.html
[29] https://www.kdnuggets.com/python-basics-syntax-data-types-and-control-structures
[30] https://docs.scala-lang.org/scala3/book/control-structures.html
[31] https://dictionary.cambridge.org/us/dictionary/english/comment
[32] https://www.vocabulary.com/dictionary/comment
[33] https://en.wikipedia.org/wiki/Comment_(computer_programming)
[34] https://support.google.com/youtube/answer/6000976?hl=en
[35] https://www.dictionary.com/browse/comment
[36] https://www.merriam-webster.com/dictionary/comment
[37] https://socialbee.com/glossary/comment/
[38] https://www.thesaurus.com/browse/comment
