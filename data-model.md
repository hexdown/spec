# Data Model

The hexdown data model describes an orchard as a layered structure: an orchard contains plots, plots contain cards, and each card is a (face, back) pair. Documents are derived: a document is the closure of a taproot card plus all cards reachable through child references on backs.

This document covers the layers, card identity, the classes of card and of document node, and the working scale of a card. The kinds within each node class are cataloged in [flora.md](flora.md); the shipped arbors and trellises are described in [schemas.md](schemas.md). See [glossary.md](glossary.md) for one-paragraph definitions of each term.

## Layering

```
orchard               (one per hexdown installation; the corpus)
└── plot              (grouped by arbor + intent; gardener permissions attach here)
    └── card          (face + back)
        ├── face: tree of document nodes (sip stream)
        └── back: id, trellis ref, face hash, child card refs
```

Documents are not a separate stored layer. A *document* is the closure of (taproot card + descendants reachable through `back.children`); a card is a taproot when its trellis is the core taproot trellis. Listing all taproot-trellis cards in a plot enumerates the plot's documents.

## Card identity

Every card has a stable 60-bit **card-id** — ten petals, split 6 + 4, so ids serialize as ordinary blossoms and still fit a 64-bit word with four bits spare (sips all the way down, decided 2026-07-20):

| field         | bits | petals | range                                        |
|:--------------|:----:|:----:|:---------------------------------------------|
| document-id   |  36  |  6   | up to ~68 billion documents per orchard      |
| local-id      |  24  |  4   | up to ~16 million cards per document         |

The document-id is monotonically issued by the orchard. Document-id `0` is reserved (TBD — likely system / metadata purposes such as the orchard-level arbor and trellis registry). User documents are issued starting at `1`.

Within a document, local-ids count up from `0`. **The taproot of a document always has `local-id = 0`** — this is a structural rule of the spec, not just a convention. Consequences:

- Looking up a card's taproot requires no I/O: `(card.document_id, 0)`.
- Listing all cards in a document is a range scan over the card-id space `[document_id << 24, (document_id + 1) << 24)`.
- The taproot's card-id and the document's id are the same value.

## Capacity and the archive-and-fork model

The 36 + 24 bit split sizes a single orchard for human-generation scale, not for indefinite growth. At one million new documents per day, the 36-bit document-id space lasts approximately 190 years; at one thousand new documents per day, ~190,000 years. Communities whose orchards approach the document-id limit are expected to **archive and fork**: the existing orchard becomes a read-only archive, and a new orchard is created for ongoing work. The spec deliberately does not try to scale a single orchard beyond this horizon — long-term continuity is achieved by chains of orchards rather than by ever-larger card-ids.

## Flush ids

Every flush has a 60-bit **flush-id** — ten petals, split 6 + 4 like card-ids — packed as a stamp + counter:

| field   | bits | petals | range                                |
|:--------|:----:|:----:|:-------------------------------------|
| stamp   |  36  |  6   | unsigned seconds since the unix epoch (1970-01-01; runs to ~year 4147) |
| counter |  24  |  4   | up to ~16M flushes/second            |

The stamp is the orchard's clock, not the world's: it timestamps orchard *events*, which cannot precede the orchard, so it anchors at 1970 and runs forward — unsigned, keeping raw value order equal to time order. Dates *described by* content (publication years, historical time, eras before 1970) are content, carried by quant layouts in faces (decided 2026-07-20).

Flushes are globally time-ordered across all gardeners in an orchard. The counter rolls per second so collisions are only possible at more than ~16 million flushes per second, which is well beyond any plausible orchard write rate.

## Card structure

A card's back contains:

- the **card-id** (36 + 24 packed; ten petals)
- the **trellis ref** the face conforms to (a card-id pointing to the trellis card; or one of the bootstrap trellises by reserved id)
- the **face hash** — content hash of the face sip stream
- the **arbor-ref** — *taproot cards only*; a stable-id index to the document's anchoring schema (the truth is the taproot face's schema bloom and its closure)
- the **child card refs** — ordered list of card-ids that fill the graft slots in the face (boughs in branch cards; faces of leaf cards have no graft slots)

