# glyphs

the sip value table: every value's kind family, display glyph, and meanings as a kind sip and as a petal. a reference-shelf doc alongside [glossary.md](glossary.md) and [flora.md](flora.md). (adopted 2026-07-20, superseding the two-halves table.)

## sip node families

kind sips are separated into four families by their leading bits, so the metastructure parses with no schema in hand:

- first bit low (`b0xxxxx`, 32 values) — **stem** node: children are nodes
- first bit high, second low (`b10xxxx`, 16 values) — **branch** node: children are exclusively grafts
- first two bits high (`b11xxxx`, 15 values) — **blossom** node: children are petals
- all bits high (`b111111`) — **null** node: a single sip, no count, no children

the whole structural parser:

```
parse_node():
    kind = read_sip()
    if kind == 0o77:   return null                                  # pad
    count = read_sip() + 1
    if kind >= 0o60:   return blossom(kind, read_sips(count))       # petals
    if kind >= 0o40:   return branch(kind, [parse_node() ...])      # children must be grafts
    return stem(kind, [parse_node() for _ in range(count)])
```

## petals are base-36

alphanumeric petals carry their base-36 value: digit *d* = value *d*, letter `a` = 10 … `z` = 35 — `int(glyph, 36)` in any language. alphabetic sort of raw sip streams is alphabetical order; a petal below 36 is always alphanumeric. the small marks sit at the top: elide `0o75`, possess `0o76`, beat `0o77`. petal values `0o44`–`0o74` are reserve.

## assignment directions

contextual kinds take their values positionally, per family, from declaration order in the governing schema:

- **stems ascend** from `0o01` (`0o00` is the reserved schema node)
- **branches descend** from `0o57` — every schema's first bough wears `Ω`, and contextual boughs stay in greek
- **blossoms descend** from `0o73` (below the reserved top)

a pad in a declaration slot skips a seat, letting schemas pin kinds at chosen values. by convention a schema declares **prop first and quant second** among its blossoms, seating them at `^` and `=` — conventional seats, not reservations.

## table of glyphs

each value has one meaning as a node kind (contextual unless marked ⊛ reserved) and another as a petal.

| octal | family | glyph | node kind | petal |
|:--------|:------|:------|:------|:------|
| `0o00` | stem | `0` | ⊛ **schema node** — opens every face | `0` |
| `0o01` | stem | `1` | | `1` |
| `0o02` | stem | `2` | | `2` |
| `0o03` | stem | `3` | | `3` |
| `0o04` | stem | `4` | | `4` |
| `0o05` | stem | `5` | | `5` |
| `0o06` | stem | `6` | | `6` |
| `0o07` | stem | `7` | | `7` |
| `0o10` | stem | `8` | | `8` |
| `0o11` | stem | `9` | | `9` |
| `0o12` | stem | `a` | | `a` |
| `0o13` | stem | `b` | | `b` |
| `0o14` | stem | `c` | | `c` |
| `0o15` | stem | `d` | | `d` |
| `0o16` | stem | `e` | | `e` |
| `0o17` | stem | `f` | | `f` |
| `0o20` | stem | `g` | | `g` |
| `0o21` | stem | `h` | | `h` |
| `0o22` | stem | `i` | | `i` |
| `0o23` | stem | `j` | | `j` |
| `0o24` | stem | `k` | | `k` |
| `0o25` | stem | `l` | | `l` |
| `0o26` | stem | `m` | | `m` |
| `0o27` | stem | `n` | | `n` |
| `0o30` | stem | `o` | | `o` |
| `0o31` | stem | `p` | | `p` |
| `0o32` | stem | `q` | | `q` |
| `0o33` | stem | `r` | | `r` |
| `0o34` | stem | `s` | | `s` |
| `0o35` | stem | `t` | | `t` |
| `0o36` | stem | `u` | | `u` |
| `0o37` | stem | `v` | | `v` |
| `0o40` | branch | `w` | | `w` |
| `0o41` | branch | `x` | | `x` |
| `0o42` | branch | `y` | | `y` |
| `0o43` | branch | `z` | | `z` |
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
| `0o72` | blossom | `=` | *conventional second blossom:* **quant** | |
| `0o73` | blossom | `^` | *conventional first blossom:* **prop** | |
| `0o74` | blossom | `·` | ⊛ **neem** — the universal phonetic word | |
| `0o75` | blossom | `'` | ⊛ **graft** — join to child card | **elide** |
| `0o76` | blossom | `*` | ⊛ **bloom** — hash vector | **possess** |
| `0o77` | null | `-` | ⊛ **null** — the pad | **beat** |

(neem's glyph is the **interpunct** `·`, decided 2026-07-20: the actual roman word-separator — `SENATVS·POPVLVSQVE·ROMANVS` — the mark that divided words for a millennium now opens every word. the draft's `"` was rejected as escaping-hostile in yaml envelopes; `º`, a little mouth speaking, lost by sitting one pixel from `°` degree.)

## notes

- the null sip is all-ones (`0o77`), as cheap to test as all-zeros; the null hash is a bloom of 64 beats and still renders as 64 dashes
- every schema card opens with the visible signature `01*-` followed by 64 dashes; every content card with `01*-` followed by its schema hash
- greek branch glyphs and `±` are non-ascii: the visible form trades a little typability for boughs you can spot at a glance
- known confusables `l`/`1` and `o`/`0` survive (the alphabet and digits are both needed); position disambiguates, inspectors may distinguish typographically
