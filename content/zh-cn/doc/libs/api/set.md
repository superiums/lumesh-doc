---
title: 内置库 set
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: set

- `all <set> <fn>`
	所有项都通过 fn(item)->bool 检查？

- `any <set> <fn>`
	有项通过 fn(item)->bool 检查？

- `contains <set> <item>`
	包含项？

- `difference <set1> <set2>`
	set1 中存在但在 set2 中不存在的项

- `filter <set> <fn>`
	保留满足 fn(item)->bool 的项

- `find <set> <fn>`
	第一个满足 fn(item)->bool 的项

- `first <set>`
	最小的项

- `from_list <list>`
	从列表创建集合

- `get <set> <index>`
	第 n 个元素，负索引从末尾开始

- `insert <set> <item>`
	添加项，返回新集合

- `intersection <set1> <set2>`
	交集

- `is_disjoint <set1> <set2>`
	没有共同项？

- `is_empty <set>`
	是否为空？

- `is_subset <set1> <set2>`
	set1 ⊆ set2？

- `is_superset <set1> <set2>`
	set1 ⊇ set2？

- `last <set>`
	最大的项

- `len <set>`
	集合大小

- `map <set> <fn>`
	对每个项应用 fn(item)->new_item

- `remove <set> <item>`
	移除项，返回新集合

- `split_first <set>`
	弹出最小的，返回 [item,rest]

- `split_last <set>`
	弹出最大的，返回 [item,rest]

- `symmetric_difference <set1> <set2>`
	在 set1 或 set2 中但不在两者中的项

- `to_list <set>`
	转换为列表，排序顺序
