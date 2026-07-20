# Glossary

Named concepts in hexdown, in rough dependency order — encoding terms first, then document nodes, then data model, then participation, then change, then operations.

## Encoding

**sip** — the basic 6-bit data unit of hexdown encoding, one of 64 possible values. Any 6 bits of a hexdown stream is a sip; sips serve different roles depending on their position in the parse — kind glyph at the start of an inner node, count, petal or graft at a leaf, or null marker. Analogous to a byte: a sip is a generic data unit whose meaning is determined by context.

**petal** — a sip at a leaf position inside a blossom-family node; every leaf of a face is a petal. Its interpretation depends on its parent's kind — in a *neem* or *prop* a phoneme; in a *quant* a digit, mantissa, exponent, or precision marker; in an *enum* an ordinal or range marker; in a *unipoint* a bit-group of a unicode codepoint; in a *graft* the kind of a grafted child card; in a *bloom* six bits of a content hash. Content petals appear only in leaf trellises.

**graft** — a blossom-family node inside a bough; the site where a child card is joined to its parent branch card. Holds exactly one petal naming the kind of child card grafted at that position; the card's back resolves each graft, in face order, to a child card-id via its child-card-refs list. Grafts are the structural counterpart of content blossoms (blossoms bottom out leaf faces with rendered petals; grafts bottom out branch faces with reference petals), and appear only in branch trellises.

**beat** — the null sip (value `0o77`, all bits high, rendered as `-`). The pad node's kind, the petal of intentional absence (a bloom of 64 beats is the null hash), and collision-resolution padding at the start of a content-addressed sip sequence.

**pad** — a single-sip node whose kind is the beat value. Pads appear only before and after a face's schema node — leading pads are the collision-resolution arena for the face hash, trailing pads are slack; parsers skip both.

**bloom** — the reserved blossom kind (`0o76`) whose petals carry a content hash: a full bloom's 64 petals encode a 384-bit hash, and a bloom of 64 beats is the null hash by which a schema card marks itself. Blooms are a species of blossom.

**neem** — the reserved blossom kind (`0o74`) whose petals are phonemes: the universal phonetic word. Prose words and metaschema names are the same kind — schemas and kinds name themselves with neems. (The metastructure sketch's *symbol* merged into neem, 2026-07-18.)

**schema node** — the reserved stem kind (`0o00`) that opens every face, with exactly two children: a bloom carrying the content hash of the card's governing schema (the null hash for schema cards), and the face's card root.

## Document nodes

The node classes form a top-down hierarchy: bough (in branch cards), stem and blossom (in leaf cards), graft (inside boughs), and petal (the sole leaf form, inside blossom-family nodes). Structurally every kind belongs to one of four families by its leading bits: stem-family kinds hold nodes, branch-family kinds hold only grafts, blossom-family kinds hold petals, and the null kind is the single-sip pad (see [glyphs.md](glyphs.md)). See [data-model.md](data-model.md) for the card and node classes and [flora.md](flora.md) for the kinds within each.

**bough** — the top-level inner node of a branch trellis. Its direct children are grafts — single sips whose values are kind glyphs naming the kind of child card at each position. The card's back resolves each graft position to a child card-id. Boughs hold no rendered content directly; all content lives in leaf cards.

**stem** — an inner document node found in leaf trellises that combines blossoms or other stems into larger structures. Examples: a *span* combines blossoms into a compound word; a *phrase* combines blossoms and spans into a thought; a *statement* combines phrases into a sentence with first-letter capitalization and a period. Stems carry rendering and contextual information that in markdown is conveyed by surrounding characters (punctuation, formatting cues, capitalization rules).

**blossom** — an inner document node whose direct children are petals; the smallest named word-scale unit in a document tree. Different blossom kinds (neem, prop, quant, enum, uniglyph) interpret their petal sequences differently. Blossoms only appear in leaf trellises.

**crown** — a kind eligible to stand at a leaf card's card root; in horticulture the crown is where stem meets root — the thing a gardener plants. Each leaf trellis declares its crown kinds (the passage trellis's crowns are paragraph, verse, and list); boughs are the branch-side counterpart and are card roots by definition.

**root** — the entry node of a sip-encoded tree. The **card root** is the content entry node of a card's face — the second child of its schema node (a bough in a branch card, a stem or blossom in a leaf card). The **document root** is specifically the card root of the document's taproot card. The **back-root** is the entry node of the back's metaschema record. "Root" alone is context-dependent; the distinct *taproot* concept refers to a card playing the entry-point role for a whole document.

