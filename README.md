# Lingo word base

Everything the [Lingo](https://github.com/amahlaka/lingo-hangman) games
practise lives here as JSON: words and phrases, fill-the-gap sentences,
grammar rules, words that get mixed up, word groups, pronunciation lessons,
short texts, and the list of languages.

**Adding or changing entries? Read [AUTHORING.md](AUTHORING.md) first.** It
says who the entries are for, how to write the English and Finnish, and how to
fill in levels and metadata. This repository holds only data; the app and its deploy pipeline
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
`level` (1, 2 or 3). Words, gap sentences and texts can have `categories`.

Optional metadata, for picking items and for the learner's statistics (the
allowed values are listed in [AUTHORING.md](AUTHORING.md)):

- `features`: what the item shows about English (`"th-sound"`,
  `"present-perfect"`, `"phrasal-verb"`…), any type.
- `contexts`: where a learner meets it (`"shopping"`, `"work"`…), any type.
- `register`: `"formal"` or `"informal"`; leave out for neutral.
- `ipa` (words): `{ "en": "/ˈwɑːt̬ɚ/", "en-GB": "/ˈwɔːtə/" }`, US and British IPA between slashes.
- `say` (words): `{ "fi": "wooter" }`, the English read the Finnish way.
- `pos` (words): part of speech, or `"phrase"` / `"sentence"`.
- `wordTypes` (sentences and rule examples): what each word is, one entry per
  word: `["article", "noun:subject", "verb", "adjective"]`.
- `note` (words and texts): `{ "en", "fi" }`, a trap for Finnish speakers. Text
in several languages is an object keyed by language code:
`{ "en": "...", "fi": "..." }`.

### `word`: a word or phrase and its translations

```json
{ "id": "bus-stop", "type": "word",
  "text": { "en": "bus stop", "fi": "bussipysäkki", "sv": "busshållplats" },
  "ipa": { "en": "/ˈbʌs stɑːp/", "en-GB": "/ˈbʌs stɒp/" }, "pos": "noun",
  "categories": ["commuting"], "features": ["compound"], "contexts": ["transport"], "level": 1,
  "examples": [{ "en": "I'll wait at the bus stop.", "fi": "Odotan bussipysäkillä." }] }
```

`text.en` (US English) is required. Add `en-GB` only when the British form
differs (`"color"` / `"colour"`); British English falls back to `en`. `time` (clock
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

Questions have up to four options. The item's own distractors are always
shown; free slots are filled at random from a `decoyPool` list, so the
options change from round to round. The pool is the item's own
`"decoyPool": ["he", "him", "she"]` or else its topic's (`decoyPool` on the
`rule` or `confusable` item). `"decoyPool": []` on an item turns its topic's
pool off. Pool words that are one of the item's answers, or already in the
sentence, are skipped, and at the start of a sentence they get a capital.

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
  the possible `sounds` as `[{ "id": "s", "label": { "en": "s", "fi": "s" } }]`
  and give each word its `sound` id. Learners pick the sound. The label is
  just the sound's name; the game adds "as in" and a word of the lesson with
  that sound, never the word being asked, so every sound needs at least two
  words (four or more gives variety).
- `spell`: put `[ ]` around the letters the learner picks (`ba[ck]`, `[sch]ool`)
  and list the spellings to choose from in `spellings` (`["ck", "k"]`); the
  bracketed letters must be one of them. Learners hear the word, see it with
  the letters left out and its Finnish meaning, and pick the spelling.
- `pairs`: words that sound alike, without brackets. Each word names the
  other word of its pair in `pair` (`ship` → `"pair": "sheep"`). Learners
  hear one and pick between the two.
- `letters`: each word is a letter (`"A"`, no brackets). Learners hear the
  letter name and pick the letter.

Every lesson needs at least three words. `say` is how a Finn would write the
sound (`naif`); `meaning` is the Finnish meaning; `note` (`{ "en", "fi" }`)
explains an exception. `speak` (optional) is what the voice reads instead of
the word, for a word a voice gets wrong. `ipa` (optional) is the word's IPA as
a string, like `"/naɪf/"`.

### `text`: several sentences that belong together

```json
{ "id": "at-the-cafe", "type": "text", "kind": "dialogue", "you": "Anna",
  "title": { "en": "At the café", "fi": "Kahvilassa" },
  "sentences": [
    { "speaker": "Server", "en": "Hi, what can I get you?", "fi": "Hei, mitä saisi olla?" },
    { "speaker": "Anna", "en": "Could I have a latte, please?", "fi": "Saisinko latten, kiitos?" }],
  "questions": [{ "question": { "en": "What does Anna drink?", "fi": "Mitä Anna juo?" },
    "answers": ["a latte"], "distractors": ["a tea", "an orange juice"] }],
  "categories": ["food"], "level": 1 }
```

`kind` is one of `dialogue` (every sentence names its `speaker`), `story`,
`message`, `email`, `notice`, `instructions` or `description`. For reading
and listening games; the sentences are also used as sentence material.
`questions` (optional) can be answered from the text; Read and understand asks
them. In a dialogue, `you` (optional) names the speaker the learner plays in
Your turn; without it they play the second speaker.

## Packs, categories and languages

A pack is `{ "id", "title": { "en", "fi" }, "items": [...] }` in
`packs/<id>.json`, and must be listed in `index.json` `packs`.

`index.json` `categories` gives each category its English and Finnish name
for the category chips. A category without one shows as written.

`index.json` `categoryGroups` sorts the categories under headings in the
focus area list: `[{ "id", "title": { "en", "fi" }, "categories": [...] }]`,
in the order shown. Each category belongs to one group; a category left out
is shown under "Other".

`languages.json` lists `{ "code", "label", "alphabet", "fallback" }`.
`alphabet` is the hangman keyboard's letters; `fallback` is the language to
use for words without their own text (only `en-GB` uses it).

The checks are in lingo-hangman's `src/lib/contentSchema.js`.
