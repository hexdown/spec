# Encoding

Hexdown content is encoded as a sequence of **sips** — 6-bit values, each one of 64 possibilities. A sip's role is determined by its position in the parse: it may be a kind glyph, a child count, a null marker, or — at a leaf position within a blossom-family node — a **petal** carrying character or value data. This document describes the sip values, the phoneme/glyph mapping that applies to petals, the **metastructure** (the universal, schema-free grammar by which sips form trees), the **metaschema** (the draft grammar of schema cards), and notes on on-disk representation.

## Petal values

Every bit of a serialized card belongs to a sip; kind sips and count sips take their meaning from the metastructure below, while a **petal** may take any of the 64 values. The core alphabet maps them to:

- alphanumeric characters `[0-9a-z]` (36 values)
- the **beat** glyph `-` (1 value, the null sip)
- the **elide** glyph `'` (1 value)
- the **possess** glyph `*` (1 value)
- TBD (25 values unassigned)

The same 64-value space is reused for structural roles (kind glyphs, counts, null markers) — a sip's value is just a number; whether it codes for a phoneme depends on its parse position. The phoneme mapping below describes how sip values are interpreted *as petals* within phonetic blossoms (neem, prop). Other blossom kinds (quant, enum, uniglyph) interpret their petal sips under kind-specific layouts.

All punctuation, formatting, and other markup that would normally be represented by characters in a text format is represented in hexdown by stem and bough nodes in the document tree — never by petals, with three deliberate exceptions: the small marks below live *inside* words, and are therefore petal values.

### The three small marks

Beat, elide, and possess are the glyph values that are neither letters nor digits — the punctuation that survives inside words:

- **beat** (`-`) — the null sip. The pad node's kind, the hash-arena padding value, the null petal (a bloom of 64 beats is the null hash), and the compound join: a homogeneous compound (`to-day`, `caw-caw`) is a single blossom with beat petals at its joins. Spans — which compose blossoms of *different* kinds (`Mt-Rainier`) — render their own hyphens.
- **elide** (`'`) — marks elision within a word: `haven't`, `goin'`, `fo'c's'le`. Elision is the apostrophe's original job — *apostrophos*, "turning away," the Greek name for the mark of omission — and hexdown returns the glyph to it.
- **possess** (`*`) — marks the genitive clitic: `feather flop*s argument`; word-final for plural genitives, which elide nothing (`toads*`). English's possessive apostrophe arose in part as a printers' elision of the old genitive *-es*; hexdown files possession under its own mark rather than overloading the elision glyph.