## Data model

**orchard** — the totality of documents and history managed by a single hexdown installation. The hexdown-native term for what is sometimes called a *corpus* in prose. An orchard contains plots; documents within different plots may be tended by different gardeners with different permissions.

**plot** — a collection of documents within an orchard, grouped by intent and by the arbor they conform to. For example, a "Direct Messages" plot containing posts, or a "Cases" plot containing case study reports. Plots are the unit gardener permissions attach to.

**arbor** — the schema closure a document grows under: the taproot card's trellis together with every schema reachable from it through bloom references. An arbor is not a second kind of schema object — it is the view of the schema grove from a taproot (2026-07-19). The core arbors (prose, graph, table) are each anchored by a taproot trellis card; custom arbors are grown the same way, and every closure is content-addressed by construction.

**trellis** — a schema defining the valid document-node tree within a single card's face; the one grain of schema hexdown has. A trellis is itself a hexdown card, parsed under the hardcoded metaschema and marked by the null hash. Trellises come in two flavors: a **branch trellis** declares position kinds, each naming the schema of the cards grafted there by content hash (no rendered content); a **leaf trellis** declares content kinds selected from the flora (rendered content).

**metaschema** — the hardcoded grammar of schema cards: the schema that defines all other schemas, known by heart by every parser. A schema card declares the null hash in its schema node, and parsers may verify stored schema cards against the grammar they carry. (The earlier *metatrellis*/*metarbor* pair first unified into one metaschema and then the arbor half dissolved entirely — an arbor is a taproot's trellis, and *trellis* is the metaschema's sole root kind, 2026-07-19.)

**document** — a tree of cards rooted at a taproot card, conforming to the arbor anchored at that taproot's trellis. A document has no separate stored existence: it is the closure of (taproot card + all cards reachable through child references on backs). The document's id is its taproot's card-id.

**taproot** — the card at the entry point of a document tree; the document's anchor. There is exactly one taproot per document, and the taproot's card-id serves as the document's id. Every taproot card uses a **taproot trellis** (a branch trellis), so any system scanning an orchard can recognize and enumerate documents uniformly. The taproot's trellis anchors the document's arbor; the back's **arbor-ref**, where kept, is an index in stable-id space (the truth is the face's schema bloom and its closure).

**card** — a single node in a document tree, composed of a face and a back. Cards have stable ids; faces are content-addressed by hash, so unchanged content is shared rather than duplicated. Cards come in three classes by role: **taproot cards** are document anchors (one per document); **branch cards** organize the document tree without holding rendered content; **leaf cards** hold actual content. A card's class is determined by which trellis it uses (taproot trellis, a branch trellis, or a leaf trellis).

**face** — the content side of a card. A tree of nodes encoded as a flat sequence of sips, parsed under the card's trellis. In a branch card the face is a bough over grafts; in a leaf card the face is stems over blossoms over petals. Faces are content-addressed by hash.

**back** — the catalog side of a card. Holds the card's stable id, the trellis it conforms to, the content hash of its face, the **arbor-ref** (taproot cards only; a stable-id index to the document's anchoring schema — the face's schema bloom is the truth), and ordered references to its child cards.

**sheaf** — a query result set. A collection of cards returned by a forage operation, possibly with associated relevance scores or position context.

## Participation

**gardener** — a participant in a hexdown orchard. May be a human, a computational agent, or another kind of person. Gardeners have associated permissions over plots and operations within an orchard.

## Change

**till** — a delta describing structural changes to an orchard: creating plots, publishing or versioning arbors, granting permissions to gardeners. The structural counterpart of flush.

**flush** — a delta describing content changes to a card: splicing new content into its face, updating the child card references on its back. The content counterpart of till.

## Operations

**plot** *(verb)* — to initiate a new plot for a gardener. Distinct from the noun "plot"; named for breaking ground on new garden plot.

**sow** — to initiate a new document root within a plot. Produces a new taproot card and registers it as a document entry point.

**splice** — to modify a document by inserting, replacing, or removing document nodes within a card. Produces a new card-version (a new face and back) and a flush delta.

**forage** — to query an orchard for a sheaf of cards. Queries may be by document id and path, by metadata on the card or its containing document, by face text content, or by embedding vector similarity.

**delta** *(verb)* — to subscribe to a stream of orchard updates as till and flush deltas are produced.

## Open questions

- Naming for the operation that publishes a new arbor or a new arbor version — currently subsumed under "till" but may deserve a verb of its own.
