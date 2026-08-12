---
title: Top Level Builtin Lib
date: 2026-08-07 16:40:11
---
	
## Top level Builtin Functions


- `assert <expr> [expr] [message]`
	throw if not equal/truthy

- `cd [path=~]`
	change dir. '-' for previous

- `cwd `
	current dir

- `ddebug <args>...`
	eval & show expr,type,value(pretty fmt)

- `debug <args>...`
	eval & show expr,type,value(debug fmt)

- `dig <map|list|set|range|table> <path>`
	get nested value by dot path. e.g. dig m 'a.b.0'

- `eprint <args>...`
	print to stderr, red, no newline

- `eprintln <args>...`
	print to stderr, red, with newline

- `eval <expr>`
	eval expr in current env

- `eval_str <string>`
	parse & eval string in current env

- `exec <expr>`
	eval expr in forked env

- `exec_str <string>`
	parse & eval string in forked env

- `exit [status=0]`
	exit shell

- `flatten <collection>`
	flatten nested list/map to flat list

- `format <template> <args>...`
	fmt string. {name}/{} for named/positional, :spec for align.
	e.g. format '{:0>5}' 3 -> 00003

- `get_env <var>`
	get var from env

- `get_local <var>`
	get local var value

- `get_var <var>`
	get var, local first then env

- `help [libs|tops|doc|<lib>|<lib>.<func>|<top_func>]`
	show help. e.g. help string.split

- `import <path>`
	eval file in forked env

- `include <path>`
	eval file in current env

- `jobs [-k id]`
	list/kill jobs

- `len <list|set|map|table|range|string|bytes>`
	size of collection

- `not <boolean>...`
	logic not

- `pprint <value>...`
	pretty print table/list/map

- `print <args>...`
	print, space-sep, no newline

- `println <args>...`
	print, space-sep, with newline

- `quote <expr>`
	quote expr, eval later

- `read [-p prompt] [-n max_chars] [-s silent] [-t timeout_secs]`
	read input

- `repeat <expr> <n>`
	eval expr n times, collect non-None results

- `rev <string|list|table|bytes>`
	reverse

- `select <table> <columns...>`
	select columns from table

- `set_root <var> <val>`
	define var in root env

- `symof <value>`
	type name before eval

- `shift`
  shift to next in iter

- `tap <args>...`
	print then return value(s)

- `throw <msg>`
	raise a runtime error

- `typeof <value>`
	type name after eval

- `unset_root <var>`
	undefine var in root env

- `when <condition> <execute>`
	conditional execute

- `where <table> <condition>`
	filter table rows. `NR/<col_name>` injected. e.g. where t (NR>1 and col>0)
