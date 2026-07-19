# chapter 4 annotated — the acid test

the full text of chapter 4 of the mary frances garden book, hand-annotated into the decided kinds ([speech-examples.md](speech-examples.md) notation). two purposes: verify the grammar reproduces the printed page, and measure real card sizes against the slurp cap for the one-crown-block-per-card decision.

**method:** phrase boundaries follow the source's commas; capitalization is never stored (all caps derive); normalizations are flagged `⚑`. sip estimates use `~68 + 9 × words` (68 = schema node + 64-petal bloom + card root overhead; ~9/word = ~5 petals + 2 blossom sips + ~2 share of stem sips) — real counts come from the encoder, these bound the shape.

**wrinkles found (review these first):** card 15 (extended-action quoth), card 21 (echoed exclamation caps), the banner (first quant sighting + chapter-number derivability).

## document shape

```
taproot card       bough: [ section graft ]                       (~80 sips)
  section card     bough: [ banner graft, 21 passage grafts ]     (~140 sips)
    banner card    title: chapter quant(4) ^feather ^flop*s argument   (~110 sips)
    passage cards  1-21 below
```

⚑ banner notes: the chapter number is our first **quant** — trivially a 1-petal quant. renders "Chapter 4. Feather Flop's Argument" via the chapter-banner convention ("Chapter N. "). noticing: when the whole book assembles, chapter numbers become *derivable from graft position* — like the contents page, stored numbering may dissolve into structure.

## the cards

### card 1 — narration (~310 sips)

> NEITHER of the children had noticed the head of the big rooster as he peered curiously through the curtained window of the play house while they were talking.

```
paragraph
  statement
    phrase: neither of the children had noticed the head of the big rooster as he peered curiously through the curtained window of the play house while they were talking
```

⚑ opener caps "NEITHER" normalize — only the derived sentence-initial capital survives.

### card 2 — narration (~365 sips)

> As Mary Frances came out of the door, Feather Flop walked around the corner of the house. The little girl was so absorbed in looking at the plan that she did not see the rooster.

```
paragraph
  statement
    phrase: as ^mary ^frances came out of the door
    phrase: ^feather ^flop walked around the corner of the house
  statement
    phrase: the little girl was so absorbed in looking at the plan that she did not see the rooster
```

### card 3 — quoth is not narration (~150 sips)

> "Caw-caw!" Feather Flop cleared his throat. "Caw-caw!"

```
paragraph
  turn
    exclamation
      phrase: caw-caw               ← one neem, beat petal at the join
  statement
    phrase: ^feather ^flop cleared his throat
  turn
    exclamation
      phrase: caw-caw
```

### card 4 — embedded quoth in a vocative exclamation (~265 sips)

> "Why, Feather Flop," cried Mary Frances, "How you surprised me! I was so busy studying out Billy's plan for the garden—"

```
paragraph
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

⚑ source's capital "How" derives lowercase (mid-sentence continuation); the author's comma is honored as structure.

### card 5 — the full dialogue sentence (~210 sips)

> "Is he anywhere about?" inquired Feather Flop, looking around anxiously. "I thought I saw him go."

```
paragraph
  turn
    question
      phrase: is he anywhere about
    quoth
      phrase: inquired ^feather ^flop
      phrase: looking around anxiously
    statement
      phrase: ^i thought ^i saw him go
```

### card 6 — softening + pivot (~295 sips)

> "Yes, he's gone, Feather Flop," laughed Mary Frances. "But let me show you—he has been planning such a delightful garden for me."

```
paragraph
  turn
    statement
      phrase: yes
      phrase: he's gone
      phrase: ^feather ^flop
    quoth
      phrase: laughed ^mary ^frances
    statement
      pivot: but let me show you
      phrase: he has been planning such a delightful garden for me
```

### card 7 — the echo retort (~140 sips)

> "Delightful!" shrilled Feather Flop. "Delightful! I don't think so."

```
paragraph
  turn
    exclamation
      phrase: delightful
    quoth
      phrase: shrilled ^feather ^flop
    exclamation
      phrase: delightful
    statement
      phrase: ^i don't think so
