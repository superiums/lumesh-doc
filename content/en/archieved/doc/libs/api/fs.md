---
title: Builtin Lib fs
date: 2026-08-05 21:53:27
---
	
## Builtin Functions for Lib: fs

- abs <path>
	absolute path

- append <content> <file>
	append to file, creates if missing

- base_name <path>
	file name with extension

- canon <path>
	canonical path, resolves symlinks

- chmod <path> <mode:octal>
	set unix permission mode

- chown <path> <uid> <gid>
	set unix owner, -1 to keep

- cp <source> <destination>
	copy path, recursive for dirs

- dir_name <path>
	dir part before last '/'

- exists <path>
	path exists?

- extension <path>
	file extension

- glob <pattern>
	match files by pattern

- head <file> [n=10]
	first n lines

- is_dir <path>
	is dir?

- is_file <path>
	is file?

- join <segment>...
	join path segments

- ls [-l|a|h|t|L|c|u|m|p|?] [path]
	list dir contents

- mkdir <path>
	create dir, incl parents

- mv <source> <destination>
	move path

- parent <path>
	parent dir path

- read <file>
	read file, text or bytes

- read_link <link_path>
	read symlink target

- rm <path>
	remove path, recursive for dirs

- rmdir <path>
	remove empty dir

- stem <path>
	file name without extension

- symlink <source> <link_path>
	create symlink

- tail <file> [n=10]
	last n lines

- touch <path>
	create empty file, or update mtime if exists

- tree [depth=3] [path]
	dir tree as nested map

- write [content] <file>
	create/overwrite file

