# Document Nodes

Hexdown document content is represented as a tree of nodes within each card's face. The tree has three structural levels:

- **sip** — the raw 6-bit data unit; not a node
- **blossom** — leaf nodes; the smallest visible units of a document
- **stem** — inner nodes; combine blossoms and other stems into larger structures

A card's face is a tree rooted at a face-root, with stems (and possibly branches) in the middle and blossoms at the leaves.

See [glossary.md](glossary.md) for one-paragraph definitions of the core terms; see [encoding.md](encoding.md) for the sip → glyph mapping.

## Blossoms

Every blossom is a flat sip sequence whose internal layout depends on its kind. Blossoms never contain other nodes — they hold only sips.

- **neem** — a phonetic word or sub-word. Sips encode phonemes under the core phoneme mapping.
- **prop** — a phonetic word with leading-capital marking, used for proper nouns. Same internal shape as a neem; the kind distinguishes capitalization structurally rather than encoding it in the sip stream.
- **quant** — a numeric quantity. Sips encode (mantissa, exponent, precision) under a kind-specific layout.
- **enum** — an integer enumeration within a range. Sips encode (ordinal, range) under a kind-specific layout.
- **uniglyph** — a unicode word or grapheme. Sips encode a sequence of 32-bit unicode codepoints (unipoints), each ~6 sips. Used for content outside hexdown's native phoneme/numeric alphabets — other writing systems, emoji, math symbols.

## Stems

Stems combine blossoms or other stems into larger structures.

- **span** — combines neems, props, quants, enums, and uniglyphs into a single compound word (e.g., "Mt-Rainier" as prop + neem; "DNA" as a sequence of uniglyphs).
- **phrase** — combines blossoms and spans into a single thought or clause; one level above the word.

Higher-level stems (paragraph, statement, question, list, quote, note, etc.) and trellis-specific stems (banner, etc.) are described in the relevant trellis definitions in [core-arbors.md](core-arbors.md).

## Branches and roots

- **branch** — TBD. Previously framed as a stem-like node describing high-level document organization (chapter, section) within a card. In the current hexdown model these organizational levels are typically *cards* connected through child-card refs, not within-card nodes, so the role of "branch" is unclear and may collapse into "stem".
- **face-root** — the entry node of a card's face; the top of the document-node tree.
- **back-root** — the entry node of a card's back; the top of the back's metaschema record.

## Open questions

- Whether `prop` should be a separate blossom kind or a structural flag on neem (current lean: separate kind, for structural cleanliness).
- Whether `unipoint` deserves first-class node status or remains an internal detail of uniglyph (current lean: internal detail).
- Whether quant carries SI-unit information; if so, where (additional sip field, sibling node, trellis-level, ...?).
- Exact bit allocation within quant (mantissa / exponent / precision split).
- Whether enum ranges are fixed by the trellis or carried per-blossom.
- The branch / stem distinction — is there a clean structural difference, or is "branch" just a stem in a particular position? If the latter, the term may be retired.
