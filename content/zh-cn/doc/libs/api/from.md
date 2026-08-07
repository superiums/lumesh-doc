---
title: 内置库from
date: 2026-08-05 21:50:50
---
	
## Builtin Functions for Lib: from

- cmd <output> [split_regex] [headers...]
	parse cmd output into table

- csv <csv_string>
	parse CSV string, headers row required

- jq <json_string> <query_string>
	jq-like query on json string. e.g. '.a|.[]|select(.n>1)'

- json <json_string>
	parse JSON string

- script <script_string>
	parse script text to expression (unevaluated)

- toml <toml_string>
	parse TOML string