Markdown-facing renders emit `’` for both elide and possess; the structural distinction is preserved for orthographies (such as pentabased's) that render them differently. (Decided 2026-07-18; corpus evidence in [design/corpus-demands.md](design/corpus-demands.md).)

### The glyph table (proposed 2026-07-19)

The high bit splits the glyph space as it splits the kind space — a **word half** and a **value half**:

Every value has a display glyph (so any stream renders); only some values carry petal *meanings* so far — the reserves keep their glyphs while awaiting purposes:

| values | glyph | meaning |
|:--------|:------|:------|
| `0` | `-` | **beat** — the null sip, structurally forced (pads, null hash, collision arena) |
| `1-26` | `a-z` | letters: alphabet position is the value, a=1 … z=26 |
| `27` | `'` | **elide** |
| `28` | `*` | **possess** |
| `29-31` | `_ ( )` | word-half reserve (display glyphs only) |
| `32` | `^` | **the first blossom seat** (`b100000`): by convention a schema declares prop first among its blossoms, so prop wears the caret — the annotation notation made real |
| `33-42` | `0-9` | digits: digit *d* sits at 33+*d* — low five bits minus one |
| `43-60` | `, ; : ! ? = ~ / \ \| < > [ ] { } @ %` | value-half reserve (display glyphs only) |
| `61` | `.` | **neem** — the quietest mark for the most frequent kind sip: a dot opens every word |
| `62` | `+` | **graft** — the join site, drawn as a junction |
| `63` | `#` | **bloom** — the hash glyph is the hash node |

Properties: neem petals live entirely in the word half (one bit-test validates a phonetic blossom); digits self-code as low-five-bits-minus-one; raw neem streams sort alphabetically by value, so name indexes can binary-search sip streams without decoding. Emergent readability of the visible form: a word renders as a dot, a count letter, then nearly itself (`caw-caw` → `.fcaw-caw`); a full bloom opens `##`; every schema card opens with the greppable signature `--ab##…`.

Known confusables, honestly: `l`/`1` and `o`/`0` survive (the full alphabet and digits are both needed); position disambiguates in parsing, and inspectors can distinguish them typographically. Structural kind sips of *contextual* kinds necessarily share glyphs with letters and digits (a stem kind at value 3 displays `c`) — the visible form is skimmable, not self-describing; the parser is twenty lines away whenever truth is needed.

## Metastructure

**Structure is public grammar; meaning is content-addressed.** Any conforming parser can recover any card's node tree with no schema in hand — the grammar below is universal. What the tree *means* — which kinds are statements, which blossoms are words — requires the schema card named by content hash in the face's first node. A card's shape is always recoverable; its interpretation is exactly as available as its schema. (Decided 2026-07-18.)

### Node grammar

- A face is: zero or more leading pads, one **schema node**, zero or more trailing pads. The entire document tree hangs beneath the schema node.
- Every node begins with a **kind sip**.
- A **pad** (kind `b000000`) is a single-sip node with no count and no children. Before and after the schema node, pads are the collision-resolution arena for the content-addressed face hash and trailing slack; parsers skip both. In a child position, a pad is an intentionally empty slot — an absent field in a fixed layout, or a skipped value in a positional kind-declaration list (2026-07-19).
- Every other node's second sip is a **count**: values 0-63 encode 1-64 children. There is exactly one node form.
- The kind sip's high bit partitions all kinds into two families:
  - **stem family** (`b0xxxxx`) — children are nodes, parsed recursively
  - **blossom family** (`b1xxxxx`) — children are petals: single sips, flat, no substructure
- A petal's interpretation is determined by its parent's kind. Every leaf of every face is a petal.
- A blossom-family node is at most 66 sips: kind + count + up to 64 petals.

### Reserved kinds

Kind values are contextual — their meaning is assigned by the governing schema, per parent context — except five reserved values that mean the same thing in every context, forever:

| value     | kind        | family  | role                                                                 |
|:----------|:------------|:--------|:---------------------------------------------------------------------|
| `b000000` | pad         | —       | single-sip padding at the face's edges                                |
| `b000001` | schema node | stem    | opens every face; exactly 2 children: bloom (schema hash) + card root |
| `b111101` | neem        | blossom | petals are phonemes: the universal phonetic word — prose word and metaschema name alike |
| `b111110` | graft       | blossom | exactly 1 petal naming the kind of a grafted child card               |
| `b111111` | bloom       | blossom | petals are hash sips; 64 petals encode a 384-bit content hash         |

This leaves each context 30 free stem kinds and 29 free blossom kinds — the schema card governing a context assigns them. (Values arranged 2026-07-18: the two reference blossoms — graft and bloom — sit adjacent at the top, with neem, the name-and-word blossom, just below. What the metastructure sketch called *symbol* merged into neem: the metaschema names things in the same word-kind prose speaks.)

### The schema node

Every face's first non-pad node is a schema node (`b000001`) with exactly two children, in the positional idiom used everywhere in hexdown — identification first, body last:

1. a **bloom** carrying the content hash of the schema card governing this face — or the **null hash** (64 beat petals) if this card *is* a schema card, parsed under the hardcoded metaschema
2. the face's **card root** node, whose kind is one of the options the schema defines. (The **document root** is specifically the card root of a document's taproot card.)

### Grafts

A graft is a blossom-family node holding exactly one petal, whose value names the kind of the grafted child card in the enclosing schema's vocabulary. Each graft consumes, in face order, the next entry of the card back's `child-card-refs` list. The typed petal lets a document's skeleton be walked without loading child cards, and gives the orchard a free integrity check: the kind a bough declares for a child can be verified against the schema that child declares for itself.

The families give every face the same silhouette: branch faces bottom out in grafts, leaf faces bottom out in content blossoms, and every face opens with a schema node whose first child is a bloom.

### The whole structural parser

```
parse_node():
    kind = read_sip()
    if kind == PAD:      return pad
    count = read_sip() + 1
    if kind & 0b100000:  return blossom(kind, read_sips(count))   # petals
    else:                return stem(kind, [parse_node() for _ in range(count)])
```

That is the complete structural layer. Everything else — statements, turns, quants, taproots — is semantics, carried by schema cards addressed by hash.

### Methodology

**Bias toward simple symmetric rules.** When choosing between a stricter, more symmetric structural rule (e.g., "every variable-length node has 1-64 children", "every leaf is a single sip") and a more permissive but more complex rule, the metaschema picks the simpler symmetric one until a concrete use case demonstrates that the simplicity costs more than the asymmetry would. This bias is consistent with the *modest data model* commitment in [vision.md](vision.md): the cost of asymmetric flexibility is paid once by every implementation, while the cost of symmetric rigidity is paid by participants doing things the rule made awkward — and participants are usually better placed to absorb that cost.

## Metaschema (draft)

