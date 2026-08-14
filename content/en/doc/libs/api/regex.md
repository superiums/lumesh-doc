---
title: Builtin Lib REGEX
date: 2026-08-14 11:26:54
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

- `from <pattern_string> [i|m|s|x|R|U]`
	build regex from string pattern

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

