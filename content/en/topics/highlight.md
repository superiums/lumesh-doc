---
title: Syntax Highlighting  
date: 2025-12-25 19:16:45
---

Lumesh Syntax Highlighting
--------------------------

> How can Lumesh, a new language, get syntax highlighting support in editors?

The author has created a syntax highlighting project for general editors:

[tree-sitter-lumesh](https://github.com/superiums/tree-sitter-lumesh)

Editors that support tree-sitter will be able to quickly get syntax highlighting support.

### Helix Editor

*   Method 1: When installing lume with install.sh, it automatically adds syntax highlighting support for helix.

*   Method 2: Use precompiled files

1.  Add the following to your helix configuration file `languages.toml`:

```toml file-name=~/.config/helix/languages.toml

# Other sections
[[language]]
name = "lumesh"
scope = "source.lumesh"
injection-regex = "lumesh"
file-types = ["lm", "lumesh"]
roots = []
comment-token = "#"
indent = { tab-width = 2, unit = "  " }

```


2.  Link the syntax highlighting files

*   For personal installation:
```bash
ln -s ~/.local/share/lumesh/tree-sitter-lumesh/grammars/lumesh.so ~/.config/helix/runtime/grammars
ln -s ~/.local/share/lumesh/tree-sitter-lumesh/queries ~/.config/helix/runtime/
```

*   For system installation:
```bash
ln -s /usr/local/share/lumesh/tree-sitter-lumesh/grammars/lumesh.so ~/.config/helix/runtime/grammars
ln -s /usr/local/share/lumesh/tree-sitter-lumesh/queries ~/.config/helix/runtime/
```


*   Method 3: Compile from source:

1.  In your helix configuration file `languages.toml`, add:

# Top of the file, to avoid downloading other language sources
```toml
use-grammars = { only = [ "lumesh" ] }  


# Other sections
[[language]]
name = "lumesh"
scope = "source.lumesh"
injection-regex = "lumesh"
file-types = ["lm", "lumesh"]
roots = []
comment-token = "#"
indent = { tab-width = 2, unit = "  " }

[[grammar]]  
name = "lumesh"  
source = { git = "https://github.com/superiums/tree-sitter-lumesh", rev = "v0.13.0" }
```


2.  Run the following commands:

```bash
helix --grammar fetch
helix --grammar build

```

3.  Run the following command:

```bash
cp ~/.config/helix/runtime/grammars/sources/lumesh/queries/* /home/tix/.config/helix/runtime/queries
```

Usage:

Files with the `.lm` extension or those with a shebang line containing `lumesh` will receive highlighting.

## Lumelf Syntax Highlighting

Using Lumesh to write configuration files for lf file manager

The author has created a syntax highlighting project for general editors:

[tree-sitter-lumelf](https://github.com/superiums/tree-sitter-lumelf)


Editors that support tree-sitter will be able to quickly get syntax highlighting support.

#### Helix Editor
Method 1: When installing lume with install.sh, it automatically adds syntax highlighting support for helix.

Method 2: Use precompiled files or source code, similar to the previous section

But the configuration is slightly different. Add the following to your helix configuration file languages.toml:

```toml
[[language]]
name = "lumelf"
scope = "source.lumelf"
injection-regex = "lumelf"
shebangs = ["lumelf"]
file-types = ["lmf"]
roots = []
comment-token = "#"
indent = { tab-width = 2, unit = " " }

[[grammar]]
name = "lumelf"
source = { git = "https://github.com/superiums/tree-sitter-lumelf", rev = "v0.13.1" }
```

Other steps are the same as the previous section.

Usage:

Add the following shebang line at the beginning of your lfrc:
`#! lumelf`
Or name the file with the `.lmf` extension.