A card's face is a tree of document nodes encoded as a flat sip stream. Its tree shape is parseable by any hexdown parser without a schema (the metastructure in [encoding.md](encoding.md)); its meaning is interpreted under the schema named by content hash in the face's own schema node. In a branch card the face is a bough over grafts; in a leaf card the face is stems and blossoms over petals. See "Node classes" below for the classes and [flora.md](flora.md) for the kinds.

The document's arbor is anchored once per document, at the taproot: the taproot face's schema node names its trellis by content hash, and the arbor is that trellis plus the closure of schemas it references. Non-taproot cards locate the anchor by jumping to the taproot at `(card.document_id, 0)`. Every schema in the closure is itself a hexdown card, so arbors are first-class data stored within the orchard — and content-addressed by construction, the closure forming a Merkle grove.

## Card classes

Hexdown distinguishes three classes of card by role. Each card class uses a particular flavor of trellis, and each flavor of trellis produces faces with a particular node structure.

| Card class | Trellis flavor    | Face structure                            | Holds rendered content? |
|:-----------|:------------------|:------------------------------------------|:------------------------|
| taproot    | taproot (branch)  | bough rooted at the taproot kind          | no                      |
| branch     | branch trellis    | bough with grafts                         | no                      |
| leaf       | leaf trellis      | stems and blossoms over petals            | yes                     |

- **branch trellis** — produces branch cards. Face structure: a bough rooted at the trellis's specific kind (e.g., `book` bough for a book card under the prose arbor). Children are grafts; back resolves each to a child card-id.
- **leaf trellis** — produces leaf cards. Face structure: a tree of stems and blossoms over petals, rooted at whatever card-root kind the trellis specifies.

The **taproot trellis** is a particular branch trellis — every document has one taproot card, and that card uses this trellis.

The discipline this expresses: **rendered content lives only in leaf cards.** Branch cards (including the taproot) organize the tree of cards but carry no content of their own; their faces are uniformly boughs over grafts. Leaf cards carry actual content — text, diagrams, photos — under whichever leaf trellis the arbor specifies at their position.

## Node classes

Document content is represented as a tree of nodes within each card's face. The node classes are:

- **petal** — a single sip at a leaf position; every leaf of a face is a petal. Petals live only inside blossom-family nodes
- **blossom** — inner node whose direct children are petals; the smallest named word-scale content unit (content blossoms appear only in leaf trellises)
- **graft** — a blossom-family node inside a bough, holding exactly one petal that names the kind of the child card grafted at that position
- **stem** — inner node whose direct children are nodes — blossoms or other stems; combines word-scale units into larger structures (appears only in leaf trellises)
- **bough** — top-level inner node of a branch trellis; its direct children are grafts (appears only in branch trellises)

Structurally, these map onto the metastructure's four kind families: stems are stem-family (children are nodes), boughs are bough-family (children must all be grafts — a bough wears its family on its sleeve), blossoms and grafts are blossom-family (children are petals), and the pad is the null kind. "Branch" is a card-level word: branch *cards* have bough-family roots. Every node begins with a kind sip whose leading bits say which family it belongs to, followed by a count sip; see the metastructure in [encoding.md](encoding.md) and the full table in [glyphs.md](glyphs.md).

The class a card's face inhabits depends on its trellis flavor. **Branch trellises** produce faces rooted in a bough, whose children are grafts naming child card kinds — branch card faces hold no rendered content. **Leaf trellises** produce faces rooted in a stem or blossom over petals — leaf cards hold the actual rendered material.

