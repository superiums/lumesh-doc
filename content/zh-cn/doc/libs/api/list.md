---
title: 内置库 list
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: list

- `all <list> <fn>`
	所有元素都通过检查？

- `any <list> <fn>`
	有元素通过检查？

- `average <num1> <num2>... | <array>`
	数字平均值

- `chunks <list> <size>`
	拆分为大小为 n 的块

- `concat <list1|item1> <list2|item2>...`
	连接列表/项为单个列表

- `contains <list> <item>`
	包含项？

- `dig <map|list|range> <path>`
	通过点路径获取嵌套值。例如 dig m 'a.b.0'

- `fill <value> <n>`
	重复值 n 次

- `filter <list> <fn>`
	按 fn([index],item) 过滤

- `filter_map <list> <fn>`
	过滤+映射，丢弃 None 结果

- `find <list> <item|fn> [skip_n=0]`
	第一个匹配的项

- `first <list> [n=1]`
	前 n 个元素

- `flatten <collection>`
	扁平化嵌套结构

- `fold <list> <fn> [init=0]`
	左折叠，fn(acc,item)

- `from <range>`
	从范围生成列表

- `get <list> <index>`
	第 n 个元素，负索引从末尾开始

- `group <list> <fn|key>`
	按键函数或映射字段分组，例如 fn(item)->string

- `insert <list> <index> <value>`
	在指定索引插入值

- `is_empty <list>`
	是否为空？

- `items <list>`
	索引-值对

- `join <list> [sep=' ']`
	用分隔符连接字符串

- `last <list> [n=1]`
	后 n 个元素

- `len <list>`
	列表长度

- `map <list> <fn>`
	对每个元素应用 fn([index],item)

- `max <num1> <num2>... | <array>`
	最大值

- `min <num1> <num2>... | <array>`
	最小值

- `position <list> <item|fn> [skip_n=0]`
	第一个匹配的索引

- `push <list> <element>`
	追加元素

- `remove <list> <item> [all=false]`
	移除项，默认只移除第一个

- `remove_at <list> <index> [count=1]`
	从索引处移除 n 个项

- `rev <list>`
	反转

- `rfind <list> <item|fn> [skip_n=0]`
	最后一个匹配的项

- `rfold <list> <fn> [init=0]`
	右折叠，fn(acc,item)

- `rotate <list> <n>`
	旋转，n>0 右旋，n<0 左旋

- `rposition <list> <item|fn> [skip_n=0]`
	最后一个匹配的索引

- `sample <list> <n>`
	选取 n 个不同的随机元素

- `set <list> <index> <value>`
	设置现有索引的值

- `shuffle <list>`
	随机打乱顺序

- `skip <list> <count>`
	跳过前 n 个元素

- `slice <list> <start> <end>`
	子列表 [start,end)，支持负索引

- `sort <list> [fn|±key...]`
	排序，可选 fn(a,b)->[-1/0/1]。例如 sort list 'name'

- `splice <list> <start> <delete_count> [items...]`
	在索引处删除并可选插入，返回新列表

- `split_at <list> <index>`
	在索引处分割，返回 [left,right]

- `split_first <list>`
	分割头部/尾部，返回 [head,rest]

- `sum <num1> <num2>... | <array>`
	数字求和

- `swap <list> <i> <j>`
	交换两个元素

- `take <list> <count>`
	前 n 个元素

- `to_hmap <list> [fn(k,v)]`
	转换为 hashMap，键值对 [k,v,k,v...]

- `to_map <list> [fn(k,v)]`
	转换为 btreeMap，键值对 [k,v,k,v...]

- `to_set <list>`
	转换为 btreeSet

- `transpose <matrix>`
	转置矩阵（列表的列表）

- `unique <list>`
	去重，保持顺序

- `unzip <list_of_pairs>`
	解压对为两个列表

- `windows <list> <size>`
	大小为 n 的重叠滑动窗口

- `zip <list1> <list2>`
	将两个列表压缩为对
