---
title: Builtin Lib TABLE
date: 2026-08-14 11:26:54
---
	
## Builtin Functions for Lib: table

- `filter <table> <cell|fn>`
	filter rows by cell/fn(row_map)->bool

- `first <table> [n=1]`
	first n rows as lists

- `first_map <table> [n=1]`
	first n rows as maps

- `from_maps <list_of_maps>`
	build table from maps, headers = union of keys

- `get <table> <index>`
	nth row as list

- `get_cell <table> <row_index> <header|index>`
	single cell, negative row index ok

- `get_column <table> <header|index>`
	column by header/index

- `get_map <table> <index>`
	nth row as map

- `grep <table> <string>`
	rows containing string

- `header_len <table>`
	column count

- `headers <table>`
	list headers

- `is_empty <table>`
	has no rows?

- `last <table> [n=1]`
	last n rows as lists

- `last_map <table> [n=1]`
	last n rows as maps

- `len <table>`
	row count

- `position <table> <cell|fn> [start=0]`
	first row index matching cell/fn(row_map)->bool

- `push <table> <list|set>`
	append a row

- `rows <table>`
	rows as lists

- `rows_map <table>`
	rows as maps

- `rposition <table> <cell|fn> [start=0]`
	last row index matching cell/fn(row_map)->bool

- `select <table> <cols...>`
	select columns

- `slice <table> <start> <end>`
	row range as maps [start,end), negative index ok

- `sort <list> [key_fn|±key...]`
	sort, optional fn(a,b)->[-1/0/1]. e.g. sort table 'name'

- `to_csv <table>`
	serialize to CSV

