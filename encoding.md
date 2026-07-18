# Encoding

Hexdown content is encoded as a sequence of **sips** — 6-bit values, each one of 64 possibilities. A sip's role is determined by its position in the parse: it may be a kind glyph, a child count, a null marker, or — at a leaf position within a blossom — a **petal** carrying character or value data. This document describes the sip values, the phoneme/glyph mapping that applies to petals at leaf positions, and notes on on-disk representation.

## Sip values

64 sip values map to:

- alphanumeric characters `[0-9a-z]` (36 values)
- the **beat** glyph `-` (1 value, the null sip)
- the **possessive** glyph `*` (1 value)
- TBD

The same 64-value space is reused for structural roles (kind glyphs, counts, null markers) — a sip's value is just a number; whether it codes for a phoneme depends on its parse position. The phoneme mapping below describes how sip values are interpreted *as petals* within phonetic blossoms (neem, prop). Other blossom kinds (quant, enum, uniglyph) interpret their petal sips under kind-specific layouts.

All punctuation, formatting, and other markup that would normally be represented by characters in a text format is represented in hexdown by stem and bough nodes in the document tree — never by petals.

TBD — concrete mapping table assigning each sip value to a numeric value and a rendered phoneme.

## On-disk representation

The hexdown spec is *bit-stream-agnostic*: a hexdown face is a sequence of sips, and implementations choose how to pack those sips into bytes for storage and transport. Natural choices include:

- bit-packed binary (4 sips per 3 bytes, no waste)
- byte-aligned binary (1 sip per byte, simple but 25% overhead)
- utf-8 strings using the visible glyph mapping (1 sip per 1-4 bytes, human-readable but high overhead — pentabased's bootstrapping choice)

The choice does not affect the semantics described elsewhere in this spec; it affects only the bytes on disk or on the wire.

## Open questions

- Whether to mandate a leading magic number or version sip in stored sequences
- How leading beat sips for collision-resolution padding interact with packed byte boundaries (an implementation concern, but worth a spec-level note on what parsers must tolerate)
- Whether the spec should *recommend* a canonical bit-packed layout while leaving implementations free to choose

## metastructure

each card comprises a sequence of nodes

the first sip of each node defines the kind (within the context of parent node kinds)

pad nodes start with the null sip (`b000000`) and have no children (and only one sip) and can only appear before the first non-pad node in a card or after the last non-pad node

the second sip of each node gives the number of children in the range 1-64

the first non-pad node in each card wants to be a schema node that starts with an ordinal one sip `b000001` (possibly preceded by one or more pad nodes represented by `b000000` sips

the ordinal one sip node kind is reserved for the schema node and only wants to appear as a node kind once in each document (as the first nod-pad node)

every schema node wants to have two children
- a full 64 sip bloom node representing the hash id of the schema for this card
- the root document node of this card (from options defined in schema)

bloom nodes start with the full sip `b111111`

the children of bloom nodes are single-sip petals with no children of their own

the maximum length of a bloom node is 66 sips - the first sip gives the kind - the second sip (except in single-sip pad nodes) gives the number of children in the range 1-64 - in a 64 child sip the 3rd - 66th sips give the petal values

symbol node represents a phonetic name and starts with a `b111110` sip

each symbol node directly contains bloom children representing the sequence of phonemes in the name

### schema

a schema card gives the null hash as its schema hash

the document root sip wants 1-6? children where the first is a symbol with the name of this node and the rest define kinds that want to be children of this kind of node

kind node defines a node kind and begins with `b1000000` sip

wants to contain three children: 
- a single sip giving the kind pattern to match
- a symbol naming the node kind in a flat index of node names
- a set of acceptable children (and acceptable positions)
