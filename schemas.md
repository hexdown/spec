# Schemas

Hexdown ships a small set of built-in schemas covering common document types; extensions may be defined for additional types specific to a deployment. Hexdown has one grain of schema — the **trellis**, governing a single card's face. Leaf trellises select their node kinds from the [flora](flora.md); branch trellises assign a schema, by content hash, to each card position they graft. An **arbor** is not a second grain: it is the taproot card's trellis together with the closure of schemas reachable from it — the whole grove a document grows under (2026-07-19; worked set in [design/mary-frances-schemas.md](design/mary-frances-schemas.md)).

Arbors classify **shape, not genre**. A children's story, a technical report, and an encyclopedia article all share the prose arbor — nested, titled divisions of prose; their differences live in the mixture of leaf kinds each document uses, in `dex` metadata, and in plot intent. A new arbor is warranted only when the tree shape itself changes (a message stream, a graph, a table) — never merely because the writing style does.

## Core arbors

Taproot-anchored schema closures shipped with hexdown:

- **prose** — book → chapter → section. For long-form writing organized hierarchically: stories, reports, articles, manuals.
- **graph** — space → (directed) edge | node. For network-style documents.
- **table** — sheet → column | row → cell. For tabular documents.

The prose closure is worked card-by-card in [design/mary-frances-schemas.md](design/mary-frances-schemas.md); graph and table remain sketches.

## Core trellises

The hexdown core ships several trellises. The two bootstrap trellises are hardcoded into the parser; everything else is loaded from trellis cards stored in the orchard.

**The metaschema** (hardcoded in parsers; described in [encoding.md](encoding.md)) is the grammar of all schema cards — schema cards mark themselves with the null hash. There are no bootstrap trellis *cards* to list: the metaschema is carried by every parser, and optionally stored as a card any parser can verify against the grammar it knows by heart.

**Core branch trellises** (faces are boughs over grafts; no rendered content):

- **taproot** — used by every document's entry-point card. See the draft below.
- **omnibus**, **book**, **chapter**, **section** — the prose arbor's internal branch levels. Each is a bough whose graft children are an optional leading banner followed by next-level branches or leaves. See "Prose arbor (draft)" below.

**Core leaf trellises** (faces hold stems, blossoms, and petals):

- **passage** — markdown-equivalent prose content (paragraphs, lists, lifts, notes, links).
- **banner** — title + optional summary; lives on body-root branch cards (the topmost banner of a document is the document's title).
- **at** — contributor handles (typically props).
- **dex** — generic key/value metadata.
- **status** — single-enum lifecycle state.
- **diagram** — vector graphics content. TBD: SVG-like or a hexdown-native vector representation.
- **photo** — raster image content. TBD: bitmap or 2D splat representation.

TBD — full per-trellis definitions: card-root kind, valid child node kinds at each position, nesting rules, graft-slot patterns.

## Taproot trellis (draft)

Every document has exactly one taproot card, and every taproot card uses the **taproot** trellis (a branch trellis). The taproot trellis is structurally uniform across documents, so any system scanning an orchard can recognize and enumerate documents regardless of which arbor governs their content.

A taproot face is a bough rooted at the `taproot` kind with 1-64 graft children, constrained by position rather than by named fields:

- the **last** graft names the document's body root (omnibus | book | chapter | section | passage, depending on document size) — every taproot has exactly one body root, and it is always the final child
- the **0-63 preceding** grafts each name a kind of meta card (at, dex, status, etc.)

Front matter first, body last — the same positional idiom the branch trellises use (optional banner first, body children after), and the schema node itself follows (hash bloom first, document root last). The back's `child-card-refs` resolves each graft, in face order, to a card-id.

The arbor is anchored by the taproot's own trellis — named by content hash in its face's schema node, like every card's governor. The back's **arbor-ref** (see [data-model.md](data-model.md)) is a stable-id index for catalog-layer lookups; the face's bloom and its closure are the truth.

The document's *title* is also not on the taproot. The title lives on the body-root branch card (typically a book or section) as that card's own banner. Sub-titles for nested branches (chapters, sections) live on their respective banner attachments. The topmost banner is the document's title.

Conventional meta cards (recommended by arbors but never required by the trellis):

- **at** — contributors of the document as a whole
- **dex** — generic key/value system metadata (`created-at`, `updated-at`, etc.)
- **status** — single-enum lifecycle state for documents that need it

Arbors may recommend additional meta cards. The taproot trellis itself imposes no required meta — only the body graft and the arbor-ref are mandatory.

TBD — concrete kind-sip assignments for each meta card kind in the taproot's bough.

## Passage trellis (draft)

The passage trellis produces markdown-equivalent prose leaves. At block scale it admits: paragraph | list | point | lift | note — kind definitions live in [flora.md](flora.md). Proposed additions from the speech design session (turn, speech, quoth, broken, cut) are worked in [design/speech-examples.md](design/speech-examples.md).

TBD — the formal admission table: legal nesting per position, arity, and head/body structure.

## Prose arbor (draft)

The prose arbor describes long-form writing organized hierarchically. Its branch hierarchy, from largest to smallest, is:

```
taproot → omnibus → book → chapter → section → passage(s)
```

The body root can be at any level of the branch hierarchy, depending on document size:

- **taproot → passage** (single leaf — a quick note)
- **taproot → section → passages** (a short report)
- **taproot → chapter → sections → passages**
- **taproot → book → chapters → sections → passages** (full-size report)
- **taproot → omnibus → books → ...** (collected works)

Documents grow by inserting parent branch levels — when a `taproot → section` becomes too big, a new chapter card is created that contains the existing section plus new ones, and the taproot now points to the chapter. Old cards are untouched (append-only).

Each branch level uses its own branch trellis (omnibus, book, chapter, section), and each bough follows the positional idiom: an optional banner graft at the first position (title and optional summary for this branch), followed by grafts of the next branch level (or passages, for sections), 1-64 children in all.

The topmost branch's banner is the document's title.

The worked schema set — the taproot, book, chapter, and section trellises with their position kinds and bloom references — is in [design/mary-frances-schemas.md](design/mary-frances-schemas.md), absorbing here as it firms.

## Open questions

- Whether "graph" and "table" arbors are first-class core arbors or extensions
- How to handle arbors that need to embed each other (e.g., a report containing a table)
- Whether trellises can be parameterized by arbor (i.e., is "passage" exactly the same within "prose" and within "job-ticket"?)
