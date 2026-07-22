---
title: Lumesh list 库
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - list
 - array
categories:
 - wiki
 - libs
---

list 模块是 Lumesh 中最重要的数据处理模块之一，提供了丰富的列表操作函数，支持函数式编程范式。所有函数都采用不可变设计，返回新的列表而不修改原列表。

## 功能概览

| 功能类别 | 主要函数 | 用途 |
|---------|---------|------|
| **打印输出** | `pprint` | 美化打印列表 |
| **数学统计** | `max`, `min`, `sum`, `average` | 数值计算和统计 |
| **基础操作** | `get`, `len`, `insert`, `rev`, `flatten` | 基础数据操作 |
| **元素访问** | `first`, `last`, `at`, `take`, `drop` | 获取列表元素 |
| **查找操作** | `contains`, `find`, `find_last` | 元素查找和定位 |
| **修改操作** | `append`, `prepend`, `unique`, `split_at`, `sort`, `group`, `remove_at`, `remove`, `set` | 列表结构修改 |
| **创建操作** | `concat`, `from` | 列表创建和组合 |
| **函数式操作** | `map`, `filter`, `filter_map`, `any`, `all`, `each` | 高阶函数处理 |
| **转换操作** | `join`, `to_map`, `items` | 数据格式转换 |
| **结构操作** | `transpose`, `chunk`, `zip`, `unzip`, `foldl`, `foldr` | 复杂结构变换 |

## 打印输出函数

**`pprint <list>`** - 美化打印列表
- **参数**：`list` (必需): `list` - 要打印的列表
- **用途**：以表格形式美化显示列表数据



## 数学统计函数

**`max <num1> <num2> ... | <array>`** - 获取最大值
**`min <num1> <num2> ... | <array>`** - 获取最小值
**`sum <num1> <num2> ... | <array>`** - 求和
**`average <num1> <num2> ... | <array>`** - 求平均值
- **参数**：可变数量的数字参数或数字数组
- **返回**：相应的计算结果
- **示例**：
  - `list.max(1, 5, 3)` 返回 `5`
  - `list.sum([1, 2, 3, 4])` 返回 `10`



## 基础操作函数

**`get <path> <map|list|range>`** - 使用点号路径从嵌套结构中获取值
- **参数**：
  - `path` (必需): `String` - 访问路径，支持点号分隔
  - `data` (必需): `map|list|range` - 要访问的数据结构
- **返回**：`Any` - 指定路径的值
- **示例**：`list.get("0.name", [{"name": "Alice"}])` 返回 `"Alice"`



**`len <list>`** - 获取列表长度
- **参数**：`list` (必需): `list` - 要计算长度的列表
- **返回**：`integer` - 列表的长度



**`insert <index> <value> <list>`** - 向列表中插入元素
- **参数**：
  - `index` (必需): `integer` - 插入位置的索引
  - `value` (必需): `Any` - 要插入的值
  - `list` (必需): `list` - 目标列表
- **返回**：`list` - 插入元素后的新列表



**`rev <list>`** - 反转列表
- **参数**：`list` (必需): `list` - 要反转的列表
- **返回**：`list` - 反转后的新列表



**`flatten <collection>`** - 展平嵌套列表
- **参数**：`collection` (必需): `list` - 要展平的嵌套列表
- **返回**：`list` - 展平后的列表



## 元素访问函数

### 基础访问操作

**`first <list>`** - 获取列表的第一个元素
- **参数**：`list` (必需): `list` - 要访问的列表
- **返回**：`Any` - 第一个元素
- **错误**：空列表时抛出错误
- **示例**：`list.first([1, 2, 3])` 返回 `1`



**`last <list>`** - 获取列表的最后一个元素
- **参数**：`list` (必需): `list` - 要访问的列表
- **返回**：`Any` - 最后一个元素
- **错误**：空列表时抛出错误
- **示例**：`list.last([1, 2, 3])` 返回 `3`



**`at <index> <list>`** - 获取指定位置的元素
- **参数**：
  - `index` (必需): `integer` - 元素索引，支持负数（从末尾计算）
  - `list` (必需): `list` - 要访问的列表
- **返回**：`Any` - 指定位置的元素
- **示例**：
  - `list.at(1, [1, 2, 3])` 返回 `2`
  - `list.at(-1, [1, 2, 3])` 返回 `3`（最后一个元素）



### 列表切片操作

