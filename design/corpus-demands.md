# corpus demands

a working list of the features the corpus demands of hexdown's trellises, grown as documents are converted and closely read. every construct a document uses is either mapped to the trellis drafts in [schemas.md](../schemas.md) / [flora.md](../flora.md) or recorded here as wanted. the spec proper absorbs items from this list as they firm up.

status markers: `[ ]` wanted, `[~]` in discussion, `[x]` decided (decision noted), `[>]` deferred on purpose.

## the mary frances garden book — chapter ladder

four chapters + front matter converted and close-read (2026-07-18). suggested ingest order, each step adding a fistful of demands:

### chapter 4 — the minimum viable chapter

- [x] paragraph / statement / question / exclamation / phrase stems — already in the passage-trellis draft
- [x] neem + prop blossoms; span for hyphenated compounds ("eaves-dropping", "good-bye") — in draft
- [x] **speech is structural** (2026-07-18) — a speech stem for utterance by a person, distinct from quotation-from-a-source; quote marks become render output; narration tags stay ordinary phrases
- [x] **elision and possession are distinguished in the schema** (2026-07-18) — separate glyphs; see discussion notes below
- [ ] interruption dash — "your silly—I mean your brother's, plan"
- [ ] chapter banner — title stem on the chapter card's banner leaf
- [x] **normalization policy** (2026-07-18) — chapter-opener caps ("NEITHER of the children...") normalize at ingest; author-voice hyphenation ("to-day", "fish-worm") is content and preserved

### chapter 2 — adds prosody

- [x] **emphasis kinds** (2026-07-18) — a small closed set of non-numeric levels, starting with `stress` (renders italic) and `shout` (renders caps); canonical test is the escalation triple, chapter 2: "Feather Flop!" / "_Feather Flop!_" / "FEATHER FLOP!"
- [ ] verse + line stems — feather flop's quatrain; line breaks distinct from paragraph breaks
- [ ] aside stem — parenthetical stage direction: "(counting them off)"
- [ ] colon-introduced speech — "and called again and again as loudly as she dared:" followed by displayed shouts, each its own paragraph

### chapter 47 — adds embedded lectures

- [ ] section structure within a chapter — embedded `###` headings inside a long speech
- [~] speech containing document-like structure — the toad-stool lecture is *spoken* but typeset as a sub-document; speech-with-sections, or referenced sub-cards? (open)
- [x] **mid-sentence headings normalize** (2026-07-18) — "They, like most other— / Fungi, / live on dead vegetable matter" becomes a plain heading + full sentences at ingest

### chapter 23 — adds apparatus

- [ ] list + point stems with quant blossoms — "6 cutworms", "9 ants"
- [ ] note stem — inline footnote citing u.s. farmers' bulletin no. 196
- [ ] abbreviation rendering — mr., mrs., u.s., no., n. j. (period-bearing props need a render rule)
- [ ] double em-dash interruption — "baby toads——" (verified present in the 1916 original; content, not a typo)
- [ ] term quotes — 'coiled worm' (single-quoted term vs speech quotes: different roles, same glyphs in the source)

### front matter — adds the book layer

- [ ] book banner — title + bold-italic subtitle
- [ ] at meta card — byline, illustrator credit
- [ ] dex meta card — "published in 1916"
- [x] contents is not stored — derived from the book bough + chapter banners at render time (falls out of the data model)

## apostrophe discussion notes

**decided** (2026-07-18): two petal-level glyphs, **elide** `'` and **possess** `*`, documented in [encoding.md](../encoding.md) under "the three small marks"; both render `’` in markdown-facing output, distinctly in pentabased-facing renders. glyph budget affords it (25 values remain free after declining capital letters). costs named honestly:

- ingest must classify every apostrophe — mostly mechanical (closed class of contractions; 's / s' patterns), but genuine ambiguities exist ("the dog's barking": is-contraction or possessive-gerund) and require a reader's judgment
- word-final positions must be legal for both: "toads'" (possess), "goin'" (elide); multiple elisions per word occur ("fo'c's'le")
- see wishlist: the phrasal-genitive question may eventually move possession up out of the petals

## wishlist — remember to develop

- [ ] speaker attribution on speech stems — who is talking; narrative gold, deliberately deferred
- [ ] phrasal genitive — english possession attaches to phrases ("the king of spain's daughter"), so a petal-level possess glyph records orthographic position, not linguistic attachment; does possession eventually become a span/phrase-level marker that renders at the final word?
- [ ] hexdown-flavored markdown — a spec doc defining the canonical render target and round-trip dialect: inline footnote definition (`^[...]`-style; where a note *renders* — hover, margin, endnote — is a render decision, not an authoring one), curly quotes, which emphasis forms, heading conventions
- [ ] figure / photo trellises — 167 vendored illustrations awaiting a captioned conversion pass
- [ ] speech vs citation as card reference — a mature quote stem cites *another card* (mark + offset + breadth, per the passage-trellis draft); speech and citation may converge on shared machinery
- [ ] onomatopoeia — "caw-caw!", "cluck! caw!" — plain neems unless evidence demands more
- [ ] tree discipline at the card layer — faces are trees by construction (composition cannot cycle); cycles among card refs forbidden always, checked at flush time; within one document, start strict-tree and admit repeated grafts of the same card only if a real use case demands (orchard-wide sharing of cards across documents is already a deliberate DAG)
- [ ] phrase-for-word interchangeability — recursion is opt-in per trellis rule (each stem kind lists which children it admits); wanted for the phrasal genitive

## not demanded by this corpus (deferred with relief)

- uniglyph: zero occurrences in 845 lines — every word fits [a-z0-9] plus structural punctuation
- tables, code blocks, external links: absent
- quant: needed only for chapter 23's menu and the year 1916; chapters 2 and 4 spell out even "four hundred and fifty-one"
- enum: absent
