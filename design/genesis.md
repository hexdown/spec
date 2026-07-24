# genesis — the freshly seeded orchard

what does a brand-new orchard contain, card by card and record by record? working this out (2026-07-20) resolved data-model.md's reserved-document-0 question and produced the design's second big simplification, so that finding leads. this is a sketch for markup; the surviving prose promotes into data-model.md, store.md, and the glossary.

## the finding: opening an orchard is replaying the log

**everything durable is faces + deltas; everything mutable is a projection.** the store's disk tables shrink to three — `faces`, `tills`, `flushes` — and opening an orchard means replaying the delta log to distill the mutable state into ram:

- **backs** — per-card catalog records, distilled from flushes (as long envisioned)
- **the plot table** — a till carries a plot's definition inline; plot-definition cards dissolve
- **the schema registry** — lineage id → current taproot bloom, distilled from tills
- **the id allocator** — every sow is in the log, so next-document-id falls out of the same replay

no `backs/` table on disk, no plot-definition schema needed, no bootstrap configuration record. the north star **everything is a card** stands — deltas are destined to be card-like faces (plan phase 4: sips all the way down) — but the mutable projections were never cards, and now nothing pretends they are.

## bloom names a version; ring names a lineage

the two references have different referents, not redundant ones:

- a **bloom names a version** — immutable, exact. a face's schema node pins the schema *content* it was written under; its meaning can never drift. faces speak only blooms.
- a **ring names a lineage** — "the prose arbor, as it evolves." deltas need a stable name that survives the bloom changing. the log speaks lineages.

a back's schema reference is the distilled answer to "which lineage governs this card" — a projection, not a stored index.

**the registry needs only taproot lineages.** interior schemas are referenced by bloom inside position-kind specs, so any interior change re-seals the chain above it — fix passage and section's spec changes, so section re-blooms, and so on up to the taproot. the taproot's bloom versions the entire arbor; `schema.chain()` is this cascade in code. interior schemas get lineage ids of their own only if cross-arbor sharing someday wants to name them (open below).

**the metaschema needs no card and no lineage.** the null hash is already its universal, hardcoded name; a second name would only shadow a perfect one.

## tills and flushes: presupposition and operation

- a **flush presupposes a document** — a ring space, a plot, a schema to parse under.
- a **till creates presuppositions** — founds the orchard, creates plots, registers and advances schema lineages, grants gardener permissions.

the first record in any orchard is therefore a till; folding the two streams would just reintroduce the split as a special genesis-kind flush. tills do not track state across rings the way flushes do — their referent is the orchard, not a card.

both tables are **totally ordered append-only logs**: stamp rings (stamp + counter) carry the order intrinsically, so there are no head pointers to maintain — the head of a log is its last record. consequences:

- **revert is an append** — a new delta stating the restored state; history never rewrites (this is what append-only durability already promised)
- **navigation is a projection** — per-document delta chains, per-lineage schema histories, corpus-wide views are lenses built during replay, not structure stored in the log. one durable shape, many lenses.
- **merging is out of scope** — a single orchard's log is linear. DAGs arrive only if orchards ever merge, and the archive-and-fork stance says they don't.

## document 0: the orchard's own document

document 0 is real, with its taproot at (0, 0) like every document. but with pointers, plots, schemas, and allocation all handled by the log, what remains for (0, 0) is orientation, not configuration:

**(0, 0) is the orchard's self-description, written in the orchard's own medium** — a prose document naming the orchard and its purpose, growing grafts to notable documents as the orchard grows. a welcome mat for gardeners (human and otherwise), not machinery for parsers. the machinery bootstraps from hardcoded table names plus replay; the orientation bootstraps from a document.

document 0's local-id space is where the orchard mints lineage ids for infrastructure — schema lineages live at (0, n).

## the constellation

a freshly seeded orchard, enumerated (counts illustrative until the till and flush record fields are settled):

| table | records |
|:--|:--|
| `tills` | orchard founded; four plots created (`plots`, `schemas`, `gardeners`, `prose`); the prose-arbor taproot lineage registered at its genesis bloom |
| `flushes` | the sow of document 0 — taproot (0, 0) at its face's bloom |
| `faces` | the six mary frances schema slurps + the (0, 0) readme passage |
| *(ram, after replay)* | plot table, schema registry, backs {(0, 0)}, allocator at next-document-id 1 |

seven faces and a handful of deltas, and the whole orchard reconstructs from them. `hwatu open` could render the readme as its greeting.

## rulings (2026-07-20, philetus)

- **terminology**: *schema* is the headword — it captures precisely what the thing is. *arbor* is kept: it names a distinct referent, the schema closure from a taproot (the full document-type grammar). *trellis* retires until a need for it reappears.
- **bytes on disk** (hwatu-level): the file store holds **raw binary slurp files** — filename carries the key, contents are the canonical bytes, identical to what a kv substrate will hold. the yaml envelope was a ramp we have already leapt past; glyph text is `hwatu inspect` output, never stored truth.
- **provisional delta encoding** (hwatu-level): till and flush records serialize as **json bytes** through the same dumb `(table, key) → bytes` store — stdlib, zero dependencies, human-readable in a pinch. yaml was a holdover from the yaml-store idea. phase 4 re-encodes deltas as card-like faces.
- **phasing**: minimal tills and flushes move into phase 2 — seeding *is* writing the genesis log, and the constellation above is its acceptance test.

## open questions

- the till and flush record fields, exactly — the genesis set above implies founding, plot-creation, lineage-registration, and sow records; spelling their fields is the next concrete step
- rollback ergonomics: revert-by-append is the mechanism, but what a till that "points to the reverted state" carries (full restored state vs reference to a prior delta) is unspecified
- interior schema lineages: deferred until cross-arbor sharing wants to name a shared schema's evolution independently of any taproot
- what grows under (0, 0) beyond the readme passage — grafts to notable documents? a tour of plots? (whatever gardeners plant there; it is a document, not a format)
- whether the `schemas` plot still earns its keep as a *plot* now that schema state is a registry projection — or whether schema faces simply live in `faces` with their lineages in the log. philetus's framing (2026-07-20): a plot is a **gardener-experience idea** — load-bearing for the experience of tending, but how concretely plots need representing is open; possibly no more than a name in a till, a surface for permissions, and a lens over foraging