```

### card 8 — double question, turn-final quoth (~200 sips)

> "Why, what makes you say that? How do you know what he planned?" inquired Mary Frances.

```
paragraph
  turn
    question
      phrase: why
      phrase: what makes you say that
    question
      phrase: how do you know what he planned
    quoth
      phrase: inquired ^mary ^frances
```

### card 9 — repetition + pivot join (~230 sips)

> "I heard every word, every word," said the rooster. "Of course you didn't see me—I was peeping in the window."

```
paragraph
  turn
    statement
      phrase: ^i heard every word
      phrase: every word
    quoth
      phrase: said the rooster
    statement
      pivot: of course you didn't see me
      phrase: ^i was peeping in the window
```

### card 10 — the scandalized gasp (~140 sips)

> "Oh, Feather Flop!" cried Mary Frances. "Were you eaves-dropping?"

```
paragraph
  turn
    exclamation
      phrase: oh
      phrase: ^feather ^flop
    quoth
      phrase: cried ^mary ^frances
    question
      phrase: were you eaves-dropping
```

### card 11 — the resumed statement (~185 sips)

> "I was listening," acknowledged Feather Flop, "and I don't approve of the plan at all."

```
paragraph
  turn
    statement
      phrase: ^i was listening
      quoth
        phrase: acknowledged ^feather ^flop
      phrase: and ^i don't approve of the plan at all
```

### card 12 — (~175 sips)

> "Why, what's wrong with it?" asked Mary Frances. "I think it's beautiful."

```
paragraph
  turn
    question
      phrase: why
      phrase: what's wrong with it
    quoth
      phrase: asked ^mary ^frances
    statement
      phrase: ^i think it's beautiful
```

### card 13 — parallel exclamations (~140 sips)

> "It's not sensible!" said Feather Flop. "It's not useful!"

```
paragraph
  turn
    exclamation
      phrase: it's not sensible
    quoth
      phrase: said ^feather ^flop
    exclamation
      phrase: it's not useful
```

### card 14 — the bare turn, no quoth (~175 sips)

> "But it seems perfect to me. How would you change it, Feather Flop?"

```
paragraph
  turn
    statement
      phrase: but it seems perfect to me
    question
      phrase: how would you change it
      phrase: ^feather ^flop
```

### card 15 — ⚑ the extended-action quoth (~437 sips)

> "Nobody can eat flowers!" exclaimed Feather Flop. "See here," he looked over Mary Frances' shoulder as she sat down on the bench, and pointed with his claw, "that plan fills the entire front yard with bloomin' plants and gives only the little back yard for such things as taste good!"

```
paragraph
  turn
    exclamation
      phrase: nobody can eat flowers
    quoth
      phrase: exclaimed ^feather ^flop
    exclamation
      phrase: see here
      quoth
        phrase: he looked over ^mary ^frances* shoulder as she sat down on the bench
        phrase: and pointed with his claw
      phrase: that plan fills the entire front yard with bloomin' plants and gives only the little back yard for such things as taste good
```

⚑ three notables: the embedded quoth is *pure narrative action* — no said-verb at all — and the model absorbs it (dialogue convention treats extended action as an extended tag); `^mary ^frances*` is an s-final possessive (possess renders bare `'` after s: "Mary Frances' shoulder"); `bloomin'` is a word-final elide. the chapter's biggest card at ~437 sips — 23% of the 1920 cap.

### card 16 — (~210 sips)

> "Dearie me! Dearie me!" laughed Mary Frances. "Is that it, Feather Flop? Why, don't you love to see beautiful flowers?"

```
paragraph
  turn
    exclamation
      phrase: dearie me
    exclamation
      phrase: dearie me
    quoth
      phrase: laughed ^mary ^frances
    question
      phrase: is that it
      phrase: ^feather ^flop
    question
      phrase: why
      phrase: don't you love to see beautiful flowers
```

