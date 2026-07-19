# speech examples — chapter 4 annotated

worked examples applying the speech stems to real sentences from chapter 4 of the mary frances garden book (with one guest from chapter 23). the core model is **decided** (2026-07-18): dialogue is carried by a `turn` stem whose children are sentence kinds and `quoth` attributions, with quoths embeddable *inside* sentences at phrase positions — quoted material is never stored as a node; the renderer derives the quote runs. remaining decisions are listed at the bottom; when they land, the kind definitions move to the spec proper and this doc is absorbed.

## notation

- lowercase word = a **neem** (petals spelled only where interesting)
- `^word` = a **prop** — marked even where position would capitalize anyway (`^i`, sentence-initial names): prop-ness is semantic, not positional
- `span(word word)` = a **span** (renders its blossom children hyphen-joined)
- `'` in a word = **elide** petal; `*` = **possess** petal
- indentation = tree structure; `←` = commentary

## kind roster for chapter 4

| kind | class | children | renders | card root? |
|:--|:--|:--|:--|:--|
| paragraph | stem | sentence kinds, turns | block with terminal newline | ✓ crown |
| **turn** ★ | stem | sentence kinds and quoths, ≥1 sentence | quoted runs derived; mechanics below | — |
| **quoth** ★ | stem | phrases | attribution: lowercase, comma or period by position | — |
| statement | stem | phrases, pivots, embedded quoths | capitalized, terminal `.` | — |
| question | stem | phrases, pivots, embedded quoths | capitalized, terminal `?` | — |
| exclamation | stem | phrases, pivots, embedded quoths | capitalized, terminal `!` | — |
| **broken** ★ | stem | phrases, pivots, embedded quoths | capitalized, terminal `—` (interrupted) | — |
| phrase | stem | blossoms, spans | trailing `,` if not last in parent | — |
| **pivot** ★ | stem | blossoms, spans | trailing `—` instead of `,` | — |
| span | stem | neems, props | children joined by `-` | — |
| neem / prop | blossom | petals | word (prop capitalized everywhere) | — |

★ = new. earlier drafts had `speech` and `carry` kinds; both were deleted by the embedded-quoth model (2026-07-18) — quoted runs are derivable, and a "resumed" sentence is just a sentence continuing past its embedded quoth. sentence kinds appear both as paragraph children (narration) and as turn children (dialogue); quoths appear at both sentence and phrase level — the interchangeability meta-pattern, twice.

**crown** (proposed term): a kind eligible to stand at a leaf card's root — in horticulture, the crown is where stem meets root, the thing a gardener plants. root-eligibility is declared per-trellis in the schema (the card root comes "from options defined in schema," per the metastructure): the passage trellis's crowns are paragraph | verse | list (verse and list arrive with ch2/ch23); the banner trellis's crown is title. boughs are the branch-side counterpart and are card roots by definition. how a schema card *encodes* its crown list is a question for the hand-encoded passage schema.

## the dialogue mechanics (turn's render rules)

1. **quoted runs are derived.** the renderer wraps each maximal quoth-free run of the turn's quoted material in `“ ”`, closing before each quoth and re-opening after. nothing stores a quote mark.
2. **embedded quoth** (at a phrase position inside a sentence): renders like a phrase — lowercase, trailing comma. the phrase before it contributes its own natural comma inside the close-quote, and the sentence continues lowercase after the re-open: mid-sentence, no capital to derive.
3. **sentence-level quoth** (direct child of the turn): trailing `.` when quoted material precedes it; trailing `,` when it introduces (no quoted sentence before it in the turn).
4. **terminal softening:** a statement immediately followed by a sentence-level quoth renders its `.` as `,` — question, exclamation, and broken keep their marks.
5. **capitalization is derived throughout:** each quoted sentence capitalizes at its start — except a continuation after an embedded quoth (mid-sentence); quoths render lowercase — except a turn-initial introducer, which opens the frame sentence; props capitalize everywhere.

