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

kind values (passage schema declaration order): paragraph 2 · statement 3 · exclamation 5 · turn 7 · phrase 10 · prop 32 · neem 61 (reserved). count sips encode children−1.

## the sip stream

face = pads · schema node · pads. the schema bloom's 64 petals are the blake2b-384 of the passage schema card — computable only once that card is packed; written symbolically here as ⟨#passage⟩. everything else is exact.

as values:

```
1 1                                schema node, 2 children
63 63 ⟨#passage: 64 petals⟩        bloom, 64 children — the schema hash
2 2                                paragraph, 3 children
7 0 · 5 0 · 10 0                   turn(1) · exclamation(1) · phrase(1)
61 6 · 3 1 23 0 3 1 23             neem, 7 petals: c a w - c a w
3 0 · 10 4                         statement(1) · phrase(5)
32 6 · 6 5 1 20 8 5 18             prop, 7 petals: f e a t h e r
32 3 · 6 12 15 16                  prop, 4 petals: f l o p
61 6 · 3 12 5 1 18 5 4             neem, 7 petals: c l e a r e d
61 2 · 8 9 19                      neem, 3 petals: h i s
61 5 · 20 8 18 15 1 20             neem, 6 petals: t h r o a t
7 0 · 5 0 · 10 0                   turn(1) · exclamation(1) · phrase(1)
61 6 · 3 1 23 0 3 1 23             neem, 7 petals: c a w - c a w
```

as glyphs, one unbroken stream:

```
aa##⟨#passage⟩bbg-e-j-.fcaw-cawc-jd^ffeather^dflop.fcleared.bhis.ethroatg-e-j-.fcaw-caw
```

read it: the words are *visible* — `caw-caw`, `feather`, `flop`, `cleared`, `his`, `throat` — each opened by its dot (neem) or caret (prop sits on the first blossom seat, value 32 = `^`, so the annotation notation `^feather ^flop` turns out to have been the serialization in disguise). structure passes as short letter-pairs between the words; `aa##` opens the card; `g-e-j-` is a turn folding down to its phrase.

## the arithmetic

| | |
|:--|:--|
| content sips | **141** (annotation formula estimated ~150 — within 6%) |
| bit-packed | 141 × 6 = 846 bits = 106 bytes |
| slurp | 5 × 24-byte increments = 120 bytes = **160 sips** |
| padding | 19 trailing beats of slack (plus leading arena as collision resolution demands) |

## the encoder's contract

given the passage schema card (to compute the real hash) and this tree, the encoder must produce this exact sip sequence — values, counts, petals, order — byte-identical after packing, differing only in the resolved bloom petals and any collision-arena pads. when it does, the hand and the machine agree on what hexdown *is*, and the "schema as test suite" lineage has its first link.
