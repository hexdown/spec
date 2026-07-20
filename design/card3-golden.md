# card 3, hand-spelled — the golden card

the caw-caw paragraph of chapter 4 ([ch4-annotation.md](ch4-annotation.md) card 3), encoded by hand to the sip level under the passage schema and the proposed glyph table. this is the fixture the first encoder must reproduce exactly — the hand becomes the test for the machine.

## the tree

```
paragraph
  turn
    exclamation
      phrase: caw-caw
  statement
    phrase: ^feather ^flop cleared his throat
  turn
    exclamation
      phrase: caw-caw
```

kind values (passage schema declaration order under [glyphs.md](../glyphs.md), re-spelled 2026-07-20): paragraph 1 · statement 2 · exclamation 4 · turn 6 · phrase 9 · prop 59 (conventional first blossom, `^`) · neem 60 (reserved, `·`) · schema node 0 · bloom 62 · null/beat 63. petals are base-36. count sips encode children−1.

## the sip stream

face = pads · schema node · pads. the schema bloom's 64 petals are the blake2b-384 of the passage schema card — computable only once that card is packed; written symbolically here as ⟨#passage⟩. everything else is exact.

as values:

```
0 1                                schema node, 2 children
62 63 ⟨#passage: 64 petals⟩        bloom, 64 children — the schema hash
1 2                                paragraph, 3 children
6 0 · 4 0 · 9 0                    turn(1) · exclamation(1) · phrase(1)
60 6 · 12 10 32 63 12 10 32        neem, 7 petals: c a w - c a w
2 0 · 9 4                          statement(1) · phrase(5)
59 6 · 15 14 10 29 17 14 27        prop, 7 petals: f e a t h e r
59 3 · 15 21 24 25                 prop, 4 petals: f l o p
60 6 · 12 21 14 10 27 14 13        neem, 7 petals: c l e a r e d
60 2 · 17 18 28                    neem, 3 petals: h i s
60 5 · 29 17 27 24 10 29           neem, 6 petals: t h r o a t
6 0 · 4 0 · 9 0                    turn(1) · exclamation(1) · phrase(1)
60 6 · 12 10 32 63 12 10 32        neem, 7 petals: c a w - c a w
```

as glyphs, one unbroken stream:

```
01*-⟨#passage⟩12604090·6caw-caw2094^6feather^3flop·6cleared·2his·5throat604090·6caw-caw
```

read it: the words are *visible* — `caw-caw`, `feather`, `flop`, `cleared`, `his`, `throat` — each opened by its interpunct (neem, the roman word-separator returned to duty) or caret (prop on its conventional first-blossom seat, `0o73` = `^`, so the annotation notation `^feather ^flop` turns out to have been the serialization in disguise). counts render as digits; `01*-` opens the card; `604090` is a turn folding down through exclamation to phrase.

## the arithmetic

| | |
|:--|:--|
| content sips | **141** (annotation formula estimated ~150 — within 6%) |
| bit-packed | 141 × 6 = 846 bits = 106 bytes |
| slurp | 5 × 24-byte increments = 120 bytes = **160 sips** |
| padding | 19 trailing beats of slack (plus leading arena as collision resolution demands) |

## the encoder's contract

given the passage schema card (to compute the real hash) and this tree, the encoder must produce this exact sip sequence — values, counts, petals, order — byte-identical after packing, differing only in the resolved bloom petals and any collision-arena pads. when it does, the hand and the machine agree on what hexdown *is*, and the "schema as test suite" lineage has its first link.
