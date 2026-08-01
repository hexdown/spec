# Data Model

The hexdown data model describes an orchard as a layered structure: an orchard contains plots, plots contain cards, and each card is a (face, back) pair — the face stored and content-addressed, the back a projection distilled from the delta log ([deltas.md](deltas.md)). Documents are derived: a document is the closure of a taproot card plus all cards reachable through child references on backs.

This document covers the layers, card identity and its byte-aligned projections, the classes of card and of document node, and the working scale of a card. The kinds within each node class are cataloged in [flora.md](flora.md); the shipped arbors are described in [schemas.md](schemas.md); how cards persist is [store.md](store.md). See [glossary.md](glossary.md) for one-paragraph definitions of each term.

## Layering

```
orchard               (one per hexdown installation; the corpus)
└── plot              (grouped by arbor + intent; gardener permissions attach here)
    └── card          (face + back)
        ├── face: tree of document nodes (sip stream)
        └── back: face bloom + child rings (a projection of the log)
```

Documents are not a separate stored layer. A *document* is the closure of (taproot card + descendants reachable through `back.children`); a card is a taproot when its schema is a taproot schema. Listing all taproot cards in a plot enumerates the plot's documents.

## Card identity

Every card is named by a 60-bit **stead ring** — ten petals, split 6 + 4, so rings serialize as ordinary blossoms and still fit a 64-bit word with four bits spare (sips all the way down, decided 2026-07-20). A stead is a place something stands enduringly: a stead ring is *held* — grafts point by it, and revising a card never disturbs it (ratified 2026-08-01; see the ring entry in [glossary.md](glossary.md)):

| field   | bits | petals | range                                        |
|:--------|:----:|:----:|:---------------------------------------------|
| trunk   |  36  |  6   | which document — up to ~68 billion per orchard |
| step    |  24  |  4   | the growth step — up to ~16 million cards per document |

The trunk is monotonically issued by the orchard. **Document 0 is the orchard's own document** (decided 2026-07-20): its taproot at (0, 0) is the orchard's self-description, written in the orchard's own medium — orientation for gardeners, not configuration for parsers (the machinery bootstraps from hardcoded table names plus replay; [store.md](store.md)). Document 0's step space is also where the orchard mints rings for infrastructure: plot rings and schema lineages are replay-assigned from its counter in log order ([deltas.md](deltas.md)). User documents are issued starting at trunk `1`.

Within a document, steps count up from `0`. **The taproot of a document always has `step = 0`** — this is a structural rule of the spec, not just a convention. Consequences:

- Looking up a card's taproot requires no I/O: `(trunk, 0)`.
- Listing all cards in a document is a range scan over the ring space `[trunk << 24, (trunk + 1) << 24)`.
- The taproot's ring and the document's id are the same value.

## Capacity and the archive-and-fork model

The 36 + 24 bit split sizes a single orchard for human-generation scale, not for indefinite growth. At one million new documents per day, the 36-bit trunk space lasts approximately 190 years; at one thousand new documents per day, ~190,000 years. Communities whose orchards approach the trunk limit are expected to **archive and fork**: the existing orchard becomes a read-only archive, and a new orchard is created for ongoing work. The spec deliberately does not try to scale a single orchard beyond this horizon — long-term continuity is achieved by chains of orchards rather than by ever-larger rings.

## Stamp rings

Every delta record — till and flush alike — is keyed by a 60-bit **stamp ring**: ten petals, split 6 + 4 like stead rings, packed as a stamp + counter (each log keeps its own ring space; [deltas.md](deltas.md) gives the merge order). A stamp ring is *laid down* — one mark per event, permanent in the log but never held as a living referent:

| field   | bits | petals | range                                |
|:--------|:----:|:----:|:-------------------------------------|
| stamp   |  36  |  6   | unsigned seconds since the unix epoch (1970-01-01; runs to ~year 4147) |
| counter |  24  |  4   | up to ~16M records/second            |

The stamp is the orchard's clock, not the world's: it timestamps orchard *events*, which cannot precede the orchard, so it anchors at 1970 and runs forward — unsigned, keeping raw value order equal to time order. Dates *described by* content (publication years, historical time, eras before 1970) are content, carried by quant layouts in faces (decided 2026-07-20).

Delta records are globally time-ordered across all gardeners in an orchard. The counter rolls per second, so collisions are only possible at more than ~16 million records per second — well beyond any plausible orchard write rate.

## Byte-aligned projections

Sips are the authoritative representation of every hexdown value. For use as keys in byte-keyed substrates, rings and blooms also have **canonical byte-aligned projections** (decided 2026-08-01):

- a **ring** projects to a big-endian unsigned 64-bit integer — eight bytes, the four spare bits leading as zeros. Byte order equals value order, so a document's cards are a contiguous key range and a log's records scan in time order.
- a **bloom** (64 petals, 384 bits) projects to exactly its blake2b-384 digest — the projection and the hash coincide by construction: 48 bytes, nothing to pad.

