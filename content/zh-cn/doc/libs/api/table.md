---
title: 内置库 table
date: 2026-08-14 11:29:46
---

## Builtin Functions for Lib: table

- `filter <table> <cell|fn>`
	按单元格/fn(row_map)->bool 过滤行

- `first <table> [n=1]`
	前 n 行作为列表

- `first_map <table> [n=1]`
	前 n 行作为映射

- `from_maps <list_of_maps>`
	从映射构建表，表头 = 键的并集

- `get <table> <index>`
	第 n 行作为列表

- `get_cell <table> <row_index> <header|index>`
	单个单元格，负行索引支持

- `get_column <table> <header|index>`
	按表头/索引获取列

- `get_map <table> <index>`
	第 n 行作为映射

- `grep <table> <string>`
	包含字符串的行

- `header_len <table>`
	列数

- `headers <table>`
	表头列表

- `is_empty <table>`
	没有行？

- `last <table> [n=1]`
	后 n 行作为列表

- `last_map <table> [n=1]`
	后 n 行作为映射

- `len <table>`
	行数

- `position <table> <cell|fn> [start=0]`
	第一个匹配单元格/fn(row_map)->bool 的行索引

- `push <table> <list|set>`
	追加一行

- `rows <table>`
	行作为列表

- `rows_map <table>`
	行作为映射

- `rposition <table> <cell|fn> [start=0]`
	最后一个匹配单元格/fn(row_map)->bool 的行索引

- `select <table> <cols...>`
	选择列

- `slice <table> <start> <end>`
	行范围作为映射 [start,end)，支持负索引

- `sort <list> [key_fn|±key...]`
	排序，可选 fn(a,b)->[-1/0/1]。例如 sort table 'name'

- `to_csv <table>`
	序列化为 CSV