**`take <count> <list>`** - 获取前 n 个元素
- **参数**：
  - `count` (必需): `integer` - 要获取的元素数量
  - `list` (必需): `list` - 源列表
- **返回**：`list` - 包含前 n 个元素的新列表
- **示例**：`list.take(2, [1, 2, 3, 4])` 返回 `[1, 2]`



**`drop <count> <list>`** - 跳过前 n 个元素，返回剩余部分
- **参数**：
  - `count` (必需): `integer` - 要跳过的元素数量
  - `list` (必需): `list` - 源列表
- **返回**：`list` - 跳过前 n 个元素后的新列表
- **示例**：`list.drop(2, [1, 2, 3, 4])` 返回 `[3, 4]`



## 查找操作函数

**`contains <item> <list>`** - 检查列表是否包含元素
- **参数**：
  - `item` (必需): `Any` - 要查找的元素
  - `list` (必需): `list` - 源列表
- **返回**：`Boolean` - 包含元素时返回 true



**`find <item|fn> [start_index] <list>`** - 查找元素的索引
- **参数**：
  - `item` (必需): `Any|Function` - 要查找的元素或查找函数
  - `start_index` (可选): `integer` - 开始查找的索引
  - `list` (必需): `list` - 源列表
- **返回**：`integer|none` - 元素索引，未找到时返回 none
- **示例**：`list.find(3, [1, 2, 3, 4])` 返回 `2`



**`find_last <item|fn> [start_index] <list>`** - 查找元素的最后一个索引
- **参数**：
  - `item` (必需): `Any|Function` - 要查找的元素或查找函数
  - `start_index` (可选): `integer` - 开始查找的索引
  - `list` (必需): `list` - 源列表
- **返回**：`integer|none` - 元素的最后一个索引，未找到时返回 none



## 列表修改函数

### 元素添加操作

**`append <element> <list>`** - 在列表末尾添加元素
- **参数**：
  - `element` (必需): `Any` - 要添加的元素
  - `list` (必需): `list` - 目标列表
- **返回**：`list` - 添加元素后的新列表
- **示例**：`list.append(4, [1, 2, 3])` 返回 `[1, 2, 3, 4]`



**`prepend <element> <list>`** - 在列表开头添加元素
- **参数**：
  - `element` (必需): `Any` - 要添加的元素
  - `list` (必需): `list` - 目标列表
- **返回**：`list` - 添加元素后的新列表
- **示例**：`list.prepend(0, [1, 2, 3])` 返回 `[0, 1, 2, 3]`



### 列表组合操作

**`unique <list>`** - 移除重复元素，保持顺序
- **参数**：`list` (必需): `list` - 要去重的列表
- **返回**：`list` - 去重后的新列表
- **示例**：`list.unique([1, 2, 2, 3, 1])` 返回 `[1, 2, 3]`



**`split_at <index> <list>`** - 在指定位置分割列表
- **参数**：
  - `index` (必需): `integer` - 分割位置
  - `list` (必需): `list` - 要分割的列表
- **返回**：`list[list]` - 包含两个子列表的列表 [前半部分, 后半部分]
- **示例**：`list.split_at(2, [1, 2, 3, 4])` 返回 `[[1, 2], [3, 4]]`



**`sort [key_fn|key_list|keys...] <string|list>`** - 排序列表或字符串
- **参数**：
  - `key_fn` (可选): `Function|Lambda` - 排序键函数
  - `key_list` (可选): `list[String]` - 排序键列表（用于映射列表）
  - `keys` (可选): `string...` - 排序键名（用于映射列表）
  - `data` (必需): `list|String` - 要排序的数据
- **返回**：`list` - 排序后的新列表



**`group <key_fn|key> <list>`** - 按键函数或键名分组
- **参数**：
  - `key_fn` (必需): `Function|Lambda|String` - 分组键函数或键名
  - `list` (必需): `list` - 要分组的列表
- **返回**：`map` - 分组结果，键为分组标识，值为元素列表



### 元素移除和修改操作

**`remove_at <index> [count] <list>`** - 从指定索引移除 n 个元素
- **参数**：
  - `index` (必需): `integer` - 开始移除的索引
  - `count` (可选): `integer` - 要移除的元素数量，默认为1
  - `list` (必需): `list` - 源列表
- **返回**：`list` - 移除元素后的新列表



**`remove <item> [all?] <list>`** - 移除第一个匹配的元素
- **参数**：
  - `item` (必需): `Any` - 要移除的元素
  - `all` (可选): `Boolean` - 是否移除所有匹配的元素
  - `list` (必需): `list` - 源列表
- **返回**：`list` - 移除元素后的新列表



  **`set <index> <value> <list>`** - 设置指定索引处的元素
  - **参数**：
    - `index` (必需): `integer` - 要设置的索引位置
    - `value` (必需): `Any` - 新的元素值
    - `list` (必需): `list` - 源列表
  - **返回**：`list` - 设置元素后的新列表



  ### 创建操作

  **`concat <list1|item1> <list2|item2> ...`** - 连接多个列表或元素
  - **参数**：`items` (可变): `list|Any...` - 要连接的列表或元素
  - **返回**：`list` - 连接后的新列表
  - **示例**：`list.concat([1, 2], [3, 4], 5)` 返回 `[1, 2, 3, 4, 5]`



  **`from <range|item...>`** - 从范围或元素创建列表
  - **参数**：`range|item` (可变): `range|Any...` - 范围或元素列表
  - **返回**：`list` - 新创建的列表
  - **示例**：
    - `list.from(1..3)` 返回 `[1, 2, 3]`
    - `list.from(1, 2, 3)` 返回 `[1, 2, 3]`



  ## 函数式编程操作

  ### 映射和过滤

  **`map <fn> <list>`** - 对每个元素应用函数
  - **参数**：
    - `fn` (必需): `Function|Lambda` - 要应用的函数
    - `list` (必需): `list` - 源列表
  - **返回**：`list` - 应用函数后的新列表
  - **示例**：`list.map((x -> x * 2), [1, 2, 3])` 返回 `[2, 4, 6]`



  **`filter <fn> <list>`** - 按条件过滤元素
  - **参数**：
    - `fn` (必需): `Function|Lambda|Expression` - 过滤条件
    - `list` (必需): `list` - 源列表
  - **返回**：`list` - 符合条件的元素组成的新列表
  - **特殊变量**：
    - `LINENO` - 当前元素索引
    - `LINES` - 列表总长度
  - **示例**：`list.filter((x -> x > 2), [1, 2, 3, 4])` 返回 `[3, 4]`



  **`filter_map <fn> <list>`** - 过滤和映射一步完成
  - **参数**：
    - `fn` (必需): `Function|Lambda` - 转换函数，返回 none 表示过滤掉
    - `list` (必需): `list` - 源列表
  - **返回**：`list` - 转换并过滤后的新列表

  ### 聚合操作


  **`any <fn> <list>`** - 测试是否有元素通过条件
  - **参数**：
    - `fn` (必需): `Function|Lambda` - 测试函数
    - `list` (必需): `list` - 源列表
  - **返回**：`Boolean` - 有元素通过测试时返回 true



  **`all <fn> <list>`** - 测试是否所有元素都通过条件
  - **参数**：
    - `fn` (必需): `Function|Lambda` - 测试函数
    - `list` (必需): `list` - 源列表
  - **返回**：`Boolean` - 所有元素都通过测试时返回 true


  ## 转换操作

  **`join <separator> <list>`** - 将字符串列表连接为单个字符串
  - **参数**：
    - `separator` (必需): `String` - 分隔符
    - `list` (必需): `list[String]` - 字符串列表
  - **返回**：`String` - 连接后的字符串
  - **示例**：`list.join(", ", ["a", "b", "c"])` 返回 `"a, b, c"`



  **`to_map [key_fn] [val_fn] <list>`** - 将列表转换为映射
  - **参数**：
    - `key_fn` (可选): `Function|Lambda` - 键提取函数
    - `val_fn` (可选): `Function|Lambda` - 值提取函数
    - `list` (必需): `list` - 源列表
  - **返回**：`map` - 转换后的映射
  - **示例**：
    - `list.to_map((x -> x.id), (y -> y.name), [{id: 1,name: "wang"}, {id: 2,name: "fang"}])` 返回 `{1: "wang", 2: "fang"}`
    - `list.to_map((x -> x.id), [{id: 1}, {id: 2}])` 返回 `{1: {id: 1}, 2: {id: 2}}`
    - `list.to_map(["Q1", 45, "Q2", 52, "Q3", 49, "Q4", 87])` 返回 `{"Q1":45, "Q2":52, "Q3":49, "Q4": 87}`

