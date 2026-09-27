# Korean A1-B1 vocab trainer

A free vocabulary trainer for Korean, A1 through B1: 2000 words with short
English glosses and example sentences, plus 60 short reading passages with
comprehension questions.

**Live:** https://bannerless-studio.github.io/korean/

**Scope note:** this app gives the vocabulary base for B1/TOPIK 3. An exam
also needs grammar, writing and listening practice, which this app does
not teach.

## Using the trainer

- **Script primer.** A "한글" stage runs before A1 and teaches Hangul in 47
  units, with symbol-to-sound, recognition and word-reading items. It's
  skippable with "I can read it" and reversible later from Progress.
- **Today** runs one daily session: review, learn new words, listen, recall,
  sentence practice, and a reading passage when one is due. Each stage skips
  itself when there is not enough material for it yet.
- **Words** lets you browse and search the word list, and drill any set on
  demand.
- **Test** has a placement test (to skip words you already know) plus free
  tests.
- **Progress** shows your stats and lets you export, import, or reset your
  progress.
- Question types: hearing a word and picking its meaning, reading a word and
  picking its meaning, seeing a meaning and picking the word, typing the word
  from its meaning, and filling a gap in a sentence. Typing drills the
  written Hangul form and is not case-sensitive (case doesn't apply to
  Hangul).
- **Reading passages (Read tab, inside Today):** 20 short texts each at A1,
  A2 and B1. A level's passages unlock once you've learned 70% of that
  level's words. Tap any word in a passage for its gloss, including
  inflected forms. Missed comprehension questions feed the words back into
  review. A passage's spaced re-read (after 7 days) becomes a listening
  pass when your device can play every sentence, with the text hidden and
  some questions audio-only.
- **Offline:** the app is a single page with a service worker, so once
  loaded it keeps working offline and loads instantly on repeat visits.
- **Speech:** there is no recorded audio for Korean. The trainer speaks
  every word and sentence with the browser's `ko-KR` voice.
- **Progress export/import:** the Progress tab can export your progress as
  text and import it back (for example, to move to a new device). Progress
  is otherwise kept only in this browser's local storage.

## Data

This is a static data pack for a language-agnostic vocab trainer (`key:
"ko"`). It's built from the shared
[`vocab-engine`](https://github.com/Bannerless-Studio/vocab-engine) (the UI
and drill logic, included here as a git submodule at `engine/`) plus this
repo's Korean data and Korean-specific pack-builder rules
(`engine/tools/packbuilder/langs/ko.py`; summarised in `tools/README.md`).

**Data quality.** Hand QA over three rounds used stratified samples of 90-180
words and sentences at a time (seeds 51/52, 71/72, 92) and found every
sampled word and sentence correct in the final round. A check of 20
sentences with conjugated verbs found all 35 verb forms linked to their
lemma, including irregular conjugations. The top 300 words have no wrong
part of speech. Every closed set (both number series, days, months, colours,
pronouns, particles, time words, greetings) is complete at A1, and every
word has at least two example sentences. Levels are frequency bands with
grade floors and ceilings from a published Korean-learner vocabulary list,
not CEFR. Full QA history and fix-round detail is in `tools/README.md` and
`TODO.md`.

Tatoeba had too few usable sentences for many words, so **1,692 of the
3,044 example sentences were written for this pack** (312 at A1, 543 at A2,
837 at B1), marked `"src": "gen"` in `pack/sentences.json`. Every word has
at least two sentences, and every word except three sensitive ones (죽다,
전쟁, 죽음) has one at its own level. Generated sentences are polite (해요체
or 합쇼체), have an English translation, and are machine-written and checked
with the builder's own analyser, but have not been reviewed by a native
Korean speaker. Sexual content and violence are kept out of A1/A2
sentences; rape, abuse, suicide and self-harm sentences are left out at
every level, as are Tatoeba sentences with non-standard spelling (너가,
왔어죠, 할께, 되요 for 돼요). Sentences are chosen polite first, then plain
written style, with 반말 last (0.9% of A1, 1.1% of A2, 12.9% of B1
sentences are 반말).

The 60 reading passages (`pack/passages.json`) were written for this pack
(`"src": "gen"`, source `tools/passages_src.json`), enforcing in-pack word
coverage of at least 95% at A1/A2 and 93% at B1, with a level budget on how
many higher-level words each passage may use. They are machine-written by
Claude, checked by an automated QA pass and re-checked by hand, but have not
had a native-speaker review. Per-passage numbers are in
`tools/REPORT_passages.md`.

Tatoeba has no permissively licensed Korean audio, so the pack relies on
TTS. Rules, counts and seeds are in `tools/REPORT.md`.

### Sources and licences

| Data | Source | Licence | Used for |
|---|---|---|---|
| Spoken/subtitle frequency | [hermitdave/FrequencyWords](https://github.com/hermitdave/FrequencyWords) (`ko_full.txt`, 2018 OpenSubtitles) | CC-BY-SA 4.0 | word ranking |
| Written/general frequency | [`wordfreq`](https://github.com/rspeer/wordfreq) Python package (Korean, mecab morphemes) | CC-BY-SA 4.0 | word ranking |
| Glosses, part of speech, conjugation tables | [kaikki.org](https://kaikki.org) Korean extract of English Wiktionary | CC-BY-SA 3.0 / GFDL (Wiktionary) | English glosses, POS, inflection map |
| POS tagging (build time only) | [spaCy](https://spacy.io) (MIT) with `ko_core_news_sm` 3.8.0, trained on UD Korean Kaist | CC BY-SA 4.0 (model) | tag class per eojeol, a hint to the analyser. The pack ships no model files. |
| Level floors (build time only) | NIKL 한국어 학습용 어휘 목록 (2003) grades, via [julienshim/combined_korean_vocabulary_list](https://github.com/julienshim/combined_korean_vocabulary_list) | KOGL Type 1 (list); MIT (mirror code) | a word graded 중급 is never A1, one graded 고급 is never below B1. Never shipped. |
| Example sentences | [Tatoeba](https://tatoeba.org) `kor_sentences_detailed.tsv` | CC-BY 2.0 FR | sentence text (contributor usernames in `pack/attribution.json`) |
| Sentence translations | Tatoeba `eng_sentences.tsv` + `kor-eng_links.tsv` | CC-BY 2.0 FR | English translations |
| Generated sentences | written for this pack, `tools/generated_sentences.tsv` | CC-BY-SA 4.0 | sentences for words Tatoeba covers with fewer than 2 usable sentences, marked `"src": "gen"` |

Licence: code MIT, pack data CC BY-SA 4.0, see LICENSE.

Tatoeba has no permissively licensed Korean audio, so the pack links none
and relies on TTS. No licence is non-commercial.

## Level bands

Words are ranked A1/A2/B1 by a blended frequency score across the subtitle
list, `wordfreq` and the tagged Tatoeba corpus, with a forced A1 core (both
number series, days, months, colours, pronouns, demonstratives, question
words, particles and the copula, time words, greetings and set phrases). A
published grade list can push a word up a band or cap it at A2. This is a
reproducible proxy for CEFR level, not an official CEFR or TOPIK
classification.

## Rebuild and publish

See `CLAUDE.md` for the pinned rebuild/check commands and `tools/README.md`
for what each file under `tools/` is, the Korean analyser summary, and the
full from-clean-checkout rebuild steps. In short: `python3
tools/build_pack.py` rebuilds the pack, `./build.sh` builds `index.html`,
and `./check.sh` must pass before every commit that touches `index.html`.
