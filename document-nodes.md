# Document Nodes

Hexdown document content is represented as a tree of nodes within each card's face. The node classes are:

- **petal** — single-sip leaf inside a blossom; carries rendered content data
- **graft** — single-sip leaf inside a bough; the site where a child card is joined to its parent. Carries a kind sip naming the child card grafted at that point
- **blossom** — inner node whose direct children are petals; the smallest named word-scale content unit (appears only in leaf trellises)
- **stem** — inner node whose direct children are blossoms or other stems; combines word-scale units into larger structures (appears only in leaf trellises)
- **bough** — top-level inner node of a branch trellis; whose direct children are grafts naming child card kinds (appears only in branch trellises)

Every leaf of the tree is a single sip — a petal (inside a blossom) or a graft (inside a bough). Every inner node begins with a kind sip that tells the parser what to expect next (the *kind-glyph-implies-form* discipline; see [metaschema.md](metaschema.md)).

The class a card's face inhabits depends on its trellis flavor. **Branch trellises** produce faces rooted in a bough, whose children are grafts naming child card kinds — branch card faces hold no rendered content. **Leaf trellises** produce faces rooted in a stem or blossom over petals — leaf cards hold the actual rendered material.

The discipline this expresses: **no bough nodes on leaf cards**. A branch card can graft to a stem (the face root of the leaf card the bough's graft references), but the body of any stem — and the blossoms and petals it contains — lives only on leaf cards.

See [glossary.md](glossary.md) for one-paragraph definitions of the core terms; see [encoding.md](encoding.md) for the sip → glyph mapping used by phonetic petals.

## Petals

A petal is a single sip at a leaf position inside a blossom. Petals carry the rendered content of leaf cards; no petals appear in branch cards.

A petal's *meaning* is determined by its parent blossom kind:

- inside a **neem** or **prop**: a phoneme
- inside a **quant**: a digit, mantissa bit-group, exponent bit-group, or precision marker (kind-specific layout)
- inside an **enum**: an ordinal or range marker
- inside a **unipoint**: a bit-group of a 32-bit unicode codepoint

The full sip → phoneme/glyph mapping for phonetic petals lives in [encoding.md](encoding.md).

## Grafts

A graft is a single sip at a leaf position inside a bough — the site where a child card is joined to its parent branch card. Each graft's value is a kind sip naming the kind of the child card grafted at that position. The card's back resolves each graft position to a child card-id via its `child-card-refs` list.

Grafts are the structural counterpart of petals: petals carry rendered content (in leaf cards), grafts carry structural references to child cards (in branch cards). Both are single-sip leaves of the document-node tree, distinguished by which kind of parent they live in.

The plant-grafting metaphor: the bough is the rootstock (the receiving structure), the child card's face root is the scion (the thing joined in), and the graft itself is the join site — the single-sip slot in the bough naming the kind of the card grafted there.

## Blossoms

A blossom is an inner node whose direct children are petals. Each blossom kind interprets its petal children differently. Blossoms appear only in leaf trellises.

- **neem** — a phonetic word or sub-word; petals are phonemes under the core phoneme mapping.
- **prop** — a phonetic word with leading-capital marking, used for proper nouns. Same petal interpretation as neem; the kind distinguishes capitalization structurally rather than encoding it in the petals. Context-driven capitalization (sentence-initial, title-case) is applied by the parent stem at render time, not by the leaf — only proper-noun capitalization is structural.
- **quant** — a numeric quantity. Petals encode (mantissa, exponent, precision) under a kind-specific layout. (Detailed design deferred.)
- **enum** — an integer enumeration within a range. Petals encode (ordinal, range) under a kind-specific layout. (Detailed design deferred.)
- **uniglyph** — a unicode word or grapheme. Contains a sequence of unipoint inner nodes, each of which is a fixed-arity inner node with 6 petal children encoding a 32-bit unicode codepoint. Used for content outside hexdown's native phoneme/numeric alphabets — other writing systems, emoji, math symbols. (Detailed design deferred.)

## Stems

Stems combine blossoms or other stems into larger structures. Stems appear only in leaf trellises. Some stems carry rendering rules (capitalization, punctuation) that apply to their children at render time.

- **span** — combines neems, props, quants, enums, and uniglyphs into a single compound word (e.g., "Mt-Rainier" as prop + neem; "DNA" as a sequence of uniglyphs).
- **phrase** — combines blossoms and spans into a clause; rendered with a comma if not last within its parent.
- **statement** — combines phrases into a sentence; renders first-letter-capitalized with a terminating period.
- **question** — like statement but ends in `?`.
- **exclamation** — like statement but ends in `!`.
- **title** — phrase-like stem rendered with title-case capitalization on every word.

Higher-level stems (paragraph, list, quote, note, etc.) and trellis-specific stems are described in the relevant trellis definitions in [core-arbors.md](core-arbors.md).

## Boughs

A bough is the top-level inner node of a branch trellis. Its direct children are grafts — single sips whose values name the kind of child card at each position. The card's back resolves each graft position into a card-id via the `child-card-refs` list.

Boughs appear only in branch trellises (taproot, book, chapter, section, etc.). Their function is to enumerate which child cards exist and what each one is for, while delegating the actual rendered content to those child cards. This is the discipline that keeps branch cards free of rendered content.

The taproot trellis describes a specific kind of bough — its graft kinds include the meta cards (at, dex, status, ...) plus the single body root (omnibus | book | chapter | section, depending on document size).

## Face roots and back roots

- **face-root** — the entry node of a card's face. In branch cards this is a bough; in leaf cards it is a stem or blossom appropriate to the trellis.
- **back-root** — the entry node of a card's back; the top of the back's metaschema record.

(The previous "branch" document-node kind has been retired. The distinction between structural and content nodes now lives at the trellis flavor level — branch trellis vs leaf trellis — rather than at the per-node level.)

## Open questions

- Whether `prop` should be a separate blossom kind or a structural flag on neem (current lean: separate kind, for structural cleanliness).
- The exact unipoint / uniglyph design — bit layout within a unipoint's 6 petal children, what marker bits go where, whether uniglyph is variable-length or has internal substructure. (Detailed design deferred.)
- Whether quant carries SI-unit information; if so, where (additional sip field, sibling node, trellis-level, ...?). (Deferred.)
- Exact bit allocation within quant (mantissa / exponent / precision split). (Deferred.)
- Whether enum ranges are fixed by the trellis or carried per-blossom. (Deferred.)
- Whether sentence-level stems should always wrap their content in phrase children (current lean: yes, for symmetry).
