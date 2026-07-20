# Flora

The flora of hexdown: a field guide to the **kinds** of document node — every blossom and every stem the core ships, each with its children and its render rules. Where [data-model.md](data-model.md) defines the node *classes* (what a blossom is, structurally), this catalog defines the *kinds* (which blossoms exist, and what each one means). Trellises select from this flora; they do not define new species — see [schemas.md](schemas.md).

This is a reference: consult it when you meet an unfamiliar kind rather than reading it straight through. The speech-session kinds — turn, quoth, pivot, broken — were decided 2026-07-18 and are cataloged below; the worked exhibits live in [design/speech-examples.md](design/speech-examples.md) until that doc is absorbed.

The metastructure's reserved kinds (pad, schema node, neem, graft, bloom) are defined in [encoding.md](encoding.md); this catalog covers content kinds — including neem, the one kind with dual citizenship: reserved by the metastructure, cataloged here as content. Structurally, kinds occupy four families by their leading bits — stems hold nodes, boughs hold only grafts, blossoms hold petals, and the null kind pads (see [glyphs.md](glyphs.md)).

## Blossoms

A blossom's kind determines how its petal children are interpreted:

- inside a **neem** or **prop**: a phoneme
- inside a **quant**: a digit, mantissa bit-group, exponent bit-group, or precision marker (kind-specific layout)
- inside an **enum**: an ordinal or range marker
- inside a **unipoint**: a bit-group of a 32-bit unicode codepoint

The full sip → phoneme/glyph mapping for phonetic petals lives in [encoding.md](encoding.md).

- **neem** — a phonetic word or sub-word; petals are phonemes under the core phoneme mapping, plus the small marks: beat joins homogeneous compounds within a single neem (`to-day`, `caw-caw`), elide marks omission (`haven't`), possess marks the genitive (`flop*s`). Reserved at `0o74` — the one universal content kind: prose words and metaschema names are both neems.
- **prop** — a phonetic word with leading-capital marking, used for proper nouns. Same petal interpretation as neem; the kind distinguishes capitalization structurally rather than encoding it in the petals. Context-driven capitalization (sentence-initial, title-case) is applied by the parent stem at render time, not by the leaf — only proper-noun capitalization is structural. Props are marked even where position would capitalize anyway: prop-ness is semantic, not positional. The pronoun "I" is prop-marked — english's one prop pronoun.
- **quant** — a numeric quantity. Petals encode (mantissa, exponent, precision) under a kind-specific layout. (Detailed design deferred.)
- **enum** — an integer enumeration within a range. Petals encode (ordinal, range) under a kind-specific layout. (Detailed design deferred.)
- **uniglyph** — a unicode word or grapheme. Contains a sequence of unipoint inner nodes, each of which is a fixed-arity inner node with 6 petal children encoding a 32-bit unicode codepoint. Used for content outside hexdown's native phoneme/numeric alphabets — other writing systems, emoji, math symbols. (Detailed design deferred.)

## Word-scale and sentence-scale stems

- **span** — combines blossoms of *different* kinds into a single compound word ("Mt-Rainier" as prop + neem; "Jack-in-the-Pulpit" as prop + neem + neem + prop); renders its children hyphen-joined. Homogeneous compounds need no span — they are a single blossom with beat petals at the joins (decided 2026-07-18). (The old "DNA as uniglyphs" example is suspect — initialisms are unhyphenated — and defers with uniglyph.)
- **phrase** — combines blossoms and spans into a clause; rendered with a comma if not last within its parent.
- **pivot** — a phrase whose trailing joint renders `—` instead of `,`: the speaker's own mid-sentence redirect — self-repair ("your silly—I mean your brother's, plan"), an abrupt join, or a paired aside (two consecutive pivots). The speaker pivots; the world breaks.
- **statement** — combines phrases, pivots, and embedded quoths into a sentence; renders first-letter-capitalized with a terminating period.
- **question** — like statement but ends in `?`.
- **exclamation** — like statement but ends in `!`.
- **broken** — the fourth sentence kind: a sentence interrupted before completion, rendering capitalized with terminal `—` and no stop. No question or exclamation variants — interruption suspends illocution; there is only one kind of unfinished.
- **title** — phrase-like stem rendered with title-case capitalization on every word.

## Dialogue stems

- **turn** — the dialogue stem: a paragraph child holding sentence kinds and quoths (≥1 sentence). Quoted material is never stored — the renderer wraps each maximal quoth-free run in `“ ”`, closing at each quoth and re-opening after. The turn owns the dialogue mechanics: terminal softening (a statement's `.` renders `,` before a sentence-level quoth — only the meaningless mark softens), quoth punctuation by position, and derived capitalization.
- **quoth** — the attribution stem: holds phrases (real tags have internal clause commas — "inquired feather flop, looking around anxiously"). Admitted at sentence positions (trailing `.`, or `,` when it opens the turn as an introducer) and embedded at phrase positions inside a sentence (trailing `,`; the sentence continues lowercase after it). Renders lowercase except turn-initial. The level is the meaning.

## Block-scale stems (draft)

From the passage-trellis draft; content models provisional:

- **paragraph** — sequence of sentence kinds and turns (draft: may also admit notes and lifts inline)
- **list** — sequence of (neem, quant, code, handle, socket, wiki, hyper, lift, figure) elements
- **point** — single (neem, quant, mark, handle, socket, wiki, hyper, lift, figure) element
- **lift** — mark + offset + breadth: a passage lifted from another source (renamed from *quote* 2026-07-18, clearing quoth's neighborhood — lifting from another bed is transplanting imagery)
- **note** — sequence containing phrases, statements, questions, lifts

(These draft models name kinds not yet cataloged — code, mark, handle, socket, wiki, hyper, figure; their definitions are open work.)

## Open questions

- Whether `prop` should be a separate blossom kind or a structural flag on neem (current lean: separate kind, for structural cleanliness).
- The exact unipoint / uniglyph design — bit layout within a unipoint's 6 petal children, what marker bits go where, whether uniglyph is variable-length or has internal substructure. (Detailed design deferred.)
- Whether quant carries SI-unit information; if so, where (additional sip field, sibling node, trellis-level, ...?). (Deferred.)
- Content dates and times — publication years, historical time, eras before 1970 and BCE: a quant layout or a date blossom with honest calendar apparatus (precision, ranges, eras). Unrelated to flush stamps, which clock orchard events only. (Deferred with quant.)
- Exact bit allocation within quant (mantissa / exponent / precision split). (Deferred.)
- Whether enum ranges are fixed by the trellis or carried per-blossom. (Deferred.)
- Whether sentence-level stems should always wrap their content in phrase children (current lean: yes, for symmetry).
- Formal definitions of each kind, the legal nesting, and how each maps to CommonMark constructs.
- Leaf card roots: decided 2026-07-18 — the **crown** kinds, starting as paragraph | verse | list; each leaf trellis declares its crowns. To be validated and possibly extended by the chapter-4 annotation.
- The uncataloged kinds named by the draft block models: code, mark, handle, socket, wiki, hyper, figure.