The metaschema is the grammar of schema cards — the schema that defines all other schemas. Its own grammar is hardcoded into every hexdown parser, and a schema card marks itself by giving the null hash in its schema node. Parsers may verify stored schema cards against their hardcoded grammar at load time: schemas are stored as cards, but the parser also knows them by heart. This is what makes orchards self-contained — the schemas needed to interpret an orchard's contents are themselves cards within it.

The schema-card grammar, settled by the hand-encoded passage schema (2026-07-19; [design/passage-schema.md](design/passage-schema.md)):

| value | kind | family | children |
|:--|:--|:--|:--|
| `b000010` | trellis | stem | name-neem, crowns, kind* — the sole root kind of schema cards |
| `b000011` | — | | unassigned (the arbor root dissolved: an arbor is a taproot's trellis) |
| `b000100` | kind | stem | name-neem, spec |
| `b100000` | kids | blossom | value petals: the acceptable child kinds |
| `b100001` | crowns | blossom | value petals: the card-root-eligible kinds |
| `b100010` | layout | blossom | one petal: a blossom kind's petal interpretation |

- **kind values are schema-wide and positional**: the nth stem kind declared takes the nth free stem value, blossom kinds count up from `b100000`, reserved kinds keep their fixed values — a kind node never stores its own value. A pad in a declaration slot skips a value, letting schemas pin kinds at chosen seats.
- a **kind node** has two children — its name and its spec — and the spec shape determines everything: **kids** ⇒ a stem kind (children are nodes of the listed kinds), **layout** ⇒ a blossom kind (petals interpreted under the named layout), **bloom** ⇒ a position kind (cards grafted there parse under the schema with this hash).

Dynamic loading: when a parser opens a card, it reads the face's schema node, resolves the bloom's hash to a schema card, parses that card under the metaschema (or under its own declared schema, recursively, bottoming out at the null hash), and then interprets the face under it.

Working choice for the content hash: blake2b-384 — exactly 64 petals, one full bloom. TBD to mandate. (A word-sized 64-bit hash was considered and declined: birthday collisions arrive near 2^32 faces — within reach of a large orchard — and intentional collisions cost only 2^32 work, trivial for an adversary. Word-sized speed lives in card-ids and indices instead; implementations may key internal maps on a 64-bit prefix of the hash.)

## On-disk representation

The hexdown spec is *bit-stream-agnostic*: a hexdown face is a sequence of sips, and implementations choose how to pack those sips into bytes for storage and transport. Natural choices include:

- bit-packed binary (4 sips per 3 bytes, no waste)
- byte-aligned binary (1 sip per byte, simple but 25% overhead)
- utf-8 strings using the visible glyph mapping (1 sip per 1-4 bytes, human-readable but high overhead — pentabased's bootstrapping choice)

Whatever the packing, block sizes are chosen so sips align evenly with the containing structure — for example 32 sips = 192 bits = three 64-bit words, the 24-byte increment hwatu's slurps use.

The choice does not affect the semantics described elsewhere in this spec; it affects only the bytes on disk or on the wire.

## Open questions

- Whether to mandate a leading magic number or version sip in stored sequences
- How leading beat sips for collision-resolution padding interact with packed byte boundaries (an implementation concern, but worth a spec-level note on what parsers must tolerate)
- Whether the spec should *recommend* a canonical bit-packed layout while leaving implementations free to choose
- ~~How intentional absence is marked~~ resolved 2026-07-19: a pad node in the child slot (see the pad rule above); beat petals remain the within-blossom absence (the null hash)
- ~~The kind node's representations~~ resolved 2026-07-19 by the hand-encoded passage schema: positional values, two-child kind nodes, kids/crowns as value-petal blossoms ([design/passage-schema.md](design/passage-schema.md))
- ~~The shape of the schema space~~ resolved in shape 2026-07-19: taproot-anchored closures of bloom-linked trellises, one card per schema ([design/mary-frances-schemas.md](design/mary-frances-schemas.md)); still open within it: cross-schema names (is a position-kind's local name checked against the target schema's own name?) and [flora.md](flora.md) as the human-facing index of kind names
- ~~metatrellis / metarbor~~ resolved 2026-07-19: one metaschema, one root kind — trellis; the arbor dissolved into the taproot's trellis
- ~~Face hash vs back trellis_ref~~ working pattern recorded in [data-model.md](data-model.md): two coordinate systems joined by flush history — faces content-addressed, backs stable-id indexes
- The self-reference sentinel for recursive schema structures (philetus leans: reject recursion, keep explicit levels)
- The constraint vocabulary: banner-first, body-last, exactly-one-body as machine-checkable rules rather than prose conventions
- Confirming the content hash function (blake2b-384 working choice)
