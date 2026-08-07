---
title: Builtin Lib BYTES
date: 2026-08-07 16:40:11
---
	
## Builtin Functions for Lib: bytes

- `concat <bytes> <bytes>`
	concat two bytes

- `contains <bytes> <bytes>`
	contains sub-sequence?

- `ends_with <bytes> <bytes>`
	ends with suffix?

- `from <string>`
	bytes from utf8 string

- `from_base64 <base64_string>`
	bytes from base64 string

- `from_escaped <string>`
	bytes from escaped text. e.g. '\n\x41'

- `from_hex <hex_string>`
	bytes from hex string, '0x' prefix ok

- `from_list <list>`
	bytes from int(0-255) list

- `is_empty <bytes>`
	is empty?

- `len <bytes>`
	byte length

- `pop <bytes>`
	drop last byte, returns new bytes

- `position <bytes> <bytes>`
	index of sub-sequence, -1 if absent

- `push <bytes> <int>`
	append byte(0-255), returns new bytes

- `repeat <bytes> <n>`
	repeat n times

- `reverse <bytes>`
	reverse bytes

- `slice <bytes> <start> <end>`
	sub-bytes [start,end)

- `split <bytes> <int>`
	split by byte value(0-255)

- `starts_with <bytes> <bytes>`
	starts with prefix?

- `to_base64 <bytes>`
	to base64 string

- `to_hex <bytes>`
	to hex string

- `to_list <bytes>`
	to list of int(0-255)

- `to_string <bytes>`
	to utf8 string (lossy)

