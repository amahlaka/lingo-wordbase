# Writing word base entries

Instructions for anyone, person or AI agent, adding or changing entries. The
file format itself is in [README.md](README.md); this file is about what to
write and how to fill in the metadata. Checks live in lingo-hangman's
`src/lib/contentSchema.js` (`npm run content:check` there); the lists of
allowed values below are copied from it and must stay in sync.

## Who the entries are for

Finnish-speaking adults who understand some English but lack confidence.
Write for real adult life in Finland and abroad: work, shops, travel, the
doctor, small talk, messages. Avoid childish topics, slang that dates fast,
and anything political, religious, sexual or about violence.

- **English** is US (American) English: US spelling (color, center,
  traveled) and US words (apartment, elevator, cell phone, vacation). Add
  `en-GB` only when the British form differs (`"en": "color", "en-GB":
  "colour"`); British English falls back to `en`.
  Learners only ever see the US form, so notes, explanations and texts teach
  US English and never mention British words, spellings or pronunciations:
  two ways of saying one thing confuse learners.
- **Finnish** is natural standard Finnish (yleiskieli), the way an adult would
  say it, not word-for-word. Use the spoken form only in a dialogue between
  friends, and then mark the item `register: "informal"`.
- New entries need only `en` and `fi`. Leave other languages out rather than
  guessing.
- Keep sentences short: up to about 12 words for level 1-2, up to about 20 for
  level 3. One idea per sentence.
- Never invent facts about real companies or people. Use common Finnish and
  English first names (Anna, Mikko, Laura, Tom, Sarah, Jussi) for characters.

## Before adding

1. Search every pack for the English text (case-insensitive) so nothing is
   added twice. An entry that exists may only need metadata.
2. Pick the pack by theme (see "Packs" below). Add a new pack only for a new
   theme of at least about 20 items, and list it in `index.json`.
3. Give the item an `id` made from its English text (`bus-stop`,
   `where-is-the-bus-stop`). Ids are permanent once live: never rename one.

## Level

`level` is how hard the item is for this audience, not for native speakers.

| level | meaning | examples |
|---|---|---|
| 1 | everyday basics, short and common | water, thank you, Where is the toilet? |
| 2 | common but longer, less basic, or with one tricky point | receipt, I'm looking for the station., borrow / lend |
| 3 | longer sentences, idioms, tricky grammar, clock times | quarter to eight, If I had known, I would have come. |

Raise the level by one when the item has a feature Finnish speakers find
hard (articles in a fixed phrase, th, present perfect, phrasal verbs).

## Metadata

All metadata is optional for the checks, but **new `word` items should have
`ipa`, `pos`, `features` (when any apply) and `contexts`**, and new gap and
text items `features` and `contexts`.

### `ipa` (word items)

`{ "en": "/ˈwɑːt̬ɚ/", "en-GB": "/ˈwɔːtə/" }`: US IPA under `en`, and British
(Received Pronunciation) IPA under `en-GB`, between slashes, as in the
Cambridge Learner's Dictionary. Give `en` to every word with IPA; `en-GB` is
optional. Mark primary stress with ˈ before the stressed syllable, and
secondary with ˌ. US IPA uses /ɚ/ for a weak r-vowel (*water*, *doctor*),
/ɝː/ for a stressed one (*bird*, *work*), /r/ after vowels (*car* /kɑːr/),
/oʊ/ (*go*), /ɑː/ in *hot* and *stop*, /æ/ in *dance* and *ask*, and /t̬/ for
a t said like a quick d (*water*, *better*, *party*). Use `/ə/` for weak
vowels and long-vowel marks (ː), no syllable dots. For a phrase, write it as
it is said in normal speech, words separated by spaces: `/ˈnekst ˈstɑːp/`.
Say it shows the IPA under every word and sentence of up to four words, so
give those IPA too (`/ˈhaʊ ˈmʌtʃ ɪz ˈðɪs/`); leave it off longer sentences.
The recorded speech reads a single word from its `en` IPA, so a word whose
sound depends on its meaning (*live*, *close*, *read*) must have the IPA of
the meaning the item teaches. If you are not sure of a pronunciation, leave
`ipa` out: wrong IPA is worse than none.

### `say` (word items)

