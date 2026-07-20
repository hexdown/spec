# glyphs

## sip node classes

sip nodes are separated into different classes to support parsing the hexdown metastructure

- all bits high - null node
- first 2 bits high - blossom node
- first 1 bits high - branch node
- first bit low - stem node

## table of glyphs

each glyph has a meaning as a node kind and a different meaning as a petal

| octal value | node class | glyph | node meaning | petal meaning |
|:--------|:------|:------|:------|:------|
| `0o00` | stem | `0` | **banner** | |
| `0o01` | stem | `1` | **title** | |
| `0o02` | stem | `2` | | |
| `0o03` | stem | `3` | | |
| `0o04` | stem | `4` | | |
| `0o05` | stem | `5` | | |
| `0o06` | stem | `6` | | |
| `0o07` | stem | `7` | | |
| `0o10` | stem | `8` | | |
| `0o11` | stem | `9` | | |
| `0o12` | stem | `a` | | |
| `0o13` | stem | `b` | | |
| `0o14` | stem | `c` | | |
| `0o15` | stem | `d` | | |
| `0o16` | stem | `e` | | |
| `0o17` | stem | `f` | | |
| `0o20` | stem | `g` | | |
| `0o21` | stem | `h` | | |
| `0o22` | stem | `i` | | |
| `0o23` | stem | `j` | | |
| `0o24` | stem | `k` | | |
| `0o25` | stem | `l` | | |
| `0o26` | stem | `m` | | |
| `0o27` | stem | `n` | | |
| `0o30` | stem | `o` | | |
| `0o31` | stem | `p` | | |
| `0o32` | stem | `q` | | |
| `0o33` | stem | `r` | | |
| `0o34` | stem | `s` | | |
| `0o35` | stem | `t` | | |
| `0o36` | stem | `u` | | |
| `0o37` | stem | `v` | | |
| `0o40` | branch | `w` | | |
| `0o41` | branch | `x` | | |
| `0o42` | branch | `y` | | |
| `0o43` | branch | `z` | | |
| `0o44` | branch | `β` | | |
| `0o45` | branch | `Γ` | | |
| `0o46` | branch | `Δ` | | |
| `0o47` | branch | `θ` | | |
| `0o50` | branch | `λ` | | |
| `0o51` | branch | `μ` | | |
| `0o52` | branch | `Ξ` | | |
| `0o53` | branch | `π` | | |
| `0o54` | branch | `Σ` | | |
| `0o55` | branch | `φ` | | |
| `0o56` | branch | `ψ` | | |
| `0o57` | branch | `Ω` | | |
| `0o60` | blossom | `:` | | |
| `0o61` | blossom | `!` | | |
| `0o62` | blossom | `?` | | |
| `0o63` | blossom | `&` | | |
| `0o64` | blossom | `$` | | |
| `0o65` | blossom | `%` | | |
| `0o66` | blossom | `@` | | |
| `0o67` | blossom | `#` | | |
| `0o70` | blossom | `~` | | |
| `0o71` | blossom | `±` | | |
| `0o72` | blossom | `=` | **quant** — quantity | |
| `0o73` | blossom | `^` | **prop** — proper name (capitalized neem) | |
| `0o74` | blossom | `"` | **neem** — phoneme | |
| `0o75` | blossom | `'` | **graft** — join to child card | **elide** |
| `0o76` | blossom | `*` | **bloom** | **possess** |
| `0o77` | null | `-` | **null** | **beat** |