** `to_set <list>`** - 将列表转换为集合
  - **参数**：
    - `list` (必需): `list` - 源列表
  - **返回**：`set` - 集合

  **`items <list>`** - 获取索引-值对列表
  - **参数**：`list` (必需): `list` - 源列表
  - **返回**：`list[list]` - 索引-值对列表
  - **示例**：`list.items(["a", "b"])` 返回 `[[0, "a"], [1, "b"]]`



  ## 结构操作函数

  ### 矩阵操作

  **`transpose <matrix>`** - 转置矩阵（列表的列表）
  - **参数**：`matrix` (必需): `list[list]` - 要转置的矩阵
  - **返回**：`list[list]` - 转置后的矩阵
  - **示例**：`list.transpose([[1, 2], [3, 4]])` 返回 `[[1, 3], [2, 4]]`



  **`chunk <size> <list>`** - 将列表分割为指定大小的块
  - **参数**：
    - `size` (必需): `integer` - 块大小
    - `list` (必需): `list` - 源列表
  - **返回**：`list[list]` - 分块后的列表
  - **示例**：`list.chunk(2, [1, 2, 3, 4, 5])` 返回 `[[1, 2], [3, 4], [5]]`



  ### 配对操作

  **`zip <list1> <list2>`** - 将两个列表配对
  - **参数**：
    - `list1` (必需): `list` - 第一个列表
    - `list2` (必需): `list` - 第二个列表
  - **返回**：`list[list]` - 配对后的列表
  - **示例**：`list.zip([1, 2, 3], ["a", "b", "c"])` 返回 `[[1, "a"], [2, "b"], [3, "c"]]`



  **`unzip <list_of_pairs>`** - 解压成对列表
  - **参数**：`list_of_pairs` (必需): `list[list]` - 成对列表
  - **返回**：`list[list]` - 包含两个列表的列表
  - **示例**：`list.unzip([[1, "a"], [2, "b"]])` 返回 `[[1, 2], ["a", "b"]]`



  ### 折叠操作

  **`foldl <fn> <init> <list>`** - 从左到右折叠列表
  - **参数**：
    - `fn` (必需): `Function|Lambda` - 折叠函数，接受 (累加值, 当前元素)
    - `init` (必需): `Any` - 初始累加值
    - `list` (必需): `list` - 源列表
  - **返回**：`Any` - 折叠结果
  - **示例**：`list.foldl((acc, x) -> acc + x, 0, [1, 2, 3])` 返回 `6`



  **`foldr <fn> <init> <list>`** - 从右到左折叠列表
  - **参数**：
    - `fn` (必需): `Function|Lambda` - 折叠函数，接受 (当前元素, 累加值)
    - `init` (必需): `Any` - 初始累加值
    - `list` (必需): `list` - 源列表
  - **返回**：`Any` - 折叠结果
  - **示例**：`list.foldr((x, acc) -> acc + x, 0, [1, 2, 3])` 返回 `6`



  ## 使用示例

  ### 函数式编程示例
  ```bash
  # 数据处理管道
  [1, 2, 3, 4, 5] | list.filter((x -> x > 2)) | list.map((x -> x * 2))
  # 结果: [6, 8, 10]

  # 统计操作
  list.sum([1, 2, 3, 4, 5])  # 返回 15
  list.average([1, 2, 3, 4, 5])  # 返回 3

  # 分组和聚合
  users | list.group("department") | map.map((dept, users) -> list.len(users))
  ```

  ### 结构操作示例
  ```bash
  # 矩阵转置
  list.transpose([[1, 2, 3], [4, 5, 6]])  # 返回 [[1, 4], [2, 5], [3, 6]]

  # 列表分块
  list.chunk(3, [1, 2, 3, 4, 5, 6, 7])  # 返回 [[1, 2, 3], [4, 5, 6], [7]]

  # 列表配对
  list.zip([1, 2, 3], ["a", "b", "c"])  # 返回 [[1, "a"], [2, "b"], [3, "c"]]
  ```

  ## Notes

  list 模块提供了全面的列表处理能力，支持函数式编程范式和不可变数据操作。所有函数都经过优化，提供一致的错误处理和类型安全。参数类型说明中，`<>` 表示必需参数，`[]` 表示可选参数，`...` 表示可变参数。特别注意在使用高阶函数时，lambda 表达式需要用括号包围以避免解析歧义。

  实际使用时，支持链式调用，如：
  ```bash
  [1,2,3].max()
  ```
  在示例中，为方便理解，仅使用了类型名称调用。
