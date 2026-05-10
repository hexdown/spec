# Data Model

The hexdown data model describes an orchard as a layered structure: an orchard contains plots, plots contain cards, and each card is a (face, back) pair. Documents are derived: a document is the closure of a taproot card plus all cards reachable through child references on backs.

See [glossary.md](glossary.md) for one-paragraph definitions of each term.

## Layering

```
orchard               (one per hexdown installation; the corpus)
└── plot              (grouped by arbor + intent; gardener permissions attach here)
    └── card          (face + back)
        ├── face: tree of document nodes (sip stream)
        └── back: id, trellis ref, position path, face hash, child card refs
```

Documents are not a separate stored layer. A *document* is the closure of (taproot card + descendants reachable through `back.children`); a card is a taproot when its trellis is the core taproot trellis. Listing all taproot-trellis cards in a plot enumerates the plot's documents.

## Card identity

Every card has a stable 64-bit **card-id**, packed as two fields:

| field         | bits | range                                        |
|:--------------|:----:|:---------------------------------------------|
| document-id   |  40  | up to ~1 trillion documents per orchard      |
| local-id      |  24  | up to ~16 million cards per document         |

The document-id is monotonically issued by the orchard. Document-id `0` is reserved (TBD — likely system / metadata purposes such as the orchard-level arbor and trellis registry). User documents are issued starting at `1`.

Within a document, local-ids count up from `0`. **The taproot of a document always has `local-id = 0`** — this is a structural rule of the spec, not just a convention. Consequences:

- Looking up a card's taproot requires no I/O: `(card.document_id, 0)`.
- Listing all cards in a document is a range scan over the card-id space `[document_id << 24, (document_id + 1) << 24)`.
- The taproot's card-id and the document's id are the same value.

## Capacity and the archive-and-fork model

The 40 + 24 bit split sizes a single orchard for human-generation scale, not for indefinite growth. At one million new documents per day, the 40-bit document-id space lasts approximately 3,000 years; at one thousand new documents per day, ~3 million years. Communities whose orchards approach the document-id limit are expected to **archive and fork**: the existing orchard becomes a read-only archive, and a new orchard is created for ongoing work. The spec deliberately does not try to scale a single orchard beyond this horizon — long-term continuity is achieved by chains of orchards rather than by ever-larger card-ids.

## Flush ids

Every flush has a 64-bit **flush-id**, packed as a stamp + counter (the pentabased shape):

| field   | bits | range                                |
|:--------|:----:|:-------------------------------------|
| stamp   |  40  | seconds since epoch (~35,000 years)  |
| counter |  24  | up to ~16M flushes/second            |

Flushes are globally time-ordered across all gardeners in an orchard. The counter rolls per second so collisions are only possible at more than ~16 million flushes per second, which is well beyond any plausible orchard write rate.

## Card structure

A card's back contains:

- the **card-id** (40 + 24 packed)
- the **trellis ref** the face conforms to (one of the core trellises or an extension trellis)
- the **position path** within the document's arbor (e.g., `book/chapter/section/passage`) — empty for the taproot
- the **face hash** — content hash of the face sip stream
- the **child card refs** — ordered list of card-ids that fill the link slots in the face

A card's face is a tree of document nodes (face-root + branch / stem / blossom subtrees) parsed under the card's trellis and encoded as a flat sip stream.

The document's *arbor* is not stored on every back. It lives on the taproot's face. Non-taproot cards locate the arbor via their card-id: the taproot is `(card.document_id, 0)`.

## Arbors and trellises

TBD — how an arbor lists its valid card positions and the trellises accepted at each position, how arbors evolve over time, whether arbors are content-addressed.

## Open questions

- Are arbors versioned by hash or by name + version?
- Are trellises content-addressed or named?
- Should the back also store a parent card-id, or is parent discoverable only by reverse-child-lookup? (No parent ref keeps the spec tight; reverse lookups can be served by indices.)
- How are deletion / archival of individual documents handled in an append-only orchard?
- Reserved document-id `0`: precise role (system metadata? orchard-level config? arbor and trellis registry?)
