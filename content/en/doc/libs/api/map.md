---
title: Builtin Lib MAP
date: 2026-08-07 12:34:52
---
	
## Builtin Functions for Lib: map

- contains_key <map> <key>
	has key?

- contains_value <map> <value>
	has value?

- difference <map1> <map2>
	keys in map1 not in map2

- dig <map|list|range> <path>
	get nested value by dot path. e.g. dig m 'a.b.0'

- filter <map> <fn>
	keep pairs where fn(k,v)->bool

- find <map> <fn>
	first pair matching fn(k,v)->bool, returns [k,v]

- first <map>
	first key-value pair by key order, returns [k,v]

- flatten <map>
	flatten nested structure

- from_list <list>
	create map from list of [k,v] pairs

- get <map> <key>
	value by key

- insert <map> <key> <value>
	insert key-value, returns new map

- intersection <map1> <map2>
	keys in both, values from map1

- is_empty <map>
	is empty?

- keys <map>
	list of keys

- last <map>
	last key-value pair by key order, returns [k,v]

- len <map>
	map size

- map <map> <map_fn>
	transform keys/values, fn(k,v)->[k,v]

- merge <map1> <map2> [<map3>...]
	deep merge maps, recurse on nested maps

- remove <map> <key>
	remove key, returns new map

- set <map> <key> <value>
	set existing key's value, returns new map

- to_hmap <map>
	to hashMap (unordered)

- to_list <map>
	to list of [k,v] pairs

- union <map1> <map2>
	combine maps, map2 wins on conflict

- values <map>
	list of values

