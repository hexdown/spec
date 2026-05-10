# Metaschema

The metaschema describes how hexdown's own structural objects — arbors, trellises, card backs, deltas, and API messages — serialize as sip sequences. It is the schema that defines all other schemas.

The central discipline (inherited from pentabased) is **kind-glyph-implies-form**: every node in an AST begins with a kind sip whose value determines what follows (counted children, fixed-arity children, link slot, or leaf data). The kind sip is the schema — no separate tag or length field is needed to parse a node.

TBD — concrete metaschema node kinds and their forms.

## Node forms

TBD. Candidate forms (from pentabased):

- **headed** — kind + head + body-count + body-children. For nodes that have separate metadata and content sections (e.g., a card with a banner and a body).
- **counted** — kind + count + children. For variable-length nodes.
- **children(N)** — kind + N children. For fixed-arity nodes.
- **link** — kind only. Consumes one entry in the surrounding card's child-card-refs list.

## Arbor serialization

TBD — how an arbor itself is serialized as a sip sequence. An arbor is a tree of valid-position descriptors, each naming a trellis and an arity. Arbor definitions are themselves hexdown documents under a built-in "arbor" arbor (the metaschema is self-describing).

## Open questions

- Whether the metaschema has 4 or more node forms
- Whether arbors are content-addressed or named
- Self-bootstrapping: does the metaschema have its own metaschema, or does it define itself?
