# the mary frances schema set — arbor and trellises, hand-encoded

the full schema set for the mary frances garden book, continuing [passage-schema.md](passage-schema.md). working out the arbor produced the design's biggest simplification, so that finding leads.

## the finding: the arbor dissolves

the question was "how does the metaschema map to an arbor definition?" — and the answer is that **it already did.** watch:

- a branch card's face is a bough over grafts; each graft's petal names a *position kind* in the enclosing schema's vocabulary
- so a branch schema must say, for each position kind, *which schema governs the cards grafted there* — a reference to another schema card, and schema identity is content hash — **a bloom**
- so the metaschema gains its third spec shape, and the kind node's spec now determines everything:

| spec shape | the kind is a... | meaning |
|:--|:--|:--|
| kids | stem kind | children are nodes of the listed kinds |
| grafts | bough kind | the position kinds this bough grafts (added 2026-07-20; one shape per family) |
| layout | blossom kind | petals are interpreted under the named layout |
| **bloom** | **position kind** | cards grafted at this position parse under the schema with this hash |

- and then: **an arbor is just the taproot card's trellis.** the document grammar is the transitive closure of bloom-linked schemas starting from the taproot's — taproot-schema → section-schema → passage, each card in the chain declaring its own governor by hash. there is no separate arbor grammar, no metarbor, no `arbor` root kind: the metaschema's root kinds reduce to **trellis alone**.

the back's `arbor-ref` becomes pure index under the two-coordinate pattern: the truth is the taproot face's schema bloom and its closure.

## position kinds

- declared like any kind — `kind[name, bloom(#target-schema)]` — and drawing values in declaration order from the blossom seats, descending (they live only as petal values, in grafts and kids; they never head a node)
- a bough kind's grafts list names position kinds; structurally its children are reserved grafts, each holding one position-kind petal, resolved through the back's child-card-refs in face order
- positional conventions (banner first, body last, exactly-one body) remain prose conventions for now — chapter 4 needed only sets; a constraint vocabulary is future work

## the schema set

six cards, hash-chained, no cycles (blooms shown symbolically as `#name` until the glyph table lands):

### passage — done ([passage-schema.md](passage-schema.md), ~264 sips)

### banner (leaf, ~120 sips)

```
trellis
  neem: banner
  crowns: [title]
  kind  neem: title   kids: [neem prop quant]
  kind  neem: quant   layout: numeric        ← first quant declaration (chapter numbers)
```

### section (branch, ~250 sips)

```
trellis
  neem: section
  crowns: [section]
  kind  neem: section    grafts: [banner passage]   ← the bough
  kind  neem: banner     bloom: #banner              ← position kinds
  kind  neem: passage    bloom: #passage
```

### chapter (branch, ~250 sips)

```
trellis
  neem: chapter
  crowns: [chapter]
  kind  neem: chapter    grafts: [banner section]
  kind  neem: banner     bloom: #banner
  kind  neem: section    bloom: #section
```

### book (branch, ~250 sips)

```
trellis
  neem: book
  crowns: [book]
  kind  neem: book       grafts: [banner chapter]
  kind  neem: banner     bloom: #banner
  kind  neem: chapter    bloom: #chapter
```

### taproot — *this card is the prose arbor* (~700 sips)

```
trellis
  neem: taproot
  crowns: [taproot]
  kind  neem: taproot    grafts: [at dex status book chapter section passage]
  kind  neem: at         bloom: #at        ← meta schemas TBD; pads may hold these
  kind  neem: dex        bloom: #dex          slots until the schemas exist
  kind  neem: status     bloom: #status
  kind  neem: book       bloom: #book      ← the flexible root: the single body
  kind  neem: chapter    bloom: #chapter      graft (last position) may be any of
  kind  neem: section    bloom: #section      these four, by document size
  kind  neem: passage    bloom: #passage
```

chapter-4-standalone instantiates taproot → section; the full book, taproot → book → 65 × chapter → sections → passages. same six schemas either way.

## the chicken-egg, noted

a schema cannot bloom-reference *itself* — its hash can't appear in its own content. mary frances never needs it: prose nesting is strict (book → chapter → section → passage), so the chain is acyclic. truly recursive structures (sections in sections, markdown's nested lists) will someday want a **self sentinel** — perhaps a short bloom (blooms may hold 1–64 petals; a 1-petal bloom is a natural sentinel space, with the 64-beat null hash as precedent). logged as an open question, not solved.

## space accounting

philetus's concern: 30 stems + 29 blossoms per schema. mitigations, in order of arrival:

1. **schemas are small on purpose** — the six above use 2–11 kinds each; the split-per-card-kind escape hatch (verse and list as their own trellises) is architecturally *free*, since every card names its schema by hash
2. **pads pin values** — sparse declaration lists mean related schemas can keep shared kinds at aligned values without padding out full sets
3. per-parent-context values remain the deep reserve if a single schema ever genuinely needs more than 30 stems

## open questions

- the self-reference sentinel for recursive structures (short-bloom sentinel space?) — philetus leans (2026-07-19): *reject recursion*, keep an explicit set of defined levels; decision deferred
- at / dex / status schemas — needed before a taproot card can be fully populated; pads can hold their kind-declaration slots meanwhile
- the constraint vocabulary (banner-first, body-last, exactly-one-body as machine-checkable rules rather than prose)
- cross-schema *names*: blooms link schemas by hash; whether position-kind names must match the target schema's own name (lean: no — the name is local, the bloom is the truth; flora indexes the human view)
- the glyph table — still the last gate between all of this and real bytes
