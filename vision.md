# Vision

Hexdown is a specification for representing and managing a corpus of documents in a way that supports collaborative authoring among humans, computational agents, and (in the longer term) other kinds of persons interested in expressing themselves in text. Hexdown's native term for the corpus is the **orchard** — a cultivated space tended by many hands, where documents grow card by card and any card can take root in many trees at once.

The specification is designed around four commitments:

- **Documents as public infrastructure.** An orchard is structured for sharing. Cards are content-addressed, append-only, and freely referenceable across documents. Documents are utterances in a shared space rather than possessions locked behind ownership. An orchard is also self-contained — the arbors, trellises, and references it depends on are stored within it, so it can be archived, copied, or pulled into another system without external services or registries.
- **Equal footing for participants.** The data model places humans and agents on the same footing — both are gardeners interacting with a shared orchard through the same APIs. No participant is privileged over another by the underlying representation.
- **Stable structure under change.** An orchard is an append-only history of deltas. The current state of each card's back — and of any other derived structures — is an ephemeral projection of this history, updated in place as deltas are applied but never the source of truth. Every past state of the orchard remains reachable by walking the delta history; old states are navigable indefinitely.
- **Modest data model.** Hexdown intentionally keeps the data model small even where that means more work for the systems and agents using it. If two documents need to be kept aligned, that's an agent's job; if a card needs to appear across documents, that's an agent's transplant. The spec resists growing features to absorb work that participants are already capable of doing themselves.

## Cards and documents

Hexdown represents each document as a tree of **cards**. A card is the unit of storage and reference — small enough to be individually addressable and to participate in many documents at once, large enough to carry meaningful structure within itself.

Each card has two sides:

- a **face** describing the rendered content of the card as a tree of document nodes, encoded as a flat sequence of 6-bit sips
- a **back** describing the card's stable id, the trellis it conforms to, the content hash of its face, and references to its child cards

The face is what humans read; the back is what the storage layer uses to find, version, and graft cards together.

## Arbors and trellises

An orchard may contain many kinds of documents — reports, job tickets, system requirements, recipes, conversation threads. Each kind of document is described by an **arbor**: a document-level schema defining the valid macrostructure of that document type. The hexdown core ships several built-in arbors and additional arbors can be defined to extend the system.

Within an arbor, each card position references a **trellis** that defines the valid microstructure of that card's face. Trellises are independent of arbors and may be shared across many document types — a "passage" trellis used by both reports and job tickets describes the same kind of content in both contexts. Just as an arbor shapes the macrostructure of a document, a trellis shapes the microstructure of a card.

## Change over time

Hexdown represents change as deltas to an orchard:

- **till** deltas describe structural changes — creating plots, publishing arbors, granting permissions
- **flush** deltas describe content changes — splicing new content into a card, updating its child references

Both kinds of delta are first-class objects in the orchard. Together they describe the full history of the orchard as an append-only tree.

## Relationship to other systems

Hexdown's design draws from a few specific sources:

- **Hypercard** — for the card as a unit of document macrostructure that supports efficient navigation through large documents without requiring a full content scan.
- **Markdown / CommonMark** — for the document-organization semantics. The hexdown document-node model is designed to losslessly represent CommonMark documents so existing markdown corpora can be ingested as hexdown.
- **Pentabased** — the immediate predecessor of hexdown. Pentabased established the content-addressed-screed data model and the kind-glyph-implies-form encoding discipline that hexdown extends with cards, multiple arbors, and a 6-bit glyph alphabet.

## Open questions

- Versioning of arbors and trellises — how an arbor's evolution over time is recorded and referenced from cards.
- Granularity of permissions — whether gardener permissions attach to plots, individual cards, or some combination.
- Distribution model — how orchards share content across machines and trust boundaries; in particular, what affordances exist for pushing cards or documents to another orchard and pulling from another orchard, given that each orchard is self-contained. Pull/push semantics are an open design area (graft-style cross-references, plain copying, syncing protocols, etc.).
