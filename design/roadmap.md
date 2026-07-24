# Roadmap

This document describes where the spec currently stands, what concrete milestone it is being driven toward, and how implementation work feeds back into the spec.

## Where the spec currently stands

The spec covers:

- **vision and commitments** — settled in broad strokes (cards as content units, append-only deltas, equal footing for participants, modest data model)
- **data model layering** — orchard → plot → card → (face + back); ring structure (card rings and stamp rings)
- **document node classes** — petal, blossom, stem, bough; their roles in branch vs leaf trellises
- **bootstrap structure** — one hardcoded metaschema; schema cards mark themselves with the null hash; everything else loads dynamically
- **delta categories** — till (structural) and flush (content); deltas as the source of truth and backs as ephemeral projections

Sections still carrying significant TBDs:

- the concrete sip-glyph mapping ([encoding.md](../encoding.md))
- the concrete metaschema record encoding for arbors, trellises, backs, and deltas ([encoding.md](../encoding.md))
- the full trellis and arbor definitions for the core arbors ([schemas.md](../schemas.md))
- the delta tree structure and merge semantics ([deltas.md](../deltas.md))
- the permission and gardener model ([vision.md](../vision.md))

These TBDs are not deferred by accident — they are deferred because we want to resolve them by working through an implementation rather than designing them in the abstract.

## Current path (updated 2026-07-19)

Completed, each step validating the one before: speech-stem rulings ([speech-examples.md](speech-examples.md)) → chapter-4 hand-annotation ([ch4-annotation.md](ch4-annotation.md)) → the passage schema and full mary frances schema set ([passage-schema.md](passage-schema.md), [mary-frances-schemas.md](mary-frances-schemas.md)) → the glyph table → the hand-spelled golden card ([card3-golden.md](card3-golden.md)).

Remaining stones:

1. **the absorption sweep** — the arbor dissolution and metaschema decisions ripple from design/ into the spec proper (schemas.md, glossary, data-model's arbor-ref, encoding's open questions)
2. **hwatu phase 1** — begins with real fixtures: an annotated chapter, six schemas, a glyph table, and a golden card the encoder must reproduce

Every open metaschema question was answered by the corpus rather than by abstract design.

## Implementations

Two implementations of the spec are planned:

- **[hwatu](https://github.com/hexdown/hwatu)** — Python reference implementation. In active development as a prototype intended to validate the spec by building against it.
- **[hanafuda](https://github.com/hexdown/hanafuda)** — Rust production implementation over the redb key-value store. Will follow hwatu once the spec stabilizes.

The two-implementation arc inherits from the spec's [pentabased](https://github.com/pentabased) lineage, where a Python implementation served as a *schema as test suite* reference against which other implementations could be checked.

## Next milestone: first hwatu prototype

The first concrete milestone for the hexdown project is a hwatu prototype that can:

- ingest a small set of canonical markdown documents (drawn from [the Mary Frances Garden Book](https://www.gutenberg.org/cache/epub/53098/pg53098-images.html))
- store them as trees of cards in a local store
- pull them back out and re-render them as equivalent markdown

This forces the spec to resolve enough TBDs to make end-to-end ingestion work, while keeping the scope narrow enough that the first prototype is reachable in a few iterations. See [hwatu's design docs](https://github.com/hexdown/hwatu/tree/main/design) for the prototype's planned scope and phasing.

## Feedback loop

The spec leads in shape but follows in detail. The expected feedback loop is:

1. Spec defines the shape of a concept (e.g., "a back is a metaschema record with these fields")
2. Hwatu implements against that shape and either confirms it works or surfaces a gap
3. Spec is updated with the concrete details (e.g., "the record encoding for a back is sip stream X")
4. Hanafuda implements against the firmed-up spec

This means hwatu has a license to make provisional choices in places the spec is silent, and the spec accepts revisions from those choices when they prove out.

## Beyond the first prototype

Once the first hwatu prototype is running, the next questions to take up include:

- whether to also prototype against a real key-value store in hwatu, or move directly to hanafuda
- the delta tree and merge semantics
- the permission and gardener model
- forage queries (text search, embedding similarity)
- cross-orchard sharing semantics

These are not in scope for the first prototype; that prototype's success is judged on the markdown round-trip alone.
