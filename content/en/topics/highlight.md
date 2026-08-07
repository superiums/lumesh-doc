---
title: Syntax Highlighting
date: 2025-12-25 19:16:45
---

## Syntax Highlighting for Lumesh
> As Lumesh is a new language, how can editors get syntax highlighting support?

The author has created a universal editor syntax highlighting project:

[tree-sitter-lumesh](https://github.com/superiums/tree-sitter-lumesh)

Editors supporting tree-sitter will quickly get syntax highlighting support.

### Helix Editor

- **Method 1**: When installing lume using install.sh, syntax highlighting support for helix will be automatically added

- **Method 2**: Using precompiled files

  1. Add the following to helix's configuration file `languages.toml`:

  ```toml file-name=~/.config/helix/languages.toml

  # Other locations
  [[language]]
  name = "lumesh"
  scope = "source.lumesh"
  injection-regex = "lumesh"
  file-types = ["lm", "lumesh"]
  roots = []
  comment-token = "#"
  indent = { tab-width = 2, unit = "  " }

  ```
  
  2. Link the syntax highlighting files
  
  + For personal installation:
  ```bash
  ln -s ~/.local/share/lumesh/tree-sitter-lumesh/grammars/lumesh.so ~/.config/helix/runtime/grammars
  ln -s ~/.local/share/lumesh/tree-sitter-lumesh/queries ~/.config/helix/runtime/
  ```

  + For system installation:
  ```bash
  ln -s /usr/local/share/lumesh/tree-sitter-lumesh/grammars/lumesh.so ~/.config/helix/runtime/grammars
  ln -s /usr/local/share/lumesh/tree-sitter-lumesh/queries ~/.config/helix/runtime/
  ```


- **Method 3**: From source compilation:

  1. Add the following to helix's configuration file `languages.toml`:

  ```toml file-name=~/.config/helix/languages.toml
  # At the top, to avoid downloading source code for other languages
  use-grammars = { only = [ "lumesh" ] }  

  # Other locations
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

  2. Run command:
  ```bash
  helix --grammar fetch
  helix --grammar build
  ```

  3. Run command:
  ```bash
  cp ~/.config/helix/runtime/grammars/sources/lumesh/queries/* /home/tix/.config/helix/runtime/queries
  ```

**Usage:**

Files with `.lm` extension or files with `lumesh` in the shebang line will get syntax highlighting support.

## Syntax Highlighting for Lumelf
> Use Lumesh to write configuration files for lf file manager

The author has created a universal editor syntax highlighting project:

[tree-sitter-lumelf](https://github.com/superiums/tree-sitter-lumelf)

Editors supporting tree-sitter will quickly get syntax highlighting support.

### Helix Editor

- **Method 1**: When installing lume using install.sh, syntax highlighting support for helix will be automatically added

- **Method 2**: Using precompiled files or source code, similar to the previous section

  But the configuration is slightly different; add the following to helix's configuration file `languages.toml`:

  ```toml file-name=~/.config/helix/languages.toml

  [[language]]
  name = "lumelf"
  scope = "source.lumelf"
  injection-regex = "lumelf"
  shebangs = ["lumelf"]
  file-types = ["lmf"]
  roots = []
  comment-token = "#"
  indent = { tab-width = 2, unit = "  " }

  [[grammar]]  
  name = "lumelf"  
  source = { git = "https://github.com/superiums/tree-sitter-lumelf", rev = "v0.13.1" }

  ```

Other steps are the same as the previous section

**Usage:**

Add the following shebang line at the first line of lfrc:
`#! lumelf`
Or name the file with `.lmf` extension.