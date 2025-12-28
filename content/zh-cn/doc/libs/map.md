---
title: Lumesh Map 模块
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - map
 - dictionary
categories:
 - wiki
 - libs
---

Map 模块提供了全面的键值对数据结构操作函数，支持映射的创建、查询、修改、合并和转换等功能。所有函数都采用不可变设计，返回新的映射而不修改原映射。

## 功能概览

| 功能类别 | 主要函数 | 用途 |
|---------|---------|------|
| **打印输出** | `pprint` | 美化打印映射 |
| **基础操作** | `len`, `insert`, `flatten` | 基础数据操作 |
| **检查操作** | `has` | 检查键是否存在 |
| **数据获取** | `get`, `at`, `items`, `keys`, `values` | 获取映射数据 |
| **查找操作** | `find`, `filter` | 条件查找和过滤 |
| **修改操作** | `remove`, `set` | 映射内容修改 |
| **创建操作** | `from_items` | 从数据创建映射 |
| **集合运算** | `union`, `intersect`, `difference`, `merge` | 映射间运算 |
| **转换操作** | `map` | 映射转换 |

## 打印输出函数

**`pprint <map>`** - 美化打印映射
- **参数**：`map` (必需): `Map` - 要打印的映射
- **用途**：以表格形式美化显示映射数据

## 基础操作函数

**`len <map>`** - 获取映射长度
- **参数**：`map` (必需): `Map` - 要计算长度的映射
- **返回**：`Integer` - 映射中键值对的数量

**`insert <key> <value> <map>`** - 向映射中插入键值对
- **参数**：
  - `key` (必需): `String` - 键名
  - `value` (必需): `Any` - 键值
  - `map` (必需): `Map` - 目标映射
- **返回**：`Map` - 插入键值对后的新映射

**`flatten <map>`** - 展平嵌套映射结构
- **参数**：`map` (必需): `Map` - 要展平的嵌套映射
- **返回**：`Map` - 展平后的映射

## 检查操作函数

**`has <key> <map>`** - 检查映射是否包含指定键
- **参数**：
  - `key` (必需): `String` - 要检查的键名
  - `map` (必需): `Map` - 源映射
- **返回**：`Boolean` - 包含键时返回 true

## 数据获取函数

**`get <path> <map|list|range>`** - 使用点号路径从嵌套结构中获取值
- **参数**：
  - `path` (必需): `String` - 访问路径，支持点号分隔
  - `data` (必需): `Map|List|Range` - 要访问的数据结构
- **返回**：`Any` - 指定路径的值

**`at <key> <map>`** - 从映射中获取指定键的值
- **参数**：
  - `key` (必需): `String` - 键名
  - `map` (必需): `Map` - 源映射
- **返回**：`Any` - 键对应的值
- **错误**：键不存在时抛出错误

**`items <map>`** - 获取映射的键值对列表
- **参数**：`map` (必需): `Map` - 源映射
- **返回**：`List[List]` - 键值对列表，每个元素为 [key, value]
- **示例**：`Map.items({"a": 1, "b": 2})` 返回 `[["a", 1], ["b", 2]]`

**`keys <map>`** - 获取映射的所有键
- **参数**：`map` (必需): `Map` - 源映射
- **返回**：`List[String]` - 键名列表

**`values <map>`** - 获取映射的所有值
- **参数**：`map` (必需): `Map` - 源映射
- **返回**：`List[Any]` - 值列表

## 查找操作函数

**`find <predicate_fn> <map>`** - 查找第一个匹配条件的键值对
- **参数**：
  - `predicate_fn` (必需): `Function|Lambda` - 判断函数，接受 (key, value) 参数
  - `map` (必需): `Map` - 源映射
- **返回**：`List|None` - 匹配的键值对 [key, value]，未找到时返回 None

**`filter <predicate_fn> <map>`** - 按条件过滤映射
- **参数**：
  - `predicate_fn` (必需): `Function|Lambda` - 过滤函数，接受 (key, value) 参数
  - `map` (必需): `Map` - 源映射
