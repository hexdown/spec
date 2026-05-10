# Encoding

Hexdown content is encoded as a sequence of **sips** — 6-bit values, each one of 64 possibilities. This document describes the sip values, their mapping to visible glyphs, and the rules for packing sips into bytes for on-disk storage.

## Sip values

64 sips cover:

- alphanumeric characters `[0-9a-zA-Z]` (62 values)
- the **beat** glyph `-` (1 value)
- the **possessive** glyph `*` (1 value)

All other punctuation and symbols are represented as stem document nodes rather than blossom glyphs.

TBD — concrete mapping table assigning each sip value to a numeric value and a rendered character.

## Packing

TBD — on-disk packing of sip sequences. Natural alignment is 4 sips per 3 bytes. Decision needed:

- bit-packed binary (4 sips per 3 bytes, no waste)
- byte-aligned binary (1 sip per byte, simple but 25% waste)
- utf-8 string (1 sip per 1-4 bytes, human-readable but high overhead — pentabased's bootstrapping choice)

## Open questions

- Endianness within the 4-sip-per-3-byte packing
- Whether to mandate a leading magic number or version sip in stored sequences
- How leading beat sips for collision-resolution padding interact with packed boundaries
