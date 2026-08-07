---
title: Builtin Lib LIST
date: 2026-08-07 16:40:11
---
	
## Builtin Functions for Lib: list

- `all <list> <fn>`
	all elements pass?

- `any <list> <fn>`
	any element passes?

- `average <num1> <num2>... | <array>`
	average of numbers

- `chunks <list> <size>`
	split into chunks of size n

- `concat <list1|item1> <list2|item2>...`
	concat lists/items into one list

- `contains <list> <item>`
	contains item?

- `dig <map|list|range> <path>`
	get nested value by dot path. e.g. dig m 'a.b.0'

- `fill <value> <n>`
	repeat value n times

- `filter <list> <fn>`
	filter by fn([index],item)

- `filter_map <list> <fn>`
	filter+map, drop None results

- `find <list> <item|fn> [skip_n=0]`
	first matched item

- `first <list> [n=1]`
	first n elements

- `flatten <collection>`
	flatten nested structure

- `fold <list> <fn> [init=0]`
	fold left, fn(acc,item)

- `from <range>`
	list from range

- `get <list> <index>`
	nth element, negative index from end

- `group <list> <key_fn|key>`
	group by key fn or map field, e.g.  fn(item)->string

- `insert <list> <index> <value>`
	insert value at index

- `is_empty <list>`
	is empty?

- `items <list>`
	index-value pairs

- `join <list> <separator>`
	join strings with separator

- `last <list> [n=1]`
	last n elements

- `len <list>`
	list length

- `map <list> <fn>`
	apply fn([index],item) per element

- `max <num1> <num2>... | <array>`
	max value

- `min <num1> <num2>... | <array>`
	min value

- `position <list> <item|fn> [skip_n=0]`
	first matched index

- `push <list> <element>`
	append element

- `remove <list> <item> [all=false]`
	remove item, default first-only

- `remove_at <list> <index> [count=1]`
	remove n items from index

- `rev <list>`
	reverse

- `rfind <list> <item|fn> [skip_n=0]`
	last matched item

- `rfold <list> <fn> [init=0]`
	fold right, fn(acc,item)

- `rotate <list> <n>`
	rotate, n>0 right, n<0 left

- `rposition <list> <item|fn> [skip_n=0]`
	last matched index

- `sample <list> <n>`
	pick n distinct random elements

- `set <list> <index> <value>`
	set value at existing index

- `shuffle <list>`
	shuffle order

- `skip <list> <count>`
	skip first n elements

- `slice <list> <start> <end>`
	sub-list [start,end), negative index ok

- `sort <list> [key_fn|±key...]`
	sort, optional fn(a,b)->[-1/0/1]. e.g. sort list 'name'

- `splice <list> <start> <delete_count> [items...]`
	delete & optionally insert at index, returns new list

- `split_at <list> <index>`
	split at index, returns [left,right]

- `split_first <list>`
	split head/tail, returns [head,rest]

- `sum <num1> <num2>... | <array>`
	sum of numbers

- `swap <list> <i> <j>`
	swap two elements by index

- `take <list> <count>`
	first n elements

- `to_hmap <list> [key_fn] [val_fn]`
	to hashMap, default pairs [k,v,k,v...]

- `to_map <list> [key_fn] [val_fn]`
	to btreeMap, default pairs [k,v,k,v...]

- `to_set <list>`
	to btreeSet

- `transpose <matrix>`
	transpose matrix (list of lists)

- `unique <list>`
	dedupe, preserve order

- `unzip <list_of_pairs>`
	unzip pairs into two lists

- `windows <list> <size>`
	overlapping sliding windows of size n

- `zip <list1> <list2>`
	zip two lists into pairs

