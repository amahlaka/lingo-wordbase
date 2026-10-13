# Lingo word base

Everything the [Lingo](https://github.com/amahlaka/lingo-hangman) games
practise lives here as JSON: words and phrases, fill-the-gap sentences,
grammar rules, words that get mixed up, word groups, pronunciation lessons,
and the list of languages. This repository holds only data; the app and its deploy pipeline
live in lingo-hangman, so access to edit words never gives access to code.

```
  index.json        which packs are used, in order, and category names
  languages.json    languages words can be translated into
  packs/<id>.json   the items, grouped into packs by theme
```

## Editing without a pull request

Open **/admin** on the site (for example https://lingo.bololoo.com/admin). It
lists every pack, lets you add or edit items with a form, shows what is
missing as you type, and keeps unpublished changes on your device.

**Publish** writes the changed files to `master` of this repository as one
commit through the GitHub API. The **Content publish** workflow in
lingo-hangman checks this repository every 10 minutes; when `master` has
moved it validates the files and copies them to the live site. Visitors get
the new words the next time they open the app; no release is needed. To
publish at once, run Content publish by hand in lingo-hangman's Actions tab.

Publishing needs a GitHub fine-grained access token with **only this
repository** selected and **Contents: read and write** (leave every other
permission off). Paste it under *GitHub access* on the admin page; it is
stored only in that browser. If the files changed on GitHub since the page
loaded, publishing stops instead of overwriting them; reload and redo the
change.

You can also edit the files directly on GitHub. Broken files never reach the
live site: Content publish stops at the check and the site keeps the last
good version. To check locally, run `npm run content:check` in a
lingo-hangman checkout (it downloads this repository into its `content/`
folder with `npm run content:pull`); `npm run content:format` rewrites packs
into the standard layout (one item per line).

## How the app gets the content

- Each app release downloads this repository's `master` and builds it in,
  and also publishes it at `/content/...` beside the app.
- At startup the app fetches `/content/index.json`, `languages.json` and the
  listed packs. If that fails or takes more than 2.5 seconds (offline, for
  instance) it uses the built-in copy.
- On the live site nginx serves `/content/` from `/srv/lingo-prod/content`
  (where Content publish puts it) and falls back to the live release's own
  copy. Previews use the copy they were built with.
- Items that fail the checks are skipped with a console warning rather than
  breaking a game.

## Items

Every item has an `id` (lower case, numbers and dashes, unique across all
packs; keep it once it is live) and a `type`. Optional on every type:
`level` (1, 2 or 3). Words and gap sentences can have `categories`. Text
in several languages is an object keyed by language code:
`{ "en": "...", "fi": "..." }`.

### `word`: a word or phrase and its translations

```json
{ "id": "bus-stop", "type": "word",
  "text": { "en": "bus stop", "fi": "bussipysäkki", "sv": "busshållplats" },
  "categories": ["commuting"],
  "examples": [{ "en": "I'll wait at the bus stop.", "fi": "Odotan bussipysäkillä." }] }
```

`text.en` is required. Add `en-GB` only when the British spelling differs
(`"color"` / `"colour"`); British English falls back to `en`. `time` (clock
phrases only) lists the times it describes, like `["09:00", "21:00"]`.
`examples` are whole sentences using the word; games use them as sentence
material.

### `gap`: a sentence with one missing word

```json
{ "id": "borrow-lend-1", "type": "gap", "lang": "en",
  "sentence": "Can I ___ your umbrella?",
  "answers": ["borrow"], "distractors": ["lend"],
  "translation": { "fi": "Voinko lainata sateenvarjoasi?" },
  "explanation": { "en": "...", "fi": "..." },
  "topic": "borrow-lend" }
```

`___` marks the gap, exactly once. The first answer is shown as the right
one; more answers are also accepted. Distractors must really be wrong in this
sentence. `""` in either list means "no word" (no article, for example).
`topic` links the sentence to a `rule` or `confusable` item, whose
explanation the game can show.

### `rule`: a grammar rule

```json
{ "id": "a-or-an", "type": "rule",
  "title": { "en": "a or an", "fi": "a vai an" },
  "explanation": { "en": "...", "fi": "..." },
  "examples": [{ "en": "an hour", "fi": "tunti" }] }
```

### `confusable`: words that get mixed up

```json
{ "id": "borrow-lend", "type": "confusable",
  "title": { "en": "borrow or lend", "fi": "lainata: borrow vai lend" },
  "words": [{ "text": "borrow", "meaning": { "fi": "lainata joltakulta" } }, { "text": "lend" }],
  "explanation": { "en": "...", "fi": "..." } }
```

Each word can have a `meaning` and a `note`.

### `group`: small words that can replace each other

```json
{ "id": "be-forms", "type": "group",
  "title": { "en": "Forms of be", "fi": "Be-verbin muodot" },
  "words": ["is", "are", "am", "was", "were"] }
```

Games use the rest of a group as wrong options when the answer is in it.

### `sound`: a pronunciation lesson

```json
{ "id": "silent-k", "type": "sound", "kind": "silent",
  "title": { "en": "Silent k in kn", "fi": "Äänetön k: kn" },
  "explanation": { "en": "...", "fi": "..." },
  "words": [{ "text": "[k]nife", "say": { "fi": "naif" }, "meaning": { "fi": "veitsi" } }] }
```

`kind` decides the question the Sounds of English game asks:

- `silent`: put `[ ]` around the letters you don't say (`[k]nife`,
  `ni[gh]t`). Learners tap the letter they don't hear.
- `sound`: put `[ ]` around the letters whose sound changes (`[c]ity`), list
  the possible `sounds` as `[{ "id": "s", "label": { "en": "s, as in sun", "fi": "..." } }]`
  and give each word its `sound` id. Learners pick the sound.
- `letters`: each word is a letter (`"A"`, no brackets). Learners hear the
  letter name and pick the letter.

Every lesson needs at least three words. `say` is how a Finn would write the
sound (`naif`); `meaning` is the Finnish meaning; `note` (`{ "en", "fi" }`)
explains an exception. `speak` (optional) is what the voice reads instead of
the word, for a word a voice gets wrong.

## Packs, categories and languages

A pack is `{ "id", "title": { "en", "fi" }, "items": [...] }` in
`packs/<id>.json`, and must be listed in `index.json` `packs`.

`index.json` `categories` gives each category its English and Finnish name
for the category chips. A category without one shows as written.

`languages.json` lists `{ "code", "label", "alphabet", "fallback" }`.
`alphabet` is the hangman keyboard's letters; `fallback` is the language to
use for words without their own text (only `en-GB` uses it).

The checks are in lingo-hangman's `src/lib/contentSchema.js`.
