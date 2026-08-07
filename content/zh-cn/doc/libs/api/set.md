---
title: 内置库set
date: 2026-08-05 21:50:50
---
	
## Builtin Functions for Lib: set

- all <set> <fn>
	all items pass fn(item)->bool?

- any <set> <fn>
	any item passes fn(item)->bool?

- contains <set> <item>
	contains item?

- difference <set1> <set2>
	items in set1 not in set2

- filter <set> <fn>
	keep items where fn(item)->bool

- find <set> <fn>
	first item matching fn(item)->bool

- first <set>
	smallest item

- from_list <list>
	create set from list

- get <set> <index>
	nth element, negative index from end

- insert <set> <item>
	add item, returns new set

- intersection <set1> <set2>
	intersection

- is_disjoint <set1> <set2>
	no common items?

- is_empty <set>
	is empty?

- is_subset <set1> <set2>
	set1 ⊆ set2?

- is_superset <set1> <set2>
	set1 ⊇ set2?

- last <set>
	largest item

- len <set>
	set size

- map <set> <fn>
	apply fn(item)->new_item to each

- remove <set> <item>
	remove item, returns new set

- split_first <set>
	pop smallest, returns [item,rest]

- split_last <set>
	pop largest, returns [item,rest]

- symmetric_difference <set1> <set2>
	items in either but not both

- to_list <set>
	to list, sorted order

- union <set1> <set2>
	union