`{ "fi": "waader" }`: how the US English is read by a Finn who reads it as
Finnish, the paid hint in Say it (and shown after answering). Write it from
the US IPA the way Sounds of English writes `say.fi`: long sounds doubled
(`ii`, `uu`, `aa`, `oo`), `ä` for /æ/, `a` for /ʌ/, `e` for /ə/, `ör` for
/ɝː/ and `er` for /ɚ/, `ei ai oi ou au` for the diphthongs, `š tš ž dž` for
/ʃ tʃ ʒ dʒ/, `th` for /θ/ and `dh` for /ð/, `ng` for /ŋ/, and `d` for the
quick t (/t̬/). US English says the r after a vowel, so keep it ("kaar" for
*car*, "böörd" for *bird*, "waader" for *water*). Words separated by spaces.
Give every item that has `ipa` and four words or fewer a `say`.

### `pos` (word items)

One of: `noun`, `verb`, `adjective`, `adverb`, `pronoun`, `preposition`,
`conjunction`, `determiner`, `number`, `interjection`, `phrase` (a chunk that
isn't a whole sentence: "next stop", "in the morning"), `sentence` (a whole
sentence or question, with or without the full stop).

### `wordTypes` (sentences and rule examples)

For whole sentences and phrases (`pos` `sentence` or `phrase`, and `examples`
of `rule` items): what each word is, so games can label the places in the
sentence, show word types and colour words. One entry per word of the English
text, in order. Words are counted as the games split them: at spaces, with
punctuation at the ends dropped (`I'm`, `wake-up` and `o'clock` are one word,
a lone `&` is none). The check reports a count that doesn't match.

Each entry is a word class: `noun`, `pronoun` (also my, this, some, every),
`verb`, `auxiliary` (do/does/did, will, would, can, could, should, must, and
be or have when they help another verb: *is* running, *have* lost), `adjective`,
`adverb`, `article` (a, an, the), `preposition`, `conjunction`, `number`,
`question` (what, where, how…), `particle` (to before a verb, and the off/up/out
of a phrasal verb) or `interjection` (please, thanks, sorry). Contractions take
the class of their first part (`I'm` pronoun, `don't` auxiliary, `let's` verb).

Add `:subject` or `:object` to the main word of the subject and of the
object: `["article", "noun:subject", "verb", "article", "noun:object"]` for
"The man opens the door". "There" in there is / there are is the subject.
Leave roles out of fragments that have no subject ("in the morning").

```json
"wordTypes": ["auxiliary", "pronoun:subject", "verb", "pronoun:object"]
```

### `features`

What this entry shows about English, so games can pick items that practise
one thing and statistics can show which things are hard. List only features
that are clearly present and worth practising in this item; two or three is
typical, none is fine.

Pronunciation

- `th-sound`: th (/θ/ /ð/), which Finnish lacks: think, mother.
- `w-v-sound`: w and v, often mixed by Finns: wine, very.
- `z-sound`: a z sound (/z/ /ʒ/), including s said as z: zoo, is, music.
- `sh-ch-j-sound`: /ʃ/ /tʃ/ /dʒ/: shop, cheese, job.
- `voiced-consonant`: b, d, g against p, t, k matter: bag / back.
- `silent-letter`: a written letter that isn't said: knife, island.
- `schwa`: an unstressed vowel said /ə/ that Finns tend to say in full: banana, doctor.
- `vowel-length`: a long and a short vowel change the meaning: ship / sheep.
- `diphthong`: a gliding vowel spelled as one letter: name, home.
- `word-stress`: stress not on the first syllable, unlike Finnish: hotel, banana.
- `linking`: words run together in normal speech: an apple, turn it off.
- `weak-form`: a small word with a weak spoken form: can, to, of, and.

Spelling

- `irregular-spelling`: spelling far from the sound: enough, Wednesday.
- `double-letter`: double consonants that don't make a long sound: coffee, letter.
- `uk-us-spelling`: US and British spelling differ: color / colour.
- `homophone`: sounds like another word: right / write.

Grammar

- `article`: a, an or the is the point (Finnish has no articles).
- `uncountable`: an uncountable noun: information, advice, furniture.
- `irregular-plural`: child / children, person / people.
- `pronoun-gender`: he or she (Finnish hän for both).
- `possessive`: 's or my/your/their.
- `preposition-time`: in/on/at with times and days.
- `preposition-place`: in/on/at/to with places.
- `phrasal-verb`: verb + particle with its own meaning: look after, give up.
- `irregular-verb`: an irregular past form: went, bought.
- `third-person-s`: he/she/it takes -s: she works.
- `present-simple`, `present-continuous`, `past-simple`, `present-perfect`,
  `future` (will, going to, present for plans): the tense is the point.
