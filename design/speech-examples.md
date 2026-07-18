# speech examples — chapter 4 annotated

worked examples applying the proposed speech stems to real sentences from chapter 4 of the mary frances garden book. vocabulary here is **provisional** — this doc exists so we can look at concrete trees before ruling on the open decisions at the bottom. when the decisions land, the kind definitions move to the spec proper and this doc is absorbed.

## notation

- lowercase word = a **neem** (petals spelled only where interesting)
- `^word` = a **prop** (renders with its leading capital)
- `word-word` = a **span** (renders its blossom children hyphen-joined)
- `'` in a word = **elide** petal; `*` = **possess** petal
- indentation = tree structure; `←` = commentary

## proposed kind roster for chapter 4

| kind | class | children | renders |
|:--|:--|:--|:--|
| paragraph | stem | sentence kinds, turns | block with terminal newline |
| **turn** ★ | stem | speeches and quoths, ≥1 speech | dialogue mechanics (below) |
| **speech** ★ | stem | sentence kinds | wrapped in `“ ”` |
| **quoth** ★ | stem | phrases | lowercase join to preceding speech |
| statement | stem | phrases, cuts | capitalized, terminal `.` |
| question | stem | phrases, cuts | capitalized, terminal `?` |
| exclamation | stem | phrases, cuts | capitalized, terminal `!` |
| **broken** ★ | stem | phrases, cuts | capitalized, terminal `—` (interrupted) |
| phrase | stem | blossoms, spans | trailing `,` if not last in parent |
| **cut** ★ | stem | blossoms, spans | trailing `—` instead of `,` |
| span | stem | neems, props | children joined by `-` |
| neem / prop | blossom | petals | word (prop capitalized) |

★ = new in this proposal. sentence kinds appear both as paragraph children (narration) and as speech children (dialogue) — the same interchangeability meta-pattern proposed for phrases-in-word-positions.

## the dialogue mechanics (turn's render rules)

1. each speech child renders wrapped in `“ ”`
2. a quoth joins the preceding speech with a space and **no capitalization** (props still capitalize themselves)
3. **terminal softening:** when the last sentence of a speech is a *statement* and a quoth follows, the statement's `.` renders as `,` — question, exclamation, and broken keep their marks
4. the quoth's own phrases join with commas; the turn closes the frame with `.`
5. a speech following a quoth re-opens quotes; its sentences capitalize per their own kinds

every rule is local to the turn — nothing outside a turn needs to know dialogue exists.

## exhibit a — the full dialogue sentence

> "Is he anywhere about?" inquired Feather Flop, looking around anxiously. "I thought I saw him go."

```
turn
  speech
    question
      phrase: is he anywhere about
  quoth
    phrase: inquired ^feather ^flop
    phrase: looking around anxiously
  speech
    statement
      phrase: i thought i saw him go
```

render walk:

```
“Is he anywhere about?”        ← question capitalizes itself, keeps its ? (rule 3: only statements soften)
 inquired Feather Flop,        ← quoth: lowercase join (rule 2); props capitalize anyway; comma between quoth phrases
 looking around anxiously.     ← turn closes the frame (rule 4)
“I thought I saw him go.”      ← second speech re-opens (rule 5)
```

## exhibit b — quoth is not narration

> "Caw-caw!" Feather Flop cleared his throat. "Caw-caw!"

```
paragraph
  turn
    speech
      exclamation
        phrase: span(caw caw)
  statement                     ← NOT a quoth: full narration sentence, capitalized, owns its period
    phrase: ^feather ^flop cleared his throat
  turn
    speech
      exclamation
        phrase: span(caw caw)
```

this is the case that decides whether `turn` earns its place. the source distinguishes "cleared his throat" (capital F, own period — independent narration) from "inquired feather flop" (lowercase, comma-bound — attribution). without a turn, both would sit as loose paragraph children and the renderer would need order-sensitive heuristics to recover which is which — and we would still need quoth as a distinct kind to mark the bound fragments. the turn makes the binding *structural*: what belongs to the dialogue sentence is inside it; what stands alone stands outside.

## exhibit c — terminal softening + elide + continuation

> "Yes, he's gone, Feather Flop," laughed Mary Frances. "But let me show you—he has been planning such a delightful garden for me."

```
turn
  speech
    statement
      phrase: yes
      phrase: he's gone            ← neem "he's" = petals h·e·elide·s
      phrase: ^feather ^flop       ← vocative phrase
  quoth
    phrase: laughed ^mary ^frances
  speech
    statement
      cut: but let me show you     ← renders trailing — instead of ,
      phrase: he has been planning such a delightful garden for me
```

render walk, showing softening (rule 3):

```
“Yes, he's gone, Feather Flop,”   ← the statement's stored terminal is `.` — a quoth follows, so it RENDERS `,`
 laughed Mary Frances.            ← quoth lowercase; turn closes the frame
“But let me show you—he has been planning such a delightful garden for me.”
                                  ← cut emits the —; statement keeps its . (no quoth follows)
```

the tree *stores* a statement; the comma is the turn's doing at render time. ingest runs the same rule backwards: quoted material ending in `,` followed by a lowercase said-verb ⇒ recover a statement inside a turn.

## exhibit d — broken + possess

> "Why, Feather Flop," cried Mary Frances, "How you surprised me! I was so busy studying out Billy's plan for the garden—"

```
turn
  speech
    statement
      phrase: why
      phrase: ^feather ^flop
  quoth
    phrase: cried ^mary ^frances
  speech
    exclamation
      phrase: how you surprised me
    broken                          ← interrupted: renders terminal — and no period
      phrase: i was so busy studying out ^billy*s plan for the garden
```

renders: softening turns the first statement's terminal to `,` … quoth … then `“How you surprised me! I was so busy studying out Billy's plan for the garden—”` — the broken supplies the trailing `—`, and `^billy*s` renders "Billy's".

(the cut also covers mid-sentence self-repair: "your silly—I mean your brother's, plan" = `cut: your silly` · `phrase: i mean your brother*s` · `phrase: plan`.)

## document shape and card granularity

chapter 4 as our first document:

```
taproot card         (bough: head [] · body [section graft])
  section card       (bough: head [banner graft] · body [~25 passage grafts])
    banner card      (leaf: title: chapter 4 · ^feather ^flop*s argument)
    passage cards    (leaf: one block each — a paragraph or turn-bearing paragraph)
```

back-of-envelope: a slurp caps at 1920 sips (store.md); an english word costs roughly 7-8 sips (petals + blossom kind + count + share of stem overhead), giving a ceiling of **~250 words per leaf card**. chapter 4's longest paragraph is ~80 words — comfortable — but multi-paragraph passages would flirt with the cap. this nudges toward **one block per passage card** (paragraph-scale cards), which also gives splice and reference their natural grain. proposed, not decided.

## open decisions

1. does `turn` exist? (exhibit b is the argument for; the alternative is loose children + order-sensitive render heuristics, plus a quoth-like kind anyway)
2. name for the attribution stem: **quoth** (unmistakable, archaic-charming, zero markup collision) vs **said** (plain, reads naturally in trees) vs **attrib** (sober)
3. terminal softening as a pure render rule — comfortable after exhibits a/c?
4. passage-card granularity: one block per leaf card?
5. `broken` and `cut` as names for the two dash kinds
