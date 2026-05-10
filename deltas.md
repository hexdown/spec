# Deltas

Hexdown represents change as deltas applied to an orchard. The delta history of an orchard is append-only — every delta is a first-class, immutable object. Backs and other derived structures (indices, embedding caches) are *ephemeral projections* of the delta history; they are updated in place as deltas are applied but are never the source of truth. Any past state of the orchard is reachable by walking the delta history.

Two kinds of delta:

- **till** — structural changes to the orchard, its plots, and its arbors
- **flush** — content changes to cards (face and back)

## Till deltas

TBD. Candidate till operations:

- create a plot in the orchard
- publish a new arbor
- publish a new version of an existing arbor
- grant or revoke a gardener's permission on a plot
- archive a plot

Till deltas mutate the structure that documents live within, but do not mutate the content of documents themselves.

## Flush deltas

TBD. Candidate flush operations:

- splice document nodes into a card's face
- replace document nodes in a card's face
- remove document nodes from a card's face
- update the child-card references on a card's back
- create a new card

Flushes are the authoritative record of card changes. A card's back is an ephemeral projection of the flush history — it represents the *current* state of that card, updated in place when a flush is applied, but it is not the source of truth. Any past state of a card is reconstructible by replaying flushes from the current back backward.

This implies a discipline: **every flush records both the new and the prior values of every back field that changes**. A flush that doesn't capture the full before-and-after for its mutations would not be a complete record, and the orchard could not be reconstructed from incomplete flushes. The spec requires flushes to be lossless in this sense.

Faces, by contrast, are content-addressed. When a flush changes a card's face, the new face is stored under a new hash alongside the prior face; faces are never mutated in place. Whether prior faces are eventually garbage-collected once no current back references them is a separate question (see open questions).

## Delta tree

TBD — the structure of the delta tree itself:

- whether deltas form a flat per-gardener timeline or a branched tree
- whether the delta tree is orchard-wide or per-plot
- how concurrent edits from multiple gardeners merge
- how undo and history navigation map onto the delta tree

## Open questions

- How fine-grained should flush deltas be — one delta per splice, or one delta per session?
- How do till deltas interact with cards that conform to the modified arbor?
- Is there a third kind of delta for embedding-space operations (registering a space, indexing a card)?
- Periodic immutable back snapshots as a performance optimization for deep history queries — flushes alone suffice for reconstruction but may be slow if a card has thousands of prior flushes.
- Face garbage collection: when (if ever) is a face removed once no current back references it? An orchard archive needs to preserve every face ever referenced by any back ever in flush history.
