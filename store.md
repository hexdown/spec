# Store

Hexdown persistence has two levels, with one sentence of responsibility between them: **the store never parses; the orchard never touches a disk except through the protocol.**

- The **store protocol** (level 0) is the substrate contract: three tables of bytes under sorted byte keys. It knows nothing of sips, schemas, or cards, and any keyed byte substrate can implement it.
- The **orchard api** (level 1) is the hexdown library surface: it parses, validates, mints rings, seals faces, replays the log, and serves projections. It is what gardeners, applications, and (eventually) servers call.

## The store protocol

Three tables with hardcoded names — the machinery bootstraps from table names plus replay, not from configuration records ([design/genesis.md](design/genesis.md)):

| table | key (byte projection) | value |
|:--|:--|:--|
| `faces` | bloom — 48 digest bytes | face slurp |
| `tills` | stamp ring — 8 bytes big-endian | till record slurp |
| `flushes` | stamp ring — 8 bytes big-endian | flush record slurp |

Three verbs, and no more:

- **get**(table, key) → value, or absence
- **put**(table, key, value)
- **scan**(table) → every record, ascending by key bytes

**There is no delete, and there is no overwrite** (ruled 2026-08-01). Faces are immutable values, the logs are append-only, and revert is an append ([deltas.md](deltas.md)); a store only grows, and what has grown never changes. Put refuses to change what a key holds: writing identical bytes again is a quiet no-op — so retries, re-seeding, and a replica writing down what it reads are all harmless — while writing different bytes is an error. Nothing in the model ever needs an overwrite (a revised card is a new face under a new bloom and a new record under a new stamp ring), so a colliding put is always a writer's bug surfacing, never intent. Immutability needs no parsing, which is why it lives below the seam: the store cannot verify what bytes *mean* — bloom-against-content checks belong to the orchard — but it refuses to let bytes *change*. Discarding a store is a substrate operation — removing a directory or a file — not a protocol verb.

Keys are the byte-aligned projections of [data-model.md](data-model.md), chosen so that byte order equals meaning: a scan of a log table is log order, and a document's cards are a contiguous range of ring space. The sorted scan is the protocol's only query primitive — everything richer is an orchard-level projection.

Because tills and flushes share the stamp-ring shape, the table provides the namespace: the same key names different records in different tables. **Each table has its own key space, even when key shapes are shared.**

### Values are slurps

A **slurp** is the stored form of a sip sequence: sips bit-packed four to three bytes, sized in 24-byte increments (32 sips, three 64-bit words). Minimum one increment; maximum sixty — 1440 bytes, 1920 sips, chosen so a slurp fits a typical network packet payload after IPv6 + TCP overhead. Within a slurp:

- **leading pads** — the collision-resolution arena of content-addressed faces; parsers skip them at start of stream
- **content sips** — the encoded value
- **trailing pads** — slack within the chosen size; parsers skip them at end of stream

The bloom is computed over the entire slurp, so the leading/trailing split is part of the content's identity. Collision resolution is redistribution and growth: move a trailing pad to the leading arena (the content shifts, the hash changes, the size doesn't); when slack is exhausted, grow by one increment; a value that cannot fit the maximum slurp is **rejected** — fragmenting oversized content into more cards belongs to the participant shaping the document, not to the storage layer (the modest-data-model commitment).

Delta records are events keyed by stamp rings, not values keyed by hash, so their slurps carry no arena ([deltas.md](deltas.md)).

### Durability classes

Two durable classes, and only two:

- **content-addressed, immutable** — `faces`. Written once, never modified, deduplicated across every card that shares content.
- **append-only, immutable** — `tills`, `flushes`. The full history of the orchard is these two tables.

Everything else — backs, the plot table, the schema registry, the id allocator — is a projection distilled by replay and held in memory, never stored ([deltas.md](deltas.md)).

### Engines and spelling

A directory of flat files, an embedded sql database, and a b-tree key-value store are all adequate engines; the contract is sized to the least of them. A hexdown store is portable across engines **byte for byte** — same tables, same key bytes, same slurps — which makes engine equivalence testable and migration a substrate change rather than a translation.

Where an engine names records as text — a file per record — the canonical spelling of a key is **octal, two digits per sip** ([data-model.md](data-model.md)): a stamp ring is twelve digits, a hyphen, eight digits (`015230603215-00000006`), and a bloom is 128 unbroken digits. Octal keeps every sip boundary visible (three bits per digit divides six bits per sip; hex would smear sips across half-digits), uses no letters (immune to case-insensitive filesystems), and sorts identically to the key bytes — a directory listing of a log is the log in order. Glyph strings are inspector output, never names.

A reference store checked into the corpus repo — the **golden store** — lets every implementation validate the whole stack at once: seed the genesis and compare byte for byte; open the directory, and the readme should greet you.

## The orchard api

The orchard api is where all hexdown semantics live, stacked over any store:

- **read** — *open* a store into an orchard by replaying the log ([deltas.md](deltas.md)); *inspect* any record; *list* plots and documents; *forage* a query into a *sheaf* ([glossary.md](glossary.md); semantics not yet specified).
- **write** — the record kinds (*found*, *plot*, *stake*; *sow*, *shoot*) and the authoring verbs over them (*splice* edits a face tree and emits shoots). Writing is where faces are sealed, blooms computed, rings minted, and records appended.
- **watch** — a subscription is a reader standing at a log position: the stamp ring is the cursor, and everything past it is the notification stream. The append-only total order makes watching the same act as reading.

Validation belongs entirely to this level, split the tree-sitter way: **parsing never fails** (any complete sip stream yields a tree); **judgment is separate** (per-subtree verdicts under a schema, on demand); and **replay carries the structural checks** that need no schema judgment at all — a record's child-ring count against its face's grafts. The store below verifies nothing.

## The severable seam

Hexdown implementations are embedded libraries first: open a store, get an orchard — the sqlite and git posture, not the postgres posture. An orchard is community-scale by design (the archive-and-fork capacity story in [data-model.md](data-model.md)), so embedding is the common case.

But the level-1 boundary is drawn to be **severable** — like a modern editor's split between the process that owns the filesystem and the interface that talks to it, the orchard api is the seam where a wire can later be inserted: an orchard process near the store, clients wherever they live. The store's shape has already decided most of that wire story: logs ship by stamp-ring cursor, faces fetch by bloom (content-addressed, so cacheable anywhere), and a replica is a reader that writes down what it reads. The server, when it comes, is a skin over the same api — not a rearchitecture.

## Open questions

- Forage and sheaf: the query surface is named but not specified.
- Subscription delivery beyond "a reader at a cursor" (buffering, backpressure, transport): deferred to the wire skin.
- Whether oversized-value rejection ever relaxes toward storage-layer chunking if real corpora demand it (current stance: reject).
- The lifecycle of append-only stores — **promote & compact** as a development of archive & fork (sketched 2026-08-01, from the chorognomon conversations): a base scratch level records everything from every participant; promotion carries the valuable parts up layers of durability and visibility (comment → project → the boot room that keeps strategy across campaigns); compaction — at intervals, and at fork time — exports the most valuable bits to share with friends and to carry forward to future versions of ourselves, while cold storage keeps the full text forever; demotion is the only deletion. Efficient information processing depends on effective garbage collection: we clear space out so we can space out. Whether promotion layers are new machinery or just plots (already the permission surface and the foraging lens) with a record kind for re-plotting is unexplored.
