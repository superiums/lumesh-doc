---
title: 内置库regex
date: 2026-08-05 21:50:50
---
	
## Builtin Functions for Lib: regex

- capture <pattern> <text>
	first match's groups [full,g1,g2,...]

- captures <pattern> <text>
	all matches' groups [[full,g1,...],...]

- find <pattern> <text>
	first match, returns {start,end,found}

- find_all <pattern> <text>
	all matches, list of {start,end,found}

- is_match <pattern> <text>
	contains a match?

- named_captures <pattern> <text>
	named groups as map. e.g. g'(?<y>\d+)'

- replace <text> <pattern> <replacement>
	replace first match

- replace_all <text> <pattern> <replacement>
	replace all matches

- split <pattern> <text>
	split by pattern

