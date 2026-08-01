# Deltas

Hexdown represents change as two append-only logs of **delta records**. Everything durable in an orchard is faces plus these logs; everything mutable — backs, the plot table, the schema registry, the id allocator — is an ephemeral projection distilled by replaying them ([data-model.md](data-model.md) describes the lean back). The log is the history: any past state of the orchard is a replay of a prefix, revert is an append, and no record is ever rewritten.

Two kinds of delta:

- **till** — creates presuppositions. Its referent is the orchard: founding it, breaking ground on plots, staking schema lineages to their taproot blooms, and (future, unsketched) welcoming gardeners and granting permissions.
- **flush** — operates within documents. It presupposes what tills create: a ring space, a plot, a schema to parse under. Flushes sow taproots and shoot growth at cards.

The first record in any orchard is therefore a till — its founding.

## Order and replay

Both logs are keyed by stamp rings and **totally ordered**: replay merges them by stamp, tills before flushes on equal stamps (presuppositions precede operations). There are no head pointers to maintain — the head of a log is its last record. Consequences:

- **Revert is an append** — a new record stating the restored state; history never rewrites.
- **Navigation is a projection** — per-document chains, per-lineage schema histories, and corpus-wide views are lenses built during replay, not structure stored in the log. One durable shape, many lenses.
- **Merging is out of scope** — a single orchard's log is linear. DAGs arrive only if orchards merge, and the archive-and-fork stance says they don't ([data-model.md](data-model.md)).

Replay dispatches on kind *names*, and unknown kinds skip gracefully — the delta vocabulary can grow without stranding older parsers.

## Three grounded levels

Delta records are ordinary faces — no new metastructure, no reserved kinds. The grounding avoids circularity (you must not need to replay the log to learn how to parse the log):

1. **The metaschema** — hardcoded grammar, known by heart by every parser; schema cards mark themselves with the null hash.
2. **The till and flush schemas** — ordinary schema cards living in implementation code (corpus schemas live with their corpus; protocol schemas live in the library), sealed once at genesis and seeded as faces into the orchard. **Coded, not staked**: their authority comes from the spec's vocabulary, never from the log. Published reference blooms are regression fixtures, not protocol constants.
3. **Delta records** — faces whose schema nodes carry those genesis blooms. Resolution meets no loop: a bloom resolves through the hash-keyed `faces` table (not the log), and the schema face parses under the hardcoded metaschema.

Delta records are *events keyed by stamp rings*, not values keyed by hash — two identical sows at different moments are different events. Their slurps therefore carry **no collision arena**: leading pads exist only to dodge hash collisions ([store.md](store.md)).

## Record kinds

One act per record; the act's kind wears the crown. Children are positional, identification first:

| log | kind | children |
|:--|:--|:--|
| till | **found** | the orchard's name (neem words) |
| till | **plot** | the plot's name (its ring is replay-assigned from document 0's counter) |
| till | **stake** | lineage ring, taproot bloom |
| flush | **sow** | taproot ring, plot ring, face bloom, then the taproot's child rings |
| flush | **shoot** | stead ring, face bloom, then child rings in graft order |

- **found** opens the orchard and names it.
- **plot** breaks ground: a plot is a name in the log and a projection thereafter.
- **stake** ties a schema lineage's ring to its current taproot bloom; re-staking advances the lineage. In the garden a stake is both the support a growing plant is tied to and the marker naming what is planted where — both meant.
- **sow** plants a document: its taproot ring, the plot it belongs to, its face, and the taproot's child rings.
- **shoot** records growth at a ring, with upsert semantics: the first shoot at a fresh ring births a card, a later shoot at the same ring replaces its face — creation versus revision falls out of log order. The back projection is the last shoot per ring, verbatim.

**Child rings pair 1:1 with grafts, and with nothing else.** Stem children are the card's own body, not references, so they never consume rings; a leaf card's record carries no child rings. Since the graft count is derivable from the face, replay validates every record against its face structurally — ring-list length must equal graft count.

## Api verbs and record kinds

Different vocabularies. **Splice** — the authoring operation that edits a face tree — is an api verb; it *emits* shoots. The record kinds above are the at-rest truth; the api verbs are ergonomics over them (see the Operations section of [glossary.md](glossary.md)).

## Faces are inert; deltas are the refs

The git-object pattern: faces are content-addressed values with no independent liveness, and deltas are the references that make them live. Writing a face is not itself delta-tracked — the sow or shoot that references its bloom is the event. Orphaned faces can therefore accumulate (see open questions).

## Revision propagation

- **Documents are ring-linked.** Grafts resolve by ring and rings are stable, so replacing a card's face — a shoot at its same ring — touches no ancestor. Revision climbs nowhere.
- **Schemas are bloom-linked.** Position kinds name child schemas by content hash, so an interior fix re-seals the chain above it and re-stakes the taproot. Revision always climbs.

This is the version/lineage split expressing itself structurally: schema identity is content, card identity is lineage — each reference propagates exactly as much as its meaning requires. **Bloom names a version; ring names a lineage.** Faces speak only blooms; the log speaks lineages; backs are the distilled join.

## Projections

Replay distills the mutable state:

- **backs** — the last shoot per ring: face bloom plus child rings
- **the plot table** — plot ring → name
- **the schema registry** — lineage ring → current taproot bloom; taproot lineages only, since the taproot bloom versions the whole arbor
- **the id allocator** — every record is in the log, so next-document and next-step fall out of the same replay

## Open questions

- Rollback ergonomics: revert-by-append is the mechanism; what a reverting record carries (full restored state vs a reference to a prior record) is unspecified.
- One act per record: does it survive multi-card operations (a sow that also shoots the document's first branch)?
- Gardener-welcoming tills (identity, permissions): unsketched.
- Orphaned faces: an orchard archive must preserve every face any record ever referenced; whether never-referenced faces are ever collected is future work.
- Embedding-space deltas (registering a space, indexing a card): still speculative.
- Deep history at scale: replay from genesis is the truth; whether long-lived orchards want periodic projection snapshots as a pure optimization.
- Order and replay under real use: the linear total order and the no-merge stance are a working posture, not a settled conviction (philetus, 2026-08-01) — keep implementing, watch how it develops, and revisit whether any of git's DAG complexity is actually needed. Reference point: jj rather than git — stable change ids over evolving commits, plus an operation log, rhyme with rings over blooms plus the delta logs; the hope is that the ring/bloom split keeps hexdown on the simple side of that line.
