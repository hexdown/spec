# delta schemas — tills and flushes as pinned cards

how are till and flush records encoded — new metastructure, or schemas of their own? worked out 2026-07-20 (evening), continuing [genesis.md](genesis.md). the finding: neither new reserved kinds nor orchard-registered lineages — the metastructure and parser we already have are enough. a sketch for markup; the hand-encoding of an actual genesis follows it.

## the finding: three grounded levels, no loop

the metastructure stays semantic-free (its power is its smallness), and a registered delta schema would loop (you would need to replay the log to learn how to parse the log). but the design already holds the pattern for "hardcoded and known by heart" — the metaschema — and the delta schemas turn out to be *expressible as ordinary schema cards*, so they need no special marking at all:

1. **the metaschema** — hardcoded grammar; schema cards mark themselves with the null hash
2. **the till and flush schemas** — ordinary schema cards whose exact bytes the spec pins (card3-golden lineage), making their blooms **well-known constants**: magic numbers that are self-verifying content addresses. seeded as faces into every orchard for self-containedness — but **pinned, not staked**: their authority comes from the spec, never from the log
3. **delta records** — faces whose schema nodes carry those pinned blooms, parsed like any leaf card

parsers know level 2 the way they know the glyph table; nothing ever needs the log to read the log.

## the verbs (2026-07-20)

- **tills** (create presuppositions): **found** — the orchard, by name; **plot** — break ground on a plot (already the glossary's verb); **stake** — tie a schema lineage's ring to its current taproot bloom, re-staking to advance it. in the garden a stake is both the support a growing plant is tied to and the marker naming what is planted where; both resonances intended. (gardener-welcoming tills: future work, unsketched.)
- **flushes** (operate within documents): **sow** — a new taproot (the glossary's verb); **shoot** — new growth, a new card on an existing document (philetus, 2026-07-20); **splice** — modify a card, deferred to the back-projection design (plan phase 4).
- **ring** — the ten-petal id blossom, replacing the placeholder *ident*. every hexdown id is a time-and-place of creation: document-id names the trunk, local-id the growth step; a stamp is season + order-within-second. trees record time in their bodies as rings, and dendrochronology reads history from place-in-sequence — which is what replay does with the log. one ring layout serves both id spaces. (whether *ring* also replaces *card-id* / *stamp-id* as glossary headwords is open.)

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
        Kind("shoot", Kids(("ring", "bloom"))),     # parent ring, face bloom
        Kind("ring", Layout(layouts.RING)),
    ),
)
```

- the metaschema needed **zero extension** for the record bodies — `bloom` and `neem` are reserved and nameable in any kids list. the only stretch is the **ring layout** (ten raw petals), which is the spec event the phase-4 direction already promised ("ten-petal id blossoms")
- children are positional, identification first: a sow reads *taproot ring, plot ring, face bloom*
- **delta faces are stamp-keyed, not hash-keyed** — deltas are events, not values (two identical sows at different moments are different events). so delta slurps need **no collision arena**: leading pads only ever existed to dodge hash collisions. the log's slurps are simpler than content slurps, for free

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

- **shoot's mechanics**: does shooting fill an *awaiting pad slot* in the parent's bough, or does adding a graft ripple a splice of the parent's face? this is where pad-under-a-bough may finally earn its keep — the strict-pads ruling anticipated exactly this pressure. settle during hand-encoding or when shoot is first exercised
- explicit vs replay-assigned ids for found and plot records: lean replay-assigns — the log's order is already load-bearing, and a plot's ring falls out of its position in the log
- splice's fields — deferred with the back-projection design (phase 4)
- whether one-act-per-record survives multi-card operations (a sow that also shoots the document's first branch?)
- *ring* as glossary headword for the ids themselves, or only as the blossom kind's name
