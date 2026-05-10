# Glossary

Named concepts in hexdown, in rough dependency order — encoding terms first, then data model, then participation, then change, then operations.

## Encoding

**sip** — the basic unit of hexdown encoding: a 6-bit value, one of 64 possible values. All hexdown content is ultimately a sequence of sips. The name evokes both "a small mouthful" (smaller than a byte) and the SI prefix progression.

**glyph** — a sip in its visible role as a rendered character. "Sip" and "glyph" refer to the same underlying unit; "sip" emphasizes the numeric value and packed representation, while "glyph" emphasizes the visible mark or rendered character.

**beat** — the null sip (value 0x00, rendered as `-`). Acts as a separator within a blossom and as collision-resolution padding at the start of a sip sequence. Inherited from pentabased.

## Document nodes

**blossom** — a leaf document node holding a sip sequence with kind-specific internal structure; the smallest visible-unit leaf in a document tree. Hexdown defines several blossom kinds (neem, prop, quant, enum, uniglyph), each encoding a different kind of word-scale content. See [document-nodes.md](document-nodes.md) for the kinds.

**stem** — an inner document node that combines blossoms or other stems into larger structures. Examples: a *span* combines blossoms into a compound word; a *phrase* combines blossoms and spans into a thought. Stems also carry the contextual information that in markdown is conveyed by surrounding characters (punctuation, formatting cues, etc.).

**branch** — a non-leaf document node describing high-level document organization within a card, such as a chapter, section, or paragraph.

**root** — the entry node of a sip-encoded tree. A card has two such trees and so two roots: the **face-root** is the entry node of the face's document-node tree (parsed under the card's trellis), and the **back-root** is the entry node of the back's metaschema record. "Root" alone is context-dependent; the unrelated *taproot* concept refers to a card playing the entry-point role for a whole document.

## Data model

**orchard** — the totality of documents and history managed by a single hexdown installation. The hexdown-native term for what is sometimes called a *corpus* in prose. An orchard contains plots; documents within different plots may be tended by different gardeners with different permissions.

**plot** — a collection of documents within an orchard, grouped by intent and by the arbor they conform to. For example, a "Direct Messages" plot containing posts, or a "Cases" plot containing case study reports. Plots are the unit gardener permissions attach to.

**arbor** — a document-level schema defining the valid macrostructure of one kind of document. Examples: a "report" arbor describes documents organized as books containing chapters containing sections; a "job-ticket" arbor describes documents with status, assignee, description, and a log of comments. The hexdown core ships several built-in arbors, and custom arbors can be defined to extend the system.

**trellis** — a microstructure schema defining the valid document-node tree within a single card's face. Examples: a "passage" trellis describes prose content; a "diagram" trellis describes vector graphics. Trellises are independent of arbors and may be referenced by many arbors at different positions. Just as an arbor shapes the macrostructure of a document, a trellis shapes the microstructure of a card.

**document** — a tree of cards rooted at a taproot card, conforming to the arbor recorded on that taproot's face. A document has no separate stored existence: it is the closure of (taproot card + all cards reachable through child references on backs). The document's id is its taproot's card-id.

**taproot** — the card at the entry point of a document tree; the document's anchor. There is exactly one taproot per document, and the taproot's card-id serves as the document's id. All taproots conform to the same core trellis (also called *taproot*), giving documents a uniform metadata shape regardless of the arbor governing their content. Listing all taproots in a plot enumerates the plot's documents.

**card** — a single node in a document tree, composed of a face and a back. Cards have stable ids; faces are content-addressed by hash, so unchanged content is shared rather than duplicated.

**face** — the content side of a card. A tree of root, branch, stem, and blossom document nodes encoded as a flat sequence of sips, valid under the card's referenced trellis. Faces are content-addressed by hash.

**back** — the catalog side of a card. Holds the card's stable id, the trellis it conforms to, the position path within its document's arbor, the content hash of its face, and ordered references to its child cards. The document's arbor is not stored on every back — it lives once, on the taproot's face.

**sheaf** — a query result set. A collection of cards returned by a forage operation, possibly with associated relevance scores or position context.

## Participation

**gardener** — a participant in a hexdown orchard. May be a human, a computational agent, or another kind of person. Gardeners have associated permissions over plots and operations within an orchard.

## Change

**till** — a delta describing structural changes to an orchard: creating plots, publishing or versioning arbors, granting permissions to gardeners. The structural counterpart of flush.

**flush** — a delta describing content changes to a card: splicing new content into its face, updating the child card references on its back. The content counterpart of till.

## Operations

**plot** *(verb)* — to initiate a new plot for a gardener. Distinct from the noun "plot"; named for breaking ground on new garden plot.

**sow** — to initiate a new document root within a plot. Produces a new root card and registers it as a document entry point.

**splice** — to modify a document by inserting, replacing, or removing document nodes within a card. Produces a new card-version (a new face and back) and a flush delta.

**forage** — to query an orchard for a sheaf of cards. Queries may be by document id and path, by metadata on the card or its containing document, by face text content, or by embedding vector similarity.

**delta** *(verb)* — to subscribe to a stream of orchard updates as till and flush deltas are produced.

## Open questions

- Naming for the operation that publishes a new arbor or a new arbor version — currently subsumed under "till" but may deserve a verb of its own.
- Whether "blossom" refers only to the document-node kind or also to a specific kind of leaf data (e.g., alphanumeric blossoms vs other future leaf types).