every rule is local to the turn — nothing outside a turn needs to know dialogue exists. the comma/period distinction is not a rule at all: **the level is the meaning.**

## exhibit a — the full dialogue sentence

> "Is he anywhere about?" inquired Feather Flop, looking around anxiously. "I thought I saw him go."

```
turn
  question
    phrase: is he anywhere about
  quoth
    phrase: inquired ^feather ^flop
    phrase: looking around anxiously
  statement
    phrase: ^i thought ^i saw him go
```

render walk:

```
“Is he anywhere about?”         ← question keeps its mark (softening touches only statements)
 inquired Feather Flop,         ← quoth's phrases join with the phrase comma...
 looking around anxiously.      ← ...sentence-level quoth after quoted material: period (rule 3)
“I thought I saw him go.”       ← quote re-opens for the next run; ^i renders I
```

## exhibit b — quoth is not narration

> "Caw-caw!" Feather Flop cleared his throat. "Caw-caw!"

```
paragraph
  turn
    exclamation
      phrase: span(caw caw)
  statement                      ← NOT in a turn: free-standing narration, capitalized, owns its period
    phrase: ^feather ^flop cleared his throat
  turn
    exclamation
      phrase: span(caw caw)
```

the quoth/narration distinction is now pure containment: attributions live *inside* turns; narration stands *beside* them as paragraph children. this is the case that made turn earn its place.

## exhibit c — sentence-level quoth + softening + elide

> "Yes, he's gone, Feather Flop," laughed Mary Frances. "But let me show you—he has been planning such a delightful garden for me."

```
turn
  statement
    phrase: yes
    phrase: he's gone            ← neem "he's" = petals h·e·elide·s
    phrase: ^feather ^flop       ← vocative phrase
  quoth
    phrase: laughed ^mary ^frances
  statement
    pivot: but let me show you   ← renders trailing — instead of ,
    phrase: he has been planning such a delightful garden for me
```

render walk:

```
“Yes, he's gone, Feather Flop,”  ← softening: the statement's stored . renders , before the quoth (rule 4)
 laughed Mary Frances.           ← quoted material precedes: the quoth takes a period (rule 3)
“But let me show you—he has been planning such a delightful garden for me.”
                                 ← new sentence, new run: capital B derived; the pivot supplies the —
```

the tree stores a statement; the comma is the turn's doing. ingest runs the rule backwards: quoted material ending `,` + lowercase said-verb ⇒ recover a statement before a sentence-level quoth.

## exhibit d — embedded quoth: the vocative exclamation

> "Why, Feather Flop," cried Mary Frances, "How you surprised me! I was so busy studying out Billy's plan for the garden—"

read closely, "Why, Feather Flop, how you surprised me!" is *one exclamation with a vocative* — so the quoth is embedded at a phrase position inside it:

```
turn
  exclamation
    phrase: why
    phrase: ^feather ^flop
    quoth
      phrase: cried ^mary ^frances
    phrase: how you surprised me
  broken
    phrase: ^i was so busy studying out ^billy*s plan for the garden
```

render walk:

```
“Why, Feather Flop,”             ← the phrases' natural commas fall where they always do; quote closes at the quoth
 cried Mary Frances,             ← embedded quoth: lowercase, comma — a phrase that steps outside the quotes (rule 2)
“how you surprised me!           ← quote re-opens; the exclamation continues lowercase to its own !
 I was so busy studying out Billy's plan for the garden—”
                                 ← broken: trailing — and no period; ^i, ^billy*s render I, Billy's
```

the 1916 source prints a capital "How" after the comma — loose period typography. the embedded model honors the author's *comma* as structure and normalizes only the *capital*, which costs nothing: capitalization is derived, never stored.

## exhibit e — embedded quoth: the resumed statement

> "I was listening," acknowledged Feather Flop, "and I don't approve of the plan at all."

