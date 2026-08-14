---
title: Builtin Lib SYS
date: 2026-08-14 11:26:54
---
	
## Builtin Functions for Lib: sys

- `defined <var>`
	defined in scope chain?

- `dirs `
	system directories map

- `env [var]`
	root env map, or var value

- `error_codes `
	list lume error codes

- `has <var>`
	defined in current scope?

- `info `
	os info

- `max_runtime [depth]`
	get/set max runtime recursion depth

- `max_syntax [depth]`
	get/set max syntax recursion depth

- `max_usemode [depth]`
	get/set max use-mode recursion depth

- `modes `
	current mode flags {cfm,strict,pdm}

- `set_cfm <boolean|none>`
	set Cmd First Mode

- `set_pdm <boolean>`
	enable/disable print direct mode

- `set_strict <boolean>`
	enable/disable strict mode

- `vars `
	vars defined in current scope

