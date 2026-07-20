# Hexdown

*Orchards of documents, grown card by card by many hands.*

Version 0.1.

A hexdown document is a tree of **cards**. Each card is small, content-addressed, and individually referenceable — so large documents are composed of many cards, and any card can appear in any number of documents. Card content is described as a tree of document nodes encoded as a sequence of 6-bit **sips**. The corpus is append-only: every state is reachable as a history of deltas applied to prior states.

Hexdown takes its document macrostructure from [Hypercard](https://en.wikipedia.org/wiki/Hypercard), its content semantics from [Markdown](https://en.wikipedia.org/wiki/Markdown), and its compact glyph encoding from its predecessor project [pentabased](https://github.com/pentabased/spec). The combination is intended to support corpora that humans and computational agents read, write, and reason about on equal footing.

## Reading this spec

Files and sections are named for ideas a reader already carries, so the spec can be navigated before it is absorbed. The story, in reading order:

- [vision.md](vision.md) — broader goals and commitments
- [data-model.md](data-model.md) — orchards, plots, cards, faces and backs; node classes; card scale
- [schemas.md](schemas.md) — the shipped arbors and trellises
- [encoding.md](encoding.md) — sips, glyphs, metaschema rules, bootstrap grammars, on-disk notes
- [deltas.md](deltas.md) — till and flush delta semantics

The reference shelf — consult rather than read straight through:

- [glossary.md](glossary.md) — every named concept, one paragraph each
- [flora.md](flora.md) — the field guide to blossom and stem kinds
- [glyphs.md](glyphs.md) — the sip value table: kind families, glyphs, kind and petal meanings

Working state lives in [design/](design/): [roadmap.md](design/roadmap.md) (status and milestones), [corpus-demands.md](design/corpus-demands.md) (what the corpus asks of the trellises), and session documents such as [speech-examples.md](design/speech-examples.md). Design docs die by being absorbed into the spec proper.

## Status

Early specification development. Many sections contain TBDs and open questions. The next concrete milestone is a Python prototype implementation ([hwatu](https://github.com/hexdown/hwatu)) that ingests a small canonical markdown corpus as cards; see [design/roadmap.md](design/roadmap.md) for more.