- **返回**：`Map` - 符合条件的键值对组成的新映射

## 修改操作函数

**`remove <key> <map>`** - 从映射中移除指定键
- **参数**：
  - `key` (必需): `String` - 要移除的键名
  - `map` (必需): `Map` - 源映射
- **返回**：`Map` - 移除键后的新映射

**`set <key> <value> <map>`** - 设置映射中现有键的值
- **参数**：
  - `key` (必需): `String` - 键名（必须已存在）
  - `value` (必需): `Any` - 新值
  - `map` (必需): `Map` - 源映射
- **返回**：`Map` - 设置值后的新映射
- **错误**：键不存在时抛出错误

## 创建操作函数

**`from_items <items>`** - 从键值对列表创建映射
- **参数**：`items` (必需): `List[List]` - 键值对列表，每个元素为 [key, value]
- **返回**：`Map` - 创建的映射
- **示例**：`Map.from_items([["a", 1], ["b", 2]])` 返回 `{"a": 1, "b": 2}`

## 集合运算函数

**`union <map1> <map2>`** - 合并两个映射
- **参数**：
  - `map1` (必需): `Map` - 第一个映射
  - `map2` (必需): `Map` - 第二个映射
- **返回**：`Map` - 合并后的映射（map2 的值会覆盖 map1 中相同键的值）

**`intersect <map1> <map2>`** - 获取两个映射的交集
- **参数**：
  - `map1` (必需): `Map` - 第一个映射
  - `map2` (必需): `Map` - 第二个映射
- **返回**：`Map` - 包含两个映射共同键的新映射

**`difference <map1> <map2>`** - 获取两个映射的差集
- **参数**：
  - `map1` (必需): `Map` - 第一个映射
  - `map2` (必需): `Map` - 第二个映射
- **返回**：`Map` - 包含 map1 中但不在 map2 中的键的新映射

**`merge <map1> <map2> [<map3> ...]`** - 递归合并多个映射
- **参数**：
  - `map1` (必需): `Map` - 基础映射
  - `map2` (必需): `Map` - 要合并的映射
  - `map3...` (可选): `Map...` - 更多要合并的映射
- **返回**：`Map` - 深度合并后的映射
- **用途**：对于嵌套映射，会递归合并而不是简单覆盖

## 转换操作函数

**`map <key_fn> <val_fn> <map>`** - 转换映射的键和值
- **参数**：
  - `key_fn` (必需): `Function|Lambda` - 键转换函数
  - `val_fn` (可选): `Function|Lambda` - 值转换函数，默认为恒等函数
  - `map` (必需): `Map` - 源映射
- **返回**：`Map` - 转换后的新映射

## 使用示例

### 基础操作示例
```bash
# 创建和操作映射
let user = {"name": "Alice", "age": 30}
Map.has("name", user)  # 返回 true
Map.at("name", user)   # 返回 "Alice"
Map.keys(user)         # 返回 ["name", "age"]
```

### 集合运算示例
```bash
# 映射合并
let map1 = {"a": 1, "b": 2}
let map2 = {"b": 3, "c": 4}
Map.union(map1, map2)     # 返回 {"a": 1, "b": 3, "c": 4}
Map.intersect(map1, map2) # 返回 {"b": 2}
Map.difference(map1, map2) # 返回 {"a": 1}
```

### 函数式操作示例
```bash
# 过滤和转换
users | Map.filter((k, v) -> v.age > 18) | Map.map((k, v) -> k.toUpper(), (k, v) -> v.name)
```

## Notes

Map 模块提供了全面的键值对数据结构处理能力，支持函数式编程范式和不可变数据操作。所有函数都经过优化，提供一致的错误处理和类型安全。参数类型说明中，`<>` 表示必需参数，`[]` 表示可选参数，`...` 表示可变参数。

特别注意 `set` 函数只能修改已存在的键，而 `insert` 函数可以添加新键。

实际使用时，支持链式调用，如：
```bash
let map1 = {"a": 1, "b": 2}
map1.get('a')
```
在示例中，为方便理解，仅使用了类型名称调用。