The discipline this expresses: **no bough nodes on leaf cards**. A branch card can graft to a stem (the card root of the leaf card the bough's graft references), but the body of any stem — and the blossoms and petals it contains — lives only on leaf cards.

The families give every face the same silhouette: branch faces bottom out in grafts, leaf faces bottom out in content blossoms, and every face opens with a schema node whose first child is a bloom.

### Petals

A petal is a single sip at a leaf position inside a blossom-family node — a content blossom, a graft, a bloom, or a neem. Petals carry the rendered content of leaf cards; a branch card's only petals are the reference petals inside its grafts and the hash petals of its schema node's bloom. A petal's *meaning* is determined by its parent's kind — content interpretations are cataloged with the blossom kinds in [flora.md](flora.md), and the sip → phoneme/glyph mapping lives in [encoding.md](encoding.md).

### Grafts

A graft is a blossom-family node inside a bough — the site where a child card is joined to its parent branch card. Each graft holds exactly one petal, whose value names the kind of the child card grafted at that position, in the enclosing schema's vocabulary. The card's back resolves each graft, in face order, to a child card-id via its `child-card-refs` list.

Grafts are the structural counterpart of content blossoms: leaf faces bottom out in blossoms full of rendered petals; branch faces bottom out in grafts, each holding a single petal of reference. The typed petal lets a document's skeleton be walked — and checked against each child card's own declared schema — without loading the children.

The plant-grafting metaphor: the bough is the rootstock (the receiving structure), the child card's card root is the scion (the thing joined in), and the graft itself is the join site — the slot in the bough naming the kind of the card grafted there.

### Boughs

A bough is the top-level inner node of a branch trellis. Its direct children are grafts, each naming the kind of child card at its position. The card's back resolves each graft, in face order, into a card-id via the `child-card-refs` list.

Boughs appear only in branch trellises (taproot, book, chapter, section, etc.). Their function is to enumerate which child cards exist and what each one is for, while delegating the actual rendered content to those child cards. This is the discipline that keeps branch cards free of rendered content.

The taproot trellis describes a specific kind of bough — its graft kinds include the meta cards (at, dex, status, ...) plus the single body root (omnibus | book | chapter | section, depending on document size).

### Card roots and back roots

- **card root** — the content entry node of a card's face: the second child of its schema node. In branch cards the card root is a bough; in leaf cards it is a stem or blossom appropriate to the trellis. The **document root** is specifically the card root of the document's taproot card.
- **back-root** — the entry node of a card's back; the top of the back's metaschema record.

## Card scale (draft)

How much content wants to live on one card? The commitments pull in both directions: small enough to be individually addressable and to participate in many documents at once, large enough to carry meaningful structure within itself ([vision.md](vision.md)).

Working figures, under measurement:

- storage substrates cap a stored face — hwatu's store design caps a value at 1920 sips (~1440 bytes)
- an english word costs roughly 7-8 sips (petals plus blossom and stem overhead), giving a ceiling of roughly **250 words per leaf card**
- current lean: **one block per leaf card** — a single paragraph, verse, or list — which also gives splice and reference their natural grain

The decision is deliberately parked until the chapter-4 hand-annotation yields real sip counts per paragraph (see [design/speech-examples.md](design/speech-examples.md); tracked in [design/corpus-demands.md](design/corpus-demands.md)). The corpus tells us how big its cards want to be.

## Arbors and trellises

An arbor is the taproot's trellis plus its schema closure (2026-07-19). Branch trellises list position kinds, each naming the schema of its grafted cards by content hash — so the closure is content-addressed by construction. How schema *versions* are named and succession recorded over time remains open. The shipped set is described in [schemas.md](schemas.md); the worked mary frances set is in [design/mary-frances-schemas.md](design/mary-frances-schemas.md).

## Open questions

- Schema versioning: identity is content hash by construction; how versions are *named* and succession recorded (a till?) remains open.
- Should the back also store a parent card-id, or is parent discoverable only by reverse-child-lookup? (No parent ref keeps the spec tight; reverse lookups can be served by indices.)
- How are deletion / archival of individual documents handled in an append-only orchard?
- Reserved document-id `0`: precise role (system metadata? orchard-level config? arbor and trellis registry?)
- ~~Sips all the way down?~~ **decided 2026-07-20: yes** — both id kinds are 60 bits = ten petals, split 6 + 4, serializing as ordinary blossoms (tables above); the stamp anchors at the unix epoch, unsigned.
- The face's schema node names its governing schema by content hash, while the back carries a `trellis_ref` card-id. Working pattern (2026-07-18, to loop back on): these are two coordinate systems joined by flush history — faces are indexed by content hash, backs give cards stable ids, and each flush concretely re-points a stable id at a new content hash. The full schema-space design (see [encoding.md](encoding.md) open questions) remains open.