Projections are for keys; names are for sips. Where a key is spelled as text — a filename, an inspector view — the canonical spelling is **octal, two digits per sip**: six bits per sip and three bits per octal digit divide evenly, so every sip boundary stays visible (hex would smear sips across half-digits, and base64 does not survive case-insensitive filesystems). A ring spells as twelve digits, a hyphen, eight digits — `015230603215-00000000` is counter 0 in the second 1784874637 — and a bloom spells as 128 unbroken digits. See [store.md](store.md) for the tables these keys serve.

## Card structure

A card's back is a **projection, not a record** (decided 2026-07-20): the last shoot at the card's ring, verbatim —

- the **face bloom** — content hash of the card's current face
- the **child rings** — ordered stead rings filling the graft slots in the face, one per graft (leaf cards carry none)

Everything the earlier six-field back stored is either arithmetic or a sibling projection: a card's document is its ring's trunk; its document's plot is bound by the sow at the taproot; and the schema governing its face is named by the face's own schema node, with lineage staking distilled into the registry ([deltas.md](deltas.md)).

A card's face is a tree of document nodes encoded as a flat sip stream. Its tree shape is parseable by any hexdown parser without a schema (the metastructure in [encoding.md](encoding.md)); its meaning is interpreted under the schema named by content hash in the face's own schema node. In a branch card the face is a bough over grafts; in a leaf card the face is stems and blossoms over petals. See "Node classes" below for the classes and [flora.md](flora.md) for the kinds.

The document's arbor is anchored once per document, at the taproot: the taproot face's schema node names its schema by content hash, and the arbor is that schema plus the closure of schemas it references. Non-taproot cards locate the anchor by jumping to the taproot at `(card.document_id, 0)`. Every schema in the closure is itself a hexdown card, so arbors are first-class data stored within the orchard — and content-addressed by construction, the closure forming a Merkle grove.

## Card classes

Hexdown distinguishes three classes of card by role. Each card class uses a particular flavor of schema, and each flavor of schema produces faces with a particular node structure.

| Card class | Schema flavor     | Face structure                            | Holds rendered content? |
|:-----------|:------------------|:------------------------------------------|:------------------------|
| taproot    | taproot (branch)  | bough rooted at the taproot kind          | no                      |
| branch     | branch schema     | bough with grafts                         | no                      |
| leaf       | leaf schema       | stems and blossoms over petals            | yes                     |

- **branch schema** — produces branch cards. Face structure: a bough rooted at the schema's specific kind (e.g., `book` bough for a book card under the prose arbor). Children are grafts; the back resolves each to a child stead ring.
- **leaf schema** — produces leaf cards. Face structure: a tree of stems and blossoms over petals, rooted at whatever card-root kind the schema specifies.

The **taproot schema** is a particular branch schema — every document has one taproot card, and that card uses this schema.

The discipline this expresses: **rendered content lives only in leaf cards.** Branch cards (including the taproot) organize the tree of cards but carry no content of their own; their faces are uniformly boughs over grafts. Leaf cards carry actual content — text, diagrams, photos — under whichever leaf schema the arbor specifies at their position.

## Node classes

Document content is represented as a tree of nodes within each card's face. The node classes are:

- **petal** — a single sip at a leaf position; every leaf of a face is a petal. Petals live only inside blossom-family nodes
- **blossom** — inner node whose direct children are petals; the smallest named word-scale content unit (content blossoms appear only in leaf cards)
- **graft** — a blossom-family node inside a bough, holding exactly one petal that names the kind of the child card grafted at that position
- **stem** — inner node whose direct children are nodes — blossoms or other stems; combines word-scale units into larger structures (appears only in leaf cards)
- **bough** — top-level inner node of a branch card's face; its direct children are grafts (declared only by branch schemas)

Structurally, these map onto the metastructure's four kind families: stems are stem-family (children are nodes), boughs are bough-family (children must all be grafts — a bough wears its family on its sleeve), blossoms and grafts are blossom-family (children are petals), and the pad is the null kind. "Branch" is a card-level word: branch *cards* have bough-family roots. Every node begins with a kind sip whose leading bits say which family it belongs to, followed by a count sip; see the metastructure in [encoding.md](encoding.md) and the full table in [glyphs.md](glyphs.md).

The class a card's face inhabits depends on its schema flavor. **Branch schemas** produce faces rooted in a bough, whose children are grafts naming child card kinds — branch card faces hold no rendered content. **Leaf schemas** produce faces rooted in a stem or blossom over petals — leaf cards hold the actual rendered material.

