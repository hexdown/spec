# Core Arbors and Trellises

Hexdown ships a small set of built-in arbors and trellises that cover common document types. Extension arbors and trellises may be defined to handle additional document types specific to a deployment.

## Core arbors

Document-level schemas shipped with hexdown:

- **report** — book → chapter → section. For long-form prose organized hierarchically.
- **graph** — space → (directed) edge | node. For network-style documents.
- **table** — sheet → column | row → cell. For tabular documents.

TBD — full per-arbor definitions: card positions, valid trellises at each position, arity constraints, head/body splits.

## Core trellises

The hexdown core ships several trellises. The two bootstrap trellises (metatrellis, metarbor) are hardcoded into the parser; everything else is loaded from trellis cards stored in the orchard.

**Bootstrap trellises** (hardcoded; see [metaschema.md](metaschema.md)):

- **metatrellis** — the trellis used by trellis cards.
- **metarbor** — the trellis used by arbor cards.

**Core branch trellises** (faces are boughs over link petals; no rendered content):

- **taproot** — used by every document's entry-point card. See the draft below.
- **omnibus**, **book**, **chapter**, **section** — the report arbor's internal branch levels. Each is a bough whose link children are either a banner (on its head) and a body of next-level branches or leaves. See "Report arbor (draft)" below.

**Core leaf trellises** (faces hold stems, blossoms, and petals):

- **passage** — markdown-equivalent prose content (paragraphs, lists, quotes, notes, links).
- **banner** — title + optional summary; lives on body-root branch cards (the topmost banner of a document is the document's title).
- **at** — contributor handles (typically props).
- **dex** — generic key/value metadata.
- **status** — single-enum lifecycle state.
- **diagram** — vector graphics content. TBD: SVG-like or a hexdown-native vector representation.
- **photo** — raster image content. TBD: bitmap or 2D splat representation.

TBD — full per-trellis definitions: face-root kind, valid child node kinds at each position, nesting rules, link-slot patterns.

## Taproot trellis (draft)

Every document has exactly one taproot card, and every taproot card uses the **taproot** trellis (a branch trellis). The taproot trellis is structurally uniform across documents, so any system scanning an orchard can recognize and enumerate documents regardless of which arbor governs their content.

A taproot face is a bough rooted at the `taproot` kind with two sections:

- **head** — 0-64 link petals, each naming a kind of meta card (at, dex, status, etc.). The kind sip at each position determines the meta card's kind; the back's `child-card-refs` resolves each position to a card-id.
- **body** — exactly one link petal naming the body-root kind (omnibus, book, chapter, section, …, depending on document size). The taproot has *exactly one* body child (inherited from pentabased's tapestry rule).

The arbor governing the document is *not* a meta card linked from the head. It is a card-id stored in a dedicated **arbor-ref** slot on the taproot's *back* (see [data-model.md](data-model.md)), pointing to an arbor card stored elsewhere in the orchard. This keeps the taproot's face purely a tree of cards-in-this-document, while the arbor-ref is a system reference that lives at the catalog layer.

The document's *title* is also not on the taproot. The title lives on the body-root branch card (typically a book or section) as that card's own banner. Sub-titles for nested branches (chapters, sections) live on their respective banner attachments. The topmost banner is the document's title.

Conventional meta cards (recommended by arbors but never required by the trellis):

- **at** — contributors of the document as a whole
- **dex** — generic key/value system metadata (`created-at`, `updated-at`, etc.)
- **status** — single-enum lifecycle state for documents that need it

Arbors may recommend additional meta cards. The taproot trellis itself imposes no required meta — only the body link and the arbor-ref are mandatory.

TBD — concrete kind-sip assignments for each meta card kind in the taproot's bough.

## Bootstrap trellises (sketches)

### metatrellis

The metatrellis describes how a trellis card's face is laid out. A trellis card's face describes one trellis, including:

- face-root kind (the kind sip at the top of any face under this trellis)
- valid child node kinds at each position in the trellis's grammar
- arity / variability rules for each position
- whether the trellis is a branch trellis or a leaf trellis

TBD — concrete sip-level structure.

### metarbor

The metarbor describes how an arbor card's face is laid out. An arbor card's face describes one arbor, including:

- the document's root body kind (which branch trellis the body root uses)
- the tree of valid card positions, each naming a trellis (by card-id)
- arity rules at each position
- recommended meta cards for the taproot

TBD — concrete sip-level structure.

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

The report arbor describes long-form prose organized hierarchically. Its branch hierarchy, from largest to smallest, is:

```
taproot → omnibus → book → chapter → section → passage(s)
```

(`omnibus` is the parallel of pentabased's `opus`; the report arbor inherits pentabased's tape-scheme flexible-root principle.)

Like pentabased's tapestry scheme, the body root can be at any level of the branch hierarchy, depending on document size:

- **taproot → passage** (single leaf — a quick note)
- **taproot → section → passages** (a short report)
- **taproot → chapter → sections → passages**
- **taproot → book → chapters → sections → passages** (full-size report)
- **taproot → omnibus → books → ...** (collected works)

Documents grow by inserting parent branch levels — when a `taproot → section` becomes too big, a new chapter card is created that contains the existing section plus new ones, and the taproot now points to the chapter. Old cards are untouched (append-only).

Each branch level uses its own branch trellis (omnibus, book, chapter, section). Each branch trellis's bough has:

- **head** — optional banner link (title and optional summary for this branch)
- **body** — 1-64 child links of the next branch level (or passages for sections)

The topmost branch's banner is the document's title.

TBD — formal definition with positions, valid trellises per position, kind-sip assignments, and head/body details.

## Open questions

- Whether "graph" and "table" arbors are first-class core arbors or extensions
- How to handle arbors that need to embed each other (e.g., a report containing a table)
- Whether trellises can be parameterized by arbor (i.e., is "passage" exactly the same within "report" and within "job-ticket"?)
