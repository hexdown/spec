# delta schemas — tills and flushes as pinned cards

how are till and flush records encoded — new metastructure, or schemas of their own? worked out 2026-07-20 (evening), continuing [genesis.md](genesis.md). the finding: neither new reserved kinds nor orchard-registered lineages — the metastructure and parser we already have are enough. a sketch for markup; the hand-encoding of an actual genesis follows it.

## the finding: three grounded levels, no loop

the metastructure stays semantic-free (its power is its smallness), and a registered delta schema would loop (you would need to replay the log to learn how to parse the log). but the design already holds the pattern for "hardcoded and known by heart" — the metaschema — and the delta schemas turn out to be *expressible as ordinary schema cards*, so they need no special marking at all:

1. **the metaschema** — hardcoded grammar; schema cards mark themselves with the null hash
2. **the till and flush schemas** — ordinary schema cards living *in implementation code* (the seam ruling's contrapositive: corpus schemas live with their corpus, protocol schemas live in the library), sealed once at genesis and seeded as faces into the orchard. **coded, not staked**: their authority comes from the spec's vocabulary, never from the log. the spec publishes reference blooms as *test fixtures* (card3-golden lineage) — pinning as regression testing, not protocol constant (philetus, 2026-07-20: compute at genesis rather than pin bytes)
3. **delta records** — faces whose schema nodes carry those genesis blooms. resolution meets no loop and needs no constants: a record's bloom resolves through the hash-keyed `faces` table (reading a face by hash requires no replay — the faces table is not the log), and the schema face parses under the hardcoded metaschema. replay *behavior* dispatches on kind **names** — the renderer-rulebook pattern — so unknown kinds skip gracefully and the delta vocabulary can grow without stranding older parsers

implementations ship level 2 the way they ship the glyph table, and may verify the seeded faces against what they know at open — the metaschema's own both-stored-and-known pattern.

## the verbs (2026-07-20)

- **tills** (create presuppositions): **found** — the orchard, by name; **plot** — break ground on a plot (already the glossary's verb); **stake** — tie a schema lineage's ring to its current taproot bloom, re-staking to advance it. in the garden a stake is both the support a growing plant is tied to and the marker naming what is planted where; both resonances intended. (gardener-welcoming tills: future work, unsketched.)
- **flushes** (operate within documents): **sow** — a new taproot (the glossary's verb); **shoot** — growth at a ring: the first shoot at a fresh ring births a card, a later shoot at the same ring replaces its face — one record kind with upsert semantics, creation-vs-revision falling out of log order (philetus, 2026-07-20). **splice** is reserved as the *api* verb — the authoring operation that edits a face-tree and emits shoots. record kinds and api verbs are different vocabularies.
- **ring** — the ten-petal id blossom, replacing the placeholder *ident*. every hexdown id is a time-and-place of creation: document-id names the trunk, local-id the growth step; a stamp is season + order-within-second. trees record time in their bodies as rings, and dendrochronology reads history from place-in-sequence — which is what replay does with the log. one ring layout serves both id spaces. (ratified 2026-07-20: *ring* replaced *card-id*, *flush-id*, and *stamp-id* as the glossary headword — **card rings** and **stamp rings**.)

## the schemas

one act per record; the act's kind wears the crown. in the working dataclass idiom:

```python
TILL = Schema(
    name="till",
    crowns=("found", "plot", "stake"),
    kinds=(
        Kind("found", Kids(("neem",))),             # the orchard's name
        Kind("plot", Kids(("neem",))),              # break ground: plot name
        Kind("stake", Kids(("ring", "bloom"))),     # lineage ring + taproot bloom
        Kind("ring", Layout(layouts.RING)),         # ten petals, a 6+4 id
    ),
)

FLUSH = Schema(
    name="flush",
    crowns=("sow", "shoot"),
    kinds=(
        Kind("sow", Kids(("ring", "bloom"))),       # taproot ring, plot ring, face bloom
        Kind("shoot", Kids(("ring", "bloom"))),     # card ring, face bloom, child ring*
        Kind("ring", Layout(layouts.RING)),
    ),
)
```

- the metaschema needed **zero extension** for the record bodies — `bloom` and `neem` are reserved and nameable in any kids list. the only stretch is the **ring layout** (ten raw petals), which is the spec event the phase-4 direction already promised ("ten-petal id blossoms")
- children are positional, identification first: a sow reads *taproot ring, plot ring, face bloom*; a shoot reads *card ring, face bloom*, then the card's **child rings in graft order** — the ordered list a back needs to resolve the face's grafts (rings cannot be inferred from adjacent log records, so the shoot carries them). the back projection is the last shoot per ring, verbatim: the back-record encoding phase 4 promised arrives here naturally
- **revision climbs nowhere**: grafts resolve by ring and rings are stable, so replacing a card's face (a shoot at its same ring) touches no ancestor. documents are ring-linked; schemas are bloom-linked, so a schema fix re-seals its chain and re-stakes the taproot. the version/lineage split expressing itself structurally: schema identity is content, card identity is lineage — each reference propagates exactly as much as its meaning requires
- shooting-by-replacement settles **pad-under-a-bough** toward the strict ruling for good: adding a graft is a new face at the same ring, so awaiting slots are never needed
- **delta faces are stamp-keyed, not hash-keyed** — deltas are events, not values (two identical sows at different moments are different events). so delta slurps need **no collision arena**: leading pads only ever existed to dodge hash collisions. the log's slurps are simpler than content slurps, for free
- **faces are inert values; deltas are the refs that make them live** — writing a face is not itself delta-tracked (the git-object pattern). the sow references the readme face by bloom; nothing logs "face written"

## genesis for the mary frances orchard — the hand-encoding worksheet

the records to hand-spell, in log order (the exercise that tests whether the schemas above really hold):

| # | record | says |
|:--|:--|:--|
| 1 | till: found | the orchard's name (neem words) |
| 2–5 | till: plot ×4 | `plots`, `schemas`, `gardeners`, `prose` |
| 6 | till: stake | the prose lineage's ring — (0, 1)? — to the mary frances taproot bloom (already pinned in hwatu's tests) |
| 7 | flush: sow | taproot ring (0, 0), plot ring, and the readme passage's face bloom |

faces seeded alongside: the six mary frances schemas + the till and flush schema faces + the (0, 0) readme passage — **nine faces** (genesis.md's seven grows by the two delta schemas). replay of seven records reconstructs the orchard: four plots, one staked lineage, one document, allocator at 1.

## open questions

- explicit vs replay-assigned ids for found and plot records: lean replay-assigns — the log's order is already load-bearing, and a plot's ring falls out of its position in the log
- whether one-act-per-record survives multi-card operations (a sow that also shoots the document's first branch?)
- unreferenced faces: since face-writing is not delta-tracked, orphaned faces can accumulate; whether they ever want garbage collection is future work