- `modal-verb`: can, could, must, should, would, may, might.
- `question-form`: question word order or a question tag.
- `negative`: a negative form: don't, isn't, never.
- `do-support`: do/does/did in questions or negatives.
- `contraction`: I'm, don't, it's.
- `there-is`: there is / there are (Finnish uses a different structure).
- `imperative`: an instruction: Turn left. Don't touch.
- `comparative`, `superlative`: bigger / the biggest, more / most.
- `conditional`: if-sentences, would.
- `passive`: is made, was built.
- `gerund-infinitive`: -ing or to after a verb: enjoy cooking, want to go.
- `word-order`: English word order differs from Finnish here.
- `quantity`: much / many, some / any, a lot of, a few.

Vocabulary and use

- `false-friend`: looks like a Finnish word but means something else.
- `confusable`: easily mixed with another English word (borrow / lend).
- `collocation`: a fixed word partner: make a mistake, take a photo.
- `idiom`: the meaning isn't the sum of the words: It's a piece of cake.
- `compound`: two words making one: bus stop, toothbrush.
- `polite-form`: a polite request or softener: Could I…, I'd like…
- `uk-us-word`: a different word in US and British English: apartment / flat.
- `cognate`: almost the same in Finnish, an easy win: taxi, hotel, banana.

### `contexts`

Where the learner would meet or use this: `home`, `work`, `study`,
`shopping`, `eating-out`, `travel`, `transport`, `hotel`, `health`,
`emergency`, `social`, `small-talk`, `family`, `phone`, `online`, `money`,
`services` (post office, bank, hairdresser, repairs), `sport`, `nature`,
`weather`, `culture`. One to three per item.

`categories` are different: they are the topic chips learners pick as focus
areas (food, time, home…). Use existing categories where they fit and add a
new one only with its English and Finnish name in `index.json`, and put it in
one of the `categoryGroups` there (the headings of the focus area list).
Hobby and special-field topics (games, video editing…) go in a group marked
`"optIn": true`, so they only come up for learners who choose them.

### `otherOrders` (sentences)

`{ "en": ["Please take off your shoes."] }`: the same words as the text in
another order that is just as natural, so Build the phrase accepts it. Add it
when a learner could build a right sentence that the game would call wrong,
most often a phrasal verb with a noun object: *turn the lights off* and *turn
off the lights*. Not with a pronoun object: only *turn them off* is right.
"Please" at the start or end needs no entry; the game accepts it at either
end. The check reports an order with other words or the text's own order.

### `register`

`neutral` (default, leave out), `formal` (emails to strangers, official
notices) or `informal` (friends, chat messages).

### `note` (word and text items)

`{ "en": "...", "fi": "..." }`: one or two sentences on a trap for Finnish
speakers, shown by games after an answer. Only when there is a real trap.

### English examples in explanations and notes

In the Finnish text of an `explanation` or `note`, put every English example
between asterisks: letters, words, phrases and whole example sentences, and
the Finnish-style readings of sounds (*ii*, *ai*, *saiens*). The games show
them in colour so the example stands out from the description:

```
Kun sana alkaa kirjaimilla *kn*, *k* jätetään sanomatta. Sanotaan vain *n*, joten *know* kuulostaa samalta kuin *no*.
```

Mark whole phrases as one (*beat a level*, not *beat* *a* *level*), keep
Finnish words outside the marks (*Varattu* stays plain), and keep endings
and punctuation outside: *c*:n, *does*-sanaa, *Do you like tea*? The
English text needs no marks, as everything in it is English. Every `*` must
have a partner; the check reports one left on its own.

## Item types and the games that use them

- `word`: Word Hangman, Pick the meaning, Listen and choose, Say it, Say it right (words with `ipa`), Word
  Pairs (single words and short phrases), Build the phrase and Fill the Gap
  (whole sentences, 3-9 words work best). Add `examples` to useful words:
  whole sentences, each with Finnish.
- `gap` without `topic`: Fill the Gap. Distractors must be clearly wrong but
  believable: the same part of speech, a typical Finnish mistake.
