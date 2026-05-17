# Hexdown

*Orchards of documents, grown card by card by many hands.*

Version 0.1.

A hexdown document is a tree of **cards**. Each card is small, content-addressed, and individually referenceable — so large documents are composed of many cards, and any card can appear in any number of documents. Card content is described as a tree of document nodes encoded as a sequence of 6-bit **sips**. The corpus is append-only: every state is reachable as a history of deltas applied to prior states.

Hexdown takes its document macrostructure from [Hypercard](https://en.wikipedia.org/wiki/Hypercard), its content semantics from [Markdown](https://en.wikipedia.org/wiki/Markdown), and its compact glyph encoding from its predecessor project [pentabased](https://github.com/pentabased/spec). The combination is intended to support corpora that humans and computational agents read, write, and reason about on equal footing.

## Reading this spec

- [vision.md](vision.md) — broader goals and commitments
- [glossary.md](glossary.md) — every named concept, one paragraph each
- [data-model.md](data-model.md) — orchards, plots, arbors, documents, cards
- [encoding.md](encoding.md) — 6-bit sip glyphs and the base mapping
- [document-nodes.md](document-nodes.md) — blossom and stem kinds within a card's face
- [metaschema.md](metaschema.md) — how arbors and AST node trees serialize as sip streams
- [core-arbors.md](core-arbors.md) — built-in arbors and trellises
- [deltas.md](deltas.md) — till and flush delta semantics
- [roadmap.md](roadmap.md) — current status, planned implementations, and the first concrete milestone

## Status

Early specification development. Many sections contain TBDs and open questions. The next concrete milestone is a Python prototype implementation ([hwatu](https://github.com/hexdown/hwatu)) that ingests a small canonical markdown corpus as cards; see [roadmap.md](roadmap.md) for more.
