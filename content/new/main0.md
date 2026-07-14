1. Lumesh 核心介绍（基于实际架构）
LUME 设计理念详解：结合 README-cn.md 中的四大支柱（Lightweight/Ultimate/Modern/Efficient） README-cn.md:31-46
双二进制架构说明：lume（完整交互式 shell）与 lume-se（轻量脚本执行器）的区别与使用场景 README-cn.md:181-190
性能基准数据：引用实际测试结果（100万次循环 224ms） README-cn.md:197-206
解决的核心问题：传统 shell 的语法限制、错误处理缺陷、结构化数据处理困难
2. 标准文档体系（基于实际帮助系统）
快速入门指南
安装方式（脚本/预编译/源码） README-cn.md:156-178
基础配置（config.lm 结构说明） config.lm:61-119
语言参考手册
语法设计（JS-like 特性展示） README-cn.md:72-79
表达式类型（基于 Expression 枚举的 50+ 类型）
内置模块索引
模块列表获取方式（help libs 命令） top.rs:99-149
核心模块详解（list/fs/string/time 等） README-cn.md:120-129
3. 专题深入探讨（基于实际特性）
管道系统专题
7 种管道操作符详解（|、|>、|^、|_、<<、>>、>!） README-cn.md:104-117
结构化数据流处理实例
错误处理机制
7 种捕获操作符（?.、?:、?+、??、?>、?!、?~） README-cn.md:94-101
实际错误恢复策略
高级语言特性
函数装饰器（中间件风格） README-cn.md:134-138
模块系统与命名空间（use 语法） README-cn.md:141-147
占位符与位置管道（_ 占位符） README-cn.md:106-107
命令补全系统
补全数据结构（CSV 格式规范） RULE.md:1-40
自定义补全方法（LUME_COMPLETION_DIR 配置） config.lm:61-62
4. API 参考手册（基于实际实现）
顶层函数 API
通过 help tops 获取的函数列表 top.rs:152-169
核心函数详解（print、eval、exit 等）
模块函数 API
按模块分类的函数参考（使用 help <lib>.<func> 格式） top.rs:170-181
参数说明与返回值类型
系统 API
环境变量管理（sys 模块）
文件系统操作（fs 模块实例） fn_fs-test.lm:44-59
5. 实战案例集（基于测试与配置）
脚本开发示例
实际测试用例分析（fn_fs-test.lm） fn_fs-test.lm:1-40
函数式编程范式展示
配置定制指南
热键绑定配置 config.lm:71-81
别名与缩写设置 config.lm:93-106
性能优化实践
局部变量域使用（v0.11.6 特性）
懒加载模块优化

6. 针对不同背景用户的快速上手向导
6.1 Bash用户快速迁移指南
命令兼容性对比表
基本命令：ls、cd、pwd 等直接兼容
管道操作：从 | 扩展到结构化管道 | .to_table() | where() README-cn.md:104-117
变量赋值：从 VAR=value 到 let VAR = value README-cn.md:72-79
CFM模式无缝过渡
默认CFM模式让你像bash一样输入命令 CHANGELOG.md:444-454
临时切换：使用 : 前缀进入编程模式进行计算 runtime.rs:31-43
错误处理升级
从 && 和 || 到 ?~ 操作符 CHANGELOG.md:25-35
7种错误捕获操作符替代复杂的条件判断 README-cn.md:94-101
6.2 JavaScript用户快速上手指南
语法相似性映射
对象/数组操作：let user = {name: "Alice"} 完全兼容 README-cn.md:72-79
解构赋值：let {name, age} = user 直接可用
箭头函数：x -> x * 2 语法类似JS
函数式编程增强
链式调用：data.filter().map().reduce() README.md:78-84
管道操作：比JS更强大的数据流处理 README-cn.md:104-117
模块系统对比
从 import 到 use module as alias README-cn.md:141-147
内置模块：list、string、fs等类似JS标准库
优化：1. Lumesh核心介绍（强化CFM）
CFM（Command First Mode）核心特性
设计理念：日常命令使用无需引号，编程模式按需切换 CHANGELOG.md:444-454
双模式切换：: 进入编程模式，> 强制CFM模式 runtime.rs:31-43
配置控制：通过 LUME_CFM 设置默认模式 config.lm:54-55
实际使用场景演示
# CFM模式：像传统shell一样  
ping 1.1.1.1  
ls -la | grep ".lm"  
  
# 编程模式：使用 `:` 前缀  
:let result = 1 + 2  
:[1,2,3] | list.map(x -> x * 2)
优化：3. 专题深入探讨（新增CFM专题）
3.1 CFM模式深度解析
CFM词法分析器
放宽符号规则，允许数字作为符号 tokenizer.rs:1357-1390
支持的操作符：|、+、?、: 等 CHANGELOG.md:196-203
模式切换机制
运行时动态切换：set_cfm(true/false) sys_lib.rs:273-288
临时切换实现：前缀检测与状态管理 runtime.rs:31-43
CFM最佳实践
何时使用CFM：日常命令、系统管理
何时切换编程模式：数据处理、算法实现
混合使用技巧：管道中结合两种模式
新增：7. 入门实战教程（CFM重点）
7.1 第一次使用Lumesh
启动与模式认知
启动lume交互式shell README-cn.md:181-190
观察提示符中的CFM标识 config.lm:28-36
命令模式体验
# 直接输入命令，就像bash  
ls  
pwd  
git status  
  
# 管道操作  
ls -l | where(size > 1K)
编程模式切换
# 使用 : 前缀进入编程模式  
:let x = 10  
:let y = 20  
:x + y  # 显示结果  
  
# 使用 > 强制命令模式  
>echo "hello"
7.2 实用任务演练
文件管理任务
# CFM模式：传统命令  
find . -name "*.lm"  
  
# 编程模式：结构化处理  
:fs.ls(".") | list.filter(x -> x.ends_with(".lm")) | list.each(x -> println(x))
系统监控任务
# 混合模式使用  
ps aux | where(cpu > 5.0) | select(pid,cmd) | table()
优化：2. 标准文档体系（新增用户向导）
快速迁移指南
Bash用户迁移checklist
JavaScript用户迁移checklist
常见问题与解决方案