one utterance-sentence, interrupted and resumed — the lowercase "and" in the source is the structural tell:

```
turn
  statement
    phrase: ^i was listening
    quoth
      phrase: acknowledged ^feather ^flop
    phrase: and ^i don't approve of the plan at all
```

render walk:

```
“I was listening,”               ← the phrase's own comma (not last in parent) lands inside the close-quote
 acknowledged Feather Flop,      ← embedded quoth: lowercase, comma (rule 2)
“and I don't approve of the plan at all.”
                                 ← the statement continues lowercase and closes with its own .
```

an earlier draft needed a `carry` kind for this; embedding made the resumption just *the rest of the sentence*. compare exhibit c, where the quoth stands between sentences and takes a period: the level is the meaning.

## exhibit f — the introducer (from chapter 23)

> At length he blurted out, "You told me, little Miss, I think, that fish-worms were good for the garden—"

```
turn
  quoth
    phrase: at length he blurted out
  broken
    phrase: you told me
    phrase: little ^miss
    phrase: ^i think
    phrase: that span(fish worms) were good for the garden
```

render walk:

```
At length he blurted out,        ← turn-initial introducer: opens the frame sentence, so it capitalizes;
                                   no quoted sentence precedes, so it takes a comma (rules 3, 5)
“You told me, little Miss, I think, that fish-worms were good for the garden—”
```

(chapter 2's colon-introducer — "as loudly as she dared:" — is a variant waiting in corpus-demands.)

## document shape and card granularity

chapter 4 as our first document:

```
taproot card         (bough: 1-64 grafts, body root last — here just [section graft])
  section card       (bough: banner graft first, then ~25 passage grafts)
    banner card      (leaf: title: chapter 4 · ^feather ^flop*s argument)
    passage cards    (leaf: one crown block each — a paragraph, with or without turns)
```

back-of-envelope: a slurp caps at 1920 sips (hwatu store design); an english word costs roughly 7-8 sips (petals + blossom kind + count + share of stem overhead), giving a ceiling of **~250 words per leaf card**. chapter 4's longest paragraph is ~80 words — comfortable — so **one block per passage card** stays the lean, pending real sip counts from the full annotation.

## open decisions

1. ~~does `turn` exist?~~ **decided 2026-07-18: yes** — dialogs and plays as first-class citizens; all truth is reached through dialog
2. ~~attribution stem name~~ **decided 2026-07-18: quoth** — and the citation kind renamed **quote → lift** (a passage lifted from another bed) to clear the neighborhood
3. ~~carry vs resume~~ **dissolved 2026-07-18** — the embedded-quoth model deleted both `carry` and `speech`: quoths sit at phrase or sentence positions and the punctuation derives from the level
4. ~~prop marking~~ **decided 2026-07-18** — props are marked even in derived-capital positions (`^i`, sentence-initial names); "I" is english's one prop pronoun
5. ~~terminal softening~~ **decided 2026-07-18** — the tree stores the statement; the comma is the turn's doing; only the meaningless mark softens
6. ~~passage-card granularity~~ **decided 2026-07-18: one crown block per leaf card** — try it and see; provisional until the full annotation's sip counts confirm
7. ~~the two dash kinds~~ **decided 2026-07-18: `pivot` and `broken`** — two kinds, not one: they are two meanings sharing one glyph, and merging by render glyph would be structure-by-typography. the pair encodes agency: *the speaker pivots; the world breaks.* broken needs no question/exclamation variants (interruption suspends illocution — there is only one kind of unfinished); quoth keeps phrase children (real tags have internal clause commas); paired asides are two consecutive pivots. (cut was rejected for its deletion ambiguity; swallow, charming, fails the clause-join case and collides with chapter 23's literal toad-swallowing.)
8. ~~crown~~ **decided 2026-07-18** — crown is the term; passage's crown set starts as paragraph | verse | list
