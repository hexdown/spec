# the passage schema, hand-encoded — the metaschema's acid test

the passage trellis written out as an actual schema card under the draft metaschema. purpose: force the open representational questions in [encoding.md](../encoding.md)'s metaschema section to concrete answers, the way [ch4-annotation.md](ch4-annotation.md) forced the content grammar. every decision below is **proposed** (2026-07-18) and marked ⚖ where alternatives are live.

## the six decisions this forces

1. ~~⚖~~ **decided 2026-07-19: kind values are schema-wide, assigned by declaration order.** (directions re-derived 2026-07-20 under [glyphs.md](../glyphs.md): stems ascend from `0o01`, boughs descend from `0o57`, blossoms descend from `0o73`.) reserved kinds keep their fixed values everywhere. a kind node never stores its own value — *position is the value*. **refinement (philetus): a pad in a kind-declaration slot skips a value** — empty positions let a schema pin kinds at chosen values without a full set (and the same pad-in-a-slot move marks absent fields in any fixed layout — resolving the absence open question).
2. ~~⚖~~ **decided 2026-07-19: kids lists are blossoms holding value petals** — one petal per acceptable child kind; graft's structural sibling.
3. **the kind node has two children, and its family is derived**: `kind[name-neem, spec]` where spec is either a `kids` blossom (⇒ a stem kind) or a `layout` blossom (⇒ a blossom kind; its petal names the petal interpretation — phoneme, numeric, …). no stored family bit, no stored value: both derived.
4. **crowns are a dedicated node**: a blossom of value petals naming the card-root-eligible kinds, sitting right after the schema's name — front matter first.
5. **single cards suffice.** the passage schema lands around ~350 sips (≈18% of the slurp cap) — no taproot-document apparatus needed for schemas of this size; the document shape remains available if a schema ever outgrows a card.
6. **metatrellis and metarbor unify.** there is *one* metaschema, and "trellis" and "arbor" are two of its root kinds. (superseded 2026-07-19: the arbor root then dissolved entirely — an arbor is a taproot's trellis, and trellis is the sole root kind; see [mary-frances-schemas.md](mary-frances-schemas.md).)

## the metaschema vocabulary (hardcoded, known by heart)

kind values in the metaschema's own context — fixed forever, like the reserved kinds:

(re-valued 2026-07-20 under [glyphs.md](../glyphs.md) — four families, null at `0o77`, blossoms and boughs descend.)

| value | kind | family | children |
|:--|:--|:--|:--|
| `0o01` | trellis | stem | name-neem, crowns, kind* — the sole schema-card root (the arbor root dissolved; see [mary-frances-schemas.md](mary-frances-schemas.md)) |
| `0o02` | kind | stem | name-neem, spec (kids \| layout \| bloom) |
| `0o73` | kids | blossom | value petals: the acceptable child kinds |
| `0o72` | crowns | blossom | value petals: the card-root-eligible kinds |
| `0o71` | layout | blossom | one petal: the petal-interpretation of a blossom kind (0 = phoneme, …) |

plus the reserved kinds available everywhere: schema node `0o00`, neem `0o74` (names), graft `0o75`, bloom `0o76` (the null hash marking schema cards), null `0o77`.

## the passage schema

declaration order fixes the values (decision 1; stems ascend from `0o01`, blossoms descend from `0o73`):

| value | kind | | value | kind |
|:--|:--|:--|:--|:--|
| 1 | paragraph | | 7 | quoth |
| 2 | statement | | 8 | fade |
| 3 | question | | 9 | phrase |
| 4 | exclamation | | 10 | pivot |
| 5 | broken | | 59 | prop (`^`) |
| 6 | turn | | 60 | neem (reserved, `·`) |

the card, in tree notation:

```
pad*
schema-node
  bloom: ················  ← the null hash: 64 beats — "parse me with the metaschema"
  trellis
    neem: passage
    crowns: [paragraph]                      ← verse, list join when designed
    kind  neem: paragraph   kids: [statement question exclamation broken turn]
    kind  neem: statement   kids: [phrase pivot quoth]
    kind  neem: question    kids: [phrase pivot quoth]
    kind  neem: exclamation kids: [phrase pivot quoth]
    kind  neem: broken      kids: [phrase pivot quoth]
    kind  neem: turn        kids: [statement question exclamation broken quoth fade]
    kind  neem: quoth       kids: [phrase]
    kind  neem: fade        kids: [statement question exclamation broken phrase]
    kind  neem: phrase      kids: [neem prop]
    kind  neem: pivot       kids: [neem prop]
    kind  neem: prop        layout: phoneme
pad*
```

(`[...]` = value petals per decision 2; shown as names for legibility. span is omitted — chapter 4 needs no spans; it joins with the kind-mixing compounds. the two-level quoth admission — embedded and sentence-level — is simply *membership in two kids lists*: turn's and the sentence kinds'. the level is the meaning, encoded as membership.)

## sip arithmetic

- schema node + null bloom: 2 + 66 = 68
- trellis root: 2 · name "passage": 2+7 = 9 · crowns: 2+1 = 3
- 11 kind nodes: each 2 (kind) + 2+len (name-neem) + 2+n (kids or layout)
  - names total 89 petals across 11 kinds; kids/layout petals total 27
  - kinds subtotal: 22 + 22+89 + 22+27 = 182
- **total: ~264 sips + pads — 14% of the 1920-sip slurp cap**

one card, room to quadruple. decision 5 holds with margin; even the full markdown flora would fit.

## what this settles in encoding.md (once blessed)

- the kind node's "one-petal kind-value blossom" (the badge) **dissolves** — values are positional, nothing stores them
- the acceptable-children representation: kids as value-petal blossoms
- the crown-list encoding: a crowns blossom after the name
- schema cards are single cards (document shape optional at scale)
- metatrellis/metarbor → one metaschema (root kinds later reduced to trellis alone — the arbor dissolved)
- remaining for the arbor exercise: the arbor root's grammar (positions! the taproot's body-last, the banner-first idiom, trellis references across cards — where names must resolve *between* schemas, not just within one)

## open questions

- ⚖ decisions 1 and 2 (positional values; petal-lists) — the live alternatives are noted inline
- the layout enumeration for blossom kinds (phoneme = 0; numeric, enum, unipoint layouts when those kinds firm up)
- arity constraints: chapter 4 needed only *sets* of acceptable children — positional/arity constraints (body-last, banner-first, quoth's exactly-one-phrase-minimum) belong to branch schemas and richer validation; the kids representation will need an extension when they arrive
- the concrete glyph table (letter → sip value) gates spelling this card as an *actual* sip stream — the last step between this annotation and real bytes