- `decoyPool` (gap, rule, confusable): extra wrong options that fill a
  question's free slots after the item's own distractors (up to four options
  in all), picked at random each time. A pool on a `rule` or `confusable`
  covers every gap sentence of that topic, so **every word in it must be
  wrong in every one of those sentences**; check each sentence, not just the
  first. Watch for words that are also correct (*that* in a who/which
  sentence, *did* in a do/does question, *they* for a person): leave them out,
  or give that sentence its own `decoyPool` (`[]` for none). Pair topics
  (borrow/lend, a/an, for/since) have no pool: they stay two-way choices.
- `rule` + `gap` with `topic`: Grammar check. A new rule needs at least six
  gap sentences.
- `rule` with an id starting `sentence-`: how English sentences are built
  (subject, verb, object; questions; there is…), shown by Build the phrase and
  its sentence rules page. No gap sentences; give each two or three
  `examples` with `wordTypes`. The app picks which rule a sentence practises
  from its `wordTypes`, so a new one also needs an app change.
- `confusable` + `gap` with `topic`: Mixed-up words. At least six sentences,
  roughly balanced between the words.
- `group`: wrong options for grammar-word gaps in Fill the Gap.
- `alike` (pack `same-answers`): words the answer checks treat as the same.
  `kind: "sound"` for words Say it can't hear apart (to / too / two, their /
  there); `kind: "spelling"` for a word with two right spellings (color /
  colour), accepted by Say it and Type it. List the US spelling first. Never
  put two different words under `spelling`: Type it would accept the wrong
  one.
- `sound`: Sounds of English. At least six words per lesson; `say.fi` is a
  Finnish-style spelling of the sound (naif), `meaning.fi` the meaning. In
  `kind: "sound"` lessons a sound's `label` is only its name ("d", "Quiet
  th"); the game shows an example word from the lesson next to it, never the
  word being asked, so give every sound at least two words, better four.
  `kind: "spell"` lessons practise writing a sound (ck or k, ch or tch, sch,
  sk or sh): the bracketed letters are left out and the learner picks them
  from `spellings`. Give each spelling at least three words, and a `note` on
  a word that breaks the lesson's rule (*much*, *rich*).
- `phone` (pack `pronunciation`): a tip on saying one English sound, which
  Say it right shows when a learner's score shows they missed it. `symbols`
  are the IPA sounds it covers, written exactly as in the words' US `ipa`
  without slashes (`θ`, `tʃ`, `ɚ`, `oʊ`, `t̬`; length marks don't matter, so
  `iː` also covers `i`). One tip per sound: the game uses the first tip that
  lists it. `tip` says what to do with the tongue, lips or voice in one or two
  sentences, and names the Finnish sound it is mixed up with; `example` is a
  common word with the sound; `lesson` (optional) is the id of the Sounds of
  English lesson that practises it.
- `text`: several sentences that belong together, for Read and understand,
  Your turn (dialogues) and listening games. Kinds: `dialogue` (every line has a `speaker`), `story`,
  `message` (a chat message or text), `email`, `notice` (a sign or
  announcement), `instructions`, `description`. 3-10 sentences. Add two or
  three `questions` that can be answered from the text, each with a short
  `answers` list and two or three believable `distractors`. In a dialogue set
  `you` to the speaker a Finnish learner would be (the customer, the patient,
  the person asking the way), usually the one with a Finnish name; Your turn
  lets the learner play them and pick each of their lines.

## Packs

- `everyday-words`, `everyday-phrases`: the original basics.
- `more-words`: everyday vocabulary by theme (food, work, health, travel…).
- `everyday-sentences`: everyday gap sentences.
- `sentences`: whole everyday sentences and questions (`word` items).
- `grammar`, `mixed-up-words`, `word-groups`, `pronunciation`: as named.
- `texts`: `text` items.
- `games-and-pop-culture`: every item type on video games, tabletop
  role-playing, internet culture and pop culture (categories `video-games`,
  `tabletop-rpg`, `internet-culture`, `pop-culture`; an item can have several).
  Facts in it must be checkable: years, places and names.
- `video-and-media`: every item type on video editing, editing software
  (DaVinci Resolve and the words its interface uses) and media studies
  (categories `video-editing`, `editing-software`, `media-studies`). Name
  real interface parts exactly as the program shows them (Media Pool,
  Deliver page, Render All).

## After writing

1. Run `npm run content:check` in a lingo-hangman checkout pointed at your
   copy (`node scripts/check-content.mjs path/to/lingo-wordbase`).
2. Run `npm run content:format` (or format by hand: one item per line).
3. Read every Finnish sentence once more as a Finn would say it, and every
   English one as an American would. Fix anything stiff.