The discipline this expresses: **no bough nodes on leaf cards**. A branch card can graft to a stem (the card root of the leaf card the bough's graft references), but the body of any stem — and the blossoms and petals it contains — lives only on leaf cards.

The families give every face the same silhouette: branch faces bottom out in grafts, leaf faces bottom out in content blossoms, and every face opens with a schema node whose first child is a bloom.

### Petals

A petal is a single sip at a leaf position inside a blossom-family node — a content blossom, a graft, a bloom, or a neem. Petals carry the rendered content of leaf cards; a branch card's only petals are the reference petals inside its grafts and the hash petals of its schema node's bloom. A petal's *meaning* is determined by its parent's kind — content interpretations are cataloged with the blossom kinds in [flora.md](flora.md), and the sip → phoneme/glyph mapping lives in [encoding.md](encoding.md).

### Grafts

A graft is a blossom-family node inside a bough — the site where a child card is joined to its parent branch card. Each graft holds exactly one petal, whose value names the kind of the child card grafted at that position, in the enclosing schema's vocabulary. The card's back resolves each graft, in face order, to a child stead ring via its ordered child rings.

Grafts are the structural counterpart of content blossoms: leaf faces bottom out in blossoms full of rendered petals; branch faces bottom out in grafts, each holding a single petal of reference. The typed petal lets a document's skeleton be walked — and checked against each child card's own declared schema — without loading the children.

The plant-grafting metaphor: the bough is the rootstock (the receiving structure), the child card's card root is the scion (the thing joined in), and the graft itself is the join site — the slot in the bough naming the kind of the card grafted there.

### Boughs

A bough is the top-level inner node of a branch card's face. Its direct children are grafts, each naming the kind of child card at its position. The card's back resolves each graft, in face order, into a stead ring via its ordered child rings.

Boughs are declared only by branch schemas (taproot, book, chapter, section, etc.). Their function is to enumerate which child cards exist and what each one is for, while delegating the actual rendered content to those child cards. This is the discipline that keeps branch cards free of rendered content.

The taproot schema describes a specific kind of bough — its graft kinds include the meta cards (at, dex, status, ...) plus the single body root (omnibus | book | chapter | section, depending on document size).

### Card roots and back roots

- **card root** — the content entry node of a card's face: the second child of its schema node. In branch cards the card root is a bough; in leaf cards it is a stem or blossom appropriate to the schema. The **document root** is specifically the card root of the document's taproot card.
- **back-root** — the entry node of a card's back; the top of the back's metaschema record.

## Card scale (draft)

How much content wants to live on one card? The commitments pull in both directions: small enough to be individually addressable and to participate in many documents at once, large enough to carry meaningful structure within itself ([vision.md](vision.md)).

Working figures, under measurement:

- storage substrates cap a stored face — the store caps a value at 1920 sips (~1440 bytes; [store.md](store.md))
- an english word costs roughly 7-8 sips (petals plus blossom and stem overhead), giving a ceiling of roughly **250 words per leaf card**
- current lean: **one block per leaf card** — a single paragraph, verse, or list — which also gives splice and reference their natural grain

The decision is deliberately parked until the chapter-4 hand-annotation yields real sip counts per paragraph (see [design/speech-examples.md](design/speech-examples.md); tracked in [design/corpus-demands.md](design/corpus-demands.md)). The corpus tells us how big its cards want to be.

## Arbors and schemas

An arbor is the taproot's schema plus its closure (2026-07-19; *schema* is the headword — *trellis* retired 2026-07-20). Branch schemas list position kinds, each naming the schema of its grafted cards by content hash — so the closure is content-addressed by construction.

Versions and lineages are distinct names (decided 2026-07-20): a **bloom names a version** — immutable, exact, what faces speak — while a **ring names a lineage** — what the delta log speaks. A till **stakes** a lineage's ring to its current taproot bloom, and re-staking advances it; an interior fix re-seals the chain above it, so the taproot bloom versions the entire arbor and the registry projection needs only taproot lineages ([deltas.md](deltas.md)).

The shipped set is described in [schemas.md](schemas.md); the worked mary frances set is in [design/mary-frances-schemas.md](design/mary-frances-schemas.md).

## Open questions

- ~~Should the back also project a parent ring?~~ **decided 2026-08-01: no** — backs stay minimal and elegant; parents are discovered by walking the tree or by an ephemeral index.
- Deletion in an append-only orchard: the emerging direction (2026-08-01) is that **the only deletion is demotion** — an orchard holds a raw scratch level where everything lands, documents and cards are *promoted* to layers of higher visibility and durability, and demotion moves them back down; forgetting happens at archive-and-fork time, when *compaction* chooses what the next orchard carries forward (see the lifecycle question in [store.md](store.md)).
- Interior schema lineages: deferred until cross-arbor sharing wants to name a shared schema's evolution independently of any taproot ([design/genesis.md](design/genesis.md)).
- What grows under (0, 0) beyond the orchard's readme — grafts to notable documents? a tour of plots? (Whatever gardeners plant there; it is a document, not a format.)
- ~~Sips all the way down?~~ **decided 2026-07-20: yes** — both ring species are 60 bits = ten petals, split 6 + 4, serializing as ordinary blossoms (tables above); the stamp anchors at the unix epoch, unsigned.
- ~~Two coordinate systems (face hash vs stable id)?~~ **decided 2026-07-20**: bloom names a version, ring names a lineage — faces speak blooms, the log speaks rings, and a back is the distilled join ([deltas.md](deltas.md)).
