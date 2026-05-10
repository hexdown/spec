# Core Arbors and Trellises

Hexdown ships a small set of built-in arbors and trellises that cover common document types. Extension arbors and trellises may be defined to handle additional document types specific to a deployment.

## Core arbors

Document-level schemas shipped with hexdown:

- **report** — book → chapter → section. For long-form prose organized hierarchically.
- **graph** — space → (directed) edge | node. For network-style documents.
- **table** — sheet → column | row → cell. For tabular documents.

TBD — full per-arbor definitions: card positions, valid trellises at each position, arity constraints, head/body splits.

## Core trellises

Microstructure schemas for individual card content:

- **taproot** — used by every document's entry-point card; carries the document's arbor reference and key/value metadata. See the draft below.
- **passage** — markdown-equivalent prose content (paragraphs, lists, quotes, notes, links).
- **diagram** — vector graphics content. TBD: SVG-like or a hexdown-native vector representation.
- **photo** — raster image content. TBD: bitmap or 2D splat representation.

TBD — full per-trellis definitions: valid stem and blossom node kinds, nesting rules, link-slot patterns.

## Taproot trellis (draft)

Every document has exactly one taproot card, and every taproot card uses the **taproot** trellis. This makes document-level metadata uniformly readable across any orchard regardless of which arbor governs the document's content.

A taproot face contains:

- **arbor ref** — name (or hash) of the arbor governing this document's macrostructure (e.g., `report`, `job-ticket`, `message`)
- **meta** — an optional sequence of (key, value) pairs

Meta entries are (neem-key, phrase-value): keys are identifier-shaped neems, values are phrases that may contain any mix of blossoms (neem, prop, quant, enum, uniglyph) and stems (span).

Conventional keys (recommended by arbors but not required by the trellis):

- `title` — phrase containing the document's title, for documents that have one
- `created-at` — quant timestamp
- `contributors` — phrase of handles (typically props)
- `status` — enum, for documents that have lifecycle state

Arbors are free to recommend additional keys. The trellis itself imposes no required keys beyond the arbor ref.

Note on summaries / appendices: these are not taproot meta. If a document needs a human-readable summary or appendix, that is a separate card in the document's tree under whatever position the arbor specifies. Cross-document search by similarity is served by the taproot card's embedding vector, which encompasses the whole document subtree — no summary field is needed for that purpose.

TBD — exact sip encoding of the arbor-ref field; whether arbor-refs are by name, by hash, or both.

## Passage trellis (draft)

From the initial vision document:

- **list** — sequence of (neem, quant, code, handle, socket, wiki, hyper, quote, figure) elements
- **point** — single (neem, quant, mark, handle, socket, wiki, hyper, quote, figure) element
- **phrase** — sequence of (neem, quant, mark, handle, socket, note, wiki, hyper, figure) elements
- **statement** — sequence containing phrases and other inline elements
- **question** — sequence containing phrases and other inline elements
- **quote** — mark + offset + breadth
- **note** — sequence containing phrases, statements, questions, quotes
- **paragraph** — sequence containing phrases, statements, questions, notes, quotes, lists, points

TBD — formal definitions of each node kind, the legal nesting, and how each maps to CommonMark constructs.

## Report arbor (draft)

From the initial vision document:

- **banner** — title + slug
- **section** — banner + (paragraph)
- **chapter** — banner + (section)
- **book** — banner + (chapter)
- **taproot** — mark + title(neem) + slug + contributors(handle) + (book, chapter, section)

TBD — formal definition with positions, valid trellises per position, and head/body sections.

## Open questions

- Whether "graph" and "table" arbors are first-class core arbors or extensions
- How to handle arbors that need to embed each other (e.g., a report containing a table)
- Whether trellises can be parameterized by arbor (i.e., is "passage" exactly the same within "report" and within "job-ticket"?)
