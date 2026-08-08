---
title: 内置库 regex
date: 2026-08-07 16:42:00
---
	
## Builtin Functions for Lib: regex

- `capture <pattern> <text>`
	first match's groups [full,g1,g2,...]

- `captures <pattern> <text>`
	all matches' groups [[full,g1,...],...]

- `find <pattern> <text>`
	first match, returns {start,end,found}

- `find_all <pattern> <text>`
	all matches, list of {start,end,found}

- `from <pattern_string> [flag]`
  build regex from pattern and flag

- `is_match <pattern> <text>`
	contains a match?

- `named_captures <pattern> <text>`
	named groups as map. e.g. g'(?<y>\d+)'

- `replace <text> <pattern> <replacement>`
	replace first match

- `replace_all <text> <pattern> <replacement>`
	replace all matches

- `split <pattern> <text>`
	split by pattern
