---
title: 内置库 map
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: map

- `contains_key <map> <key>`
	包含键？

- `contains_value <map> <value>`
	包含值？

- `difference <map1> <map2>`
	map1 中存在但在 map2 中不存在的键

- `dig <map|list|range> <path>`
	通过点路径获取嵌套值。例如 dig m 'a.b.0'

- `filter <map> <fn>`
	保留满足 fn(k,v)->bool 的键值对

- `find <map> <fn>`
	第一个满足 fn(k,v)->bool 的键值对，返回 [k,v]

- `first <map>`
	按键顺序的第一个键值对，返回 [k,v]

- `flatten <map>`
	扁平化嵌套结构

- `from_list <list>`
	从 [k,v] 对列表创建映射

- `get <map> <key>`
	根据键获取值

- `insert <map> <key> <value>`
	插入键值对，返回新映射

- `intersection <map1> <map2>`
	两个映射中都存在的键，值来自 map1

- `is_empty <map>`
	是否为空？

- `keys <map>`
	键列表

- `last <map>`
	按键顺序的最后一个键值对，返回 [k,v]

- `len <map>`
	映射大小

- `map <map> <fn(k,v)>`
	转换键/值，fn(k,v)->[k,v]

- `merge <map1> <map2> [<map3>...]`
	深度合并映射，递归处理嵌套映射

- `remove <map> <key>`
	删除键，返回新映射

- `set <map> <key> <value>`
	设置现有键的值，返回新映射

- `to_hmap <map>`
	转换为 hashMap（无序）

- `to_list <map>`
	转换为 [k,v] 对列表

- `union <map1> <map2>`
	合并映射，冲突时 map2 优先

- `values <map>`
	值列表
