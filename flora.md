# Flora

The flora of hexdown: a field guide to the **kinds** of document node — every blossom and every stem the core ships, each with its children and its render rules. Where [data-model.md](data-model.md) defines the node *classes* (what a blossom is, structurally), this catalog defines the *kinds* (which blossoms exist, and what each one means). Trellises select from this flora; they do not define new species — see [schemas.md](schemas.md).

This is a reference: consult it when you meet an unfamiliar kind rather than reading it straight through. Kinds still under discussion live in [design/](design/) (currently: turn, speech, quoth, broken, cut — see [design/speech-examples.md](design/speech-examples.md)) and are cataloged here once decided.

The metastructure's reserved kinds (pad, schema node, neem, graft, bloom) are defined in [encoding.md](encoding.md); this catalog covers content kinds — including neem, the one kind with dual citizenship: reserved by the metastructure, cataloged here as content. Structurally, blossom kinds occupy the blossom family (high-bit kind values, children are petals) and stem kinds the stem family (children are nodes).

## Blossoms

A blossom's kind determines how its petal children are interpreted:

- inside a **neem** or **prop**: a phoneme
- inside a **quant**: a digit, mantissa bit-group, exponent bit-group, or precision marker (kind-specific layout)
- inside an **enum**: an ordinal or range marker
- inside a **unipoint**: a bit-group of a 32-bit unicode codepoint

The full sip → phoneme/glyph mapping for phonetic petals lives in [encoding.md](encoding.md).

- **neem** — a phonetic word or sub-word; petals are phonemes under the core phoneme mapping. Reserved at `b111101` — the one universal content kind: prose words and metaschema names are both neems.
- **prop** — a phonetic word with leading-capital marking, used for proper nouns. Same petal interpretation as neem; the kind distinguishes capitalization structurally rather than encoding it in the petals. Context-driven capitalization (sentence-initial, title-case) is applied by the parent stem at render time, not by the leaf — only proper-noun capitalization is structural.
- **quant** — a numeric quantity. Petals encode (mantissa, exponent, precision) under a kind-specific layout. (Detailed design deferred.)
- **enum** — an integer enumeration within a range. Petals encode (ordinal, range) under a kind-specific layout. (Detailed design deferred.)
- **uniglyph** — a unicode word or grapheme. Contains a sequence of unipoint inner nodes, each of which is a fixed-arity inner node with 6 petal children encoding a 32-bit unicode codepoint. Used for content outside hexdown's native phoneme/numeric alphabets — other writing systems, emoji, math symbols. (Detailed design deferred.)

## Word-scale and sentence-scale stems

- **span** — combines neems, props, quants, enums, and uniglyphs into a single compound word (e.g., "Mt-Rainier" as prop + neem; "DNA" as a sequence of uniglyphs); renders its children hyphen-joined.
- **phrase** — combines blossoms and spans into a clause; rendered with a comma if not last within its parent.
- **statement** — combines phrases into a sentence; renders first-letter-capitalized with a terminating period.
- **question** — like statement but ends in `?`.
- **exclamation** — like statement but ends in `!`.
- **title** — phrase-like stem rendered with title-case capitalization on every word.

## Block-scale stems (draft)

From the passage-trellis draft; content models provisional:

- **paragraph** — sequence containing phrases, statements, questions, notes, quotes, lists, points
- **list** — sequence of (neem, quant, code, handle, socket, wiki, hyper, quote, figure) elements
- **point** — single (neem, quant, mark, handle, socket, wiki, hyper, quote, figure) element
- **quote** — mark + offset + breadth
- **note** — sequence containing phrases, statements, questions, quotes

(These draft models name kinds not yet cataloged — code, mark, handle, socket, wiki, hyper, figure; their definitions are open work.)

## Open questions

- Whether `prop` should be a separate blossom kind or a structural flag on neem (current lean: separate kind, for structural cleanliness).
- The exact unipoint / uniglyph design — bit layout within a unipoint's 6 petal children, what marker bits go where, whether uniglyph is variable-length or has internal substructure. (Detailed design deferred.)
- Whether quant carries SI-unit information; if so, where (additional sip field, sibling node, trellis-level, ...?). (Deferred.)
- Exact bit allocation within quant (mantissa / exponent / precision split). (Deferred.)
- Whether enum ranges are fixed by the trellis or carried per-blossom. (Deferred.)
- Whether sentence-level stems should always wrap their content in phrase children (current lean: yes, for symmetry).
- Formal definitions of each kind, the legal nesting, and how each maps to CommonMark constructs.
- Which kinds may serve as a leaf card's card root (current lean: the block-scale kinds — paragraph | list | verse); to be validated by the chapter-4 annotation.
- The uncataloged kinds named by the draft block models: code, mark, handle, socket, wiki, hyper, figure.
