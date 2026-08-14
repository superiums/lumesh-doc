---
title: Builtin Lib RAND
date: 2026-08-14 11:26:54
---
	
## Builtin Functions for Lib: rand

- `alpha [len=1]`
	random alphabetic char(s)

- `alphanum [len=1]`
	random alphanumeric char(s)

- `chance [p=0.5]`
	random bool with probability p

- `choose <list>`
	pick random item

- `float [min] [max]`
	random float. no args: [0,1); 2 args: [min,max]

- `int [min] [max]`
	random integer. no args: any i64; 1 arg: [0,max]; 2 args: [min,max]

- `ratio <num> <den>`
	random bool with probability num/den

- `sample <list> <n>`
	pick n distinct items, no replacement

- `seed <integer>`
	seed generator for reproducible sequence

- `shuffle <list>`
	shuffle order, returns new list