### card 17 — softening before a two-phrase quoth (~265 sips)

> "Not half as much as I do to eat beautiful lettuce and beet tops and other beautiful vegetables," declared Feather Flop, shaking his head sadly.

```
paragraph
  turn
    statement
      phrase: not half as much as ^i do to eat beautiful lettuce and beet tops and other beautiful vegetables
    quoth
      phrase: declared ^feather ^flop
      phrase: shaking his head sadly
```

### card 18 — embedded quoth with action, resumed statement (~285 sips)

> "It's too bad, Feather Flop," said Mary Frances, smoothing his fine feathers, "but I'll see that you get plenty of such green things as you like."

```
paragraph
  turn
    statement
      phrase: it's too bad
      phrase: ^feather ^flop
      quoth
        phrase: said ^mary ^frances
        phrase: smoothing his fine feathers
      phrase: but ^i'll see that you get plenty of such green things as you like
```

### card 19 — the pivot (~250 sips)

> "Oh, thank you, little Miss," said the rooster. "If you will do that, I'm ready to help with your silly—I mean your brother's, plan."

```
paragraph
  turn
    statement
      phrase: oh
      phrase: thank you
      phrase: little ^miss
    quoth
      phrase: said the rooster
    statement
      phrase: if you will do that
      pivot: ^i'm ready to help with your silly
      phrase: ^i mean your brother*s
      phrase: plan
```

### card 20 — embedded quoth, then a fresh sentence in the same run (~295 sips)

> "Thank you, Feather Flop, for all your help," said the little girl, "and good-bye for now. I must go or maybe mother will send Billy to look for me."

```
paragraph
  turn
    statement
      phrase: thank you
      phrase: ^feather ^flop
      phrase: for all your help
      quoth
        phrase: said the little girl
      phrase: and good-bye for now
    statement
      phrase: ^i must go or maybe mother will send ^billy to look for me
```

### card 21 — ⚑ the echoed exclamation (~200 sips)

> "Good-bye! good-bye!" cried Feather Flop, jumping off the bench and running away as fast as possible.

```
paragraph
  turn
    exclamation
      phrase: good-bye
    fade
      exclamation
        phrase: good-bye
    quoth
      phrase: cried ^feather ^flop
      phrase: jumping off the bench and running away as fast as possible
```

⚑ the source prints the second echo lowercase ("Good-bye! good-bye!") — a trailing-off repeat. capitalization still cannot be stored — instead **fade** (provisional, 2026-07-18) stores the *meaning*: a quieter repeat, third member of the emphasis set (fade < plain < stress < shout), rendering with its initial capital suppressed. the render now reproduces the source **verbatim**. structural mechanism (wrapper stem, as here, vs sentence-variant) settles in the ch2 prosody session.

(the chapter's trailing `---` is the chapter card's render convention — the markdown renderer emits a horizontal rule at chapter ends, or a different style might not; never stored.)

## measurements

| | |
|:--|:--|
| cards | 24 (taproot + section + banner + 21 passages) |
| largest passage card | card 15, ~437 sips — **23% of the 1920-sip slurp cap** |
| median passage card | ~210 sips (~11% of cap) |
| smallest | ~140 sips |
| verdict | **one crown block per leaf card is comfortably validated** — ~4× headroom on the chapter's biggest paragraph; nothing within sight of the cap |

## grammar verdict

every construct in chapter 4 is covered by the decided kinds: paragraph, turn, quoth (sentence-level, embedded, extended-action), statement, question, exclamation, broken, phrase, pivot, neem, prop, beat, elide, possess — plus one trivial quant in the banner. **zero constructs required a kind we don't have — and zero spans**: every ch4 compound is homogeneous, a single neem with beat joins (span waits for kind-mixers like the preface's "Jack-in-the-Pulpit"). one construct *invited* a kind: the diminuendo echo became **fade** (card 21, provisional). the remaining wrinkles were all render-convention questions, not grammar gaps.
