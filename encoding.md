# Encoding

Hexdown content is encoded as a sequence of **sips** — 6-bit values, each one of 64 possibilities. A sip's role is determined by its position in the parse: it may be a kind glyph, a child count, a null marker, or — at a leaf position within a blossom — a **petal** carrying character or value data. This document describes the sip values, the phoneme/glyph mapping that applies to petals at leaf positions, and notes on on-disk representation.

## Sip values

64 sip values map to:

- alphanumeric characters `[0-9a-z]` (36 values)
- the **beat** glyph `-` (1 value, the null sip)
- the **possessive** glyph `*` (1 value)
- TBD

The same 64-value space is reused for structural roles (kind glyphs, counts, null markers) — a sip's value is just a number; whether it codes for a phoneme depends on its parse position. The phoneme mapping below describes how sip values are interpreted *as petals* within phonetic blossoms (neem, prop). Other blossom kinds (quant, enum, uniglyph) interpret their petal sips under kind-specific layouts.

All punctuation, formatting, and other markup that would normally be represented by characters in a text format is represented in hexdown by stem and branch nodes in the document tree — never by petals.

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
