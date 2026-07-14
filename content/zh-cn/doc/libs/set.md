---
title: Lumesh Set 库
date: 2025-07-13 19:16:45
highlight: true
tags:
 - libs
 - list
 - set
categories:
 - wiki
 - libs
---

Set集合采用BtreeSet，会自动排序。

Set库的功能和List类似。

- set.add <set> <item>
	add item to set

- set.contains <set> <item>
	check if set contains item

- set.difference <set1> <set2>
	difference of two sets

- set.filter <set> <predicate_fn>
	filter set by condition

- set.find <set> <predicate_fn>
	find first item matching condition

- set.first <set>
	get first item of set

- set.from_items <items>
	create set from list

- set.intersect <set1> <set2>
	intersection of two sets

- set.is_empty <set>
	check if set is empty

- set.is_subset <set1> <set2>
	check if set1 is subset of set2

- set.is_superset <set1> <set2>
	check if set1 is superset of set2

- set.items <set>
	get all items from set

- set.last <set>
	get last item of set

- set.len <set>
	get size of set

- set.map <set> <fn>
	apply function to each item

- set.remove <set> <item>
	remove item from set

- set.to_list <set>
	convert set to list

- set.union <set1> <set2>
	union of two sets
