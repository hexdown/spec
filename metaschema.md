# Metaschema

The metaschema describes how hexdown's own structural objects — arbors, trellises, card backs, deltas, and API messages — serialize as sip sequences. It is the schema that defines all other schemas.

The central discipline (inherited from pentabased) is **kind-glyph-implies-form**: every inner node begins with a kind sip whose value determines what follows (counted children, fixed-arity children, link slot, or leaf petals). The kind sip is the schema — no separate tag or length field is needed to parse a node.

TBD — concrete metaschema node kinds and their forms.

## Structural rules

These rules apply uniformly across all hexdown ASTs (arbors, trellises, card faces, card backs, deltas):

- **Inner nodes carry a kind sip.** Every non-leaf node begins with a kind sip that names its kind and (via the metaschema) determines its form.
- **Leaves are single petals.** Every leaf position holds exactly one sip — a petal — whose interpretation comes from its parent's grammar. There are no multi-sip leaf nodes.
- **Variable-length nodes have 1-64 children.** A counted-form node uses a 6-bit count sip whose value (0-63) maps to a child count of (1-64). Zero-child nodes are not representable in counted form.
- **Absence is explicit.** A required-but-absent position takes the null sip (beat, value 0x00). This applies both to fixed-arity child slots and to positions where a child kind sip is otherwise expected.
- **Leading null padding is collision-resolution.** Leading null sips at the very start of a face stream are collision-resolution padding for the content-addressed face hash; they are skipped by the parser. (Inherited from pentabased.)

## Card classes and node classes

Hexdown distinguishes three classes of card by role, and four classes of node by structural position within a face. Each card class uses a particular flavor of trellis, and each flavor of trellis produces faces with a particular node structure.

| Card class | Trellis flavor    | Face structure                            | Holds rendered content? |
|:-----------|:------------------|:------------------------------------------|:------------------------|
| taproot    | taproot (branch)  | bough rooted at the taproot kind          | no                      |
| branch     | branch trellis    | bough with link petals                    | no                      |
| leaf       | leaf trellis      | stems and blossoms over content petals    | yes                     |

| Node class | Where it appears                | Children are                          |
|:-----------|:--------------------------------|:--------------------------------------|
| bough      | the face-root of a branch trellis | link petals (kind sips naming child cards) |
| stem       | within a leaf trellis            | blossoms or other stems              |
| blossom    | within a leaf trellis            | content petals                       |
| petal      | leaf position (always)           | nothing (a single sip)               |

The discipline this expresses: **rendered content lives only in leaf cards.** Branch cards (including the taproot) organize the tree of cards but carry no content of their own; their faces are uniformly boughs over link petals. Leaf cards carry actual content — text, diagrams, photos — under whichever leaf trellis the arbor specifies at their position.

## Trellis flavors

- **branch trellis** — produces branch cards. Face structure: a bough rooted at the trellis's specific kind (e.g., `book` bough for a book card under the report arbor). Children are link petals; back resolves each to a child card-id.
- **leaf trellis** — produces leaf cards. Face structure: a tree of stems and blossoms over content petals, rooted at whatever face-root kind the trellis specifies.

The **taproot trellis** is a particular branch trellis — every document has one taproot card, and that card uses this trellis.

## Bootstrap trellises

Two trellises have their grammars hardcoded into hexdown parsers, forming the bootstrap from which everything else is loaded:

- **metatrellis** — the trellis used by *trellis cards*. Its face describes the AST grammar for a trellis (which face-root kind, which child node kinds are valid at each position, etc.). A parser hardcodes how to parse the metatrellis.
- **metarbor** — the trellis used by *arbor cards*. Its face describes the tree of valid card positions in a document type, with each position naming the trellis (by card-id) that governs cards at that position. A parser hardcodes how to parse the metarbor.

With these two hardcoded, everything else loads dynamically: when a parser opens a document, it reads the taproot's `arbor-ref`, loads the arbor card (parsed under the metarbor), resolves each position's trellis (each is itself a card parsed under the metatrellis), then uses those trellises to parse content cards.

This is what makes orchards self-contained: the schemas needed to interpret an orchard's contents are themselves cards within the orchard, not external definitions.

## Node forms

TBD. Candidate forms (from pentabased):

- **headed** — kind + head + body-count + body-children. For nodes that have separate metadata and content sections (e.g., a card with a banner and a body).
- **counted** — kind + count + children. For variable-length nodes (1-64 children).
- **children(N)** — kind + N children. For fixed-arity nodes.
- **link** — kind only. Appears in bough positions; consumes one entry in the surrounding card's child-card-refs list.

## Methodology

**Bias toward simple symmetric rules.** When choosing between a stricter, more symmetric structural rule (e.g., "every variable-length node has 1-64 children", "every leaf is a single sip") and a more permissive but more complex rule, the metaschema picks the simpler symmetric one until a concrete use case demonstrates that the simplicity costs more than the asymmetry would. This bias is consistent with the *modest data model* commitment in [vision.md](vision.md): the cost of asymmetric flexibility is paid once by every implementation, while the cost of symmetric rigidity is paid by participants doing things the rule made awkward — and participants are usually better placed to absorb that cost.

## Open questions

- Whether the metaschema has 4 or more node forms.
- The full sip-level encoding of metatrellis and metarbor faces (this is the gate to a working implementation).
- Self-bootstrapping: are metatrellis and metarbor themselves stored as cards (using each other as their trellises) or purely as parser code? The cleanest answer is "stored as cards but the parser also knows them by heart" — a parser can verify the stored cards parse to its hardcoded grammar at load time.
