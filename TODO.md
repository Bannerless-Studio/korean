# TODO (v2 candidates)

Residuals from the v1 QA rounds. The rules already in place are in
`engine/tools/packbuilder/langs/ko.py` and summarised in the README.

## Content
- **Reading passages: shipped (2026-09-25).** 60 hand-written passages
  (`tools/passages_src.json`, report in `tools/REPORT_passages.md`), rebuilt
  and jsonified with engine `ff88f44`. Re-QA found one bad true/false item
  (p0043 q3, sourced from world knowledge instead of the passage text); fixed
  in `.cache/src/b1.py` and regenerated via `.cache/src/assemble.py`. The 15
  display glosses (`tools/gloss_display.json`, incl. 안 "inside") are in
  `pack/words.json`; `pack/sentences.js` was regenerated so it is no longer
  stale. Open: the passages were written by a model and want a native
  speaker's read, B1 first. Authoring drafts live in the gitignored
  `.cache/src/` (a1/a2/b1.py, assemble.py, qa.py, spans.py).
- **Passage residuals (re-QA 2026-09-25, accepted as-is).** 부탁드립니다 links
  as one whole eojeol rather than 부탁 + 드리다 + -ㅂ니다; p0030 q2's wrong
  options include one at a level above the passage's own; p0035's sixth
  sentence has an awkward English phrasing. None block coverage or linking
  and none were fixed this round.
- 1,692 of 3,044 sentences are machine-written (`"src": "gen"`): 312 at A1,
  543 at A2 and 837 at B1. A native speaker should review them, B1 first.
- **Suicide and self-harm filter is Korean-only.** `langs/ko.py` drops these
  sentences at every level (`DROP_ALL_KO`, `DROP_ALL_KO_EN`). The shared
  drop-all tier in the packbuilder core should carry suicide/self-harm for
  every language, and the other packs should then be rebuilt.
- **Sensitive words without an own-level sentence.** 죽다 (A1), 전쟁 and 죽음
  (A2) have sentences only at higher levels, because the A1/A2 violence
  filter removes their own-level ones. Write mild own-level sentences for them
  or accept the gap.
- Tatoeba spells some sentences with spacing mistakes (갈거라고, 할꺼야,
  가야되요). Those eojeol link only the parts the analyser can read.

## Levels
- Levels are frequency bands with NIKL grade floors. A word graded 중급 is
  never A1, and one graded 고급 is never below B1. Ungraded words keep their
  band. This is not a TOPIK grading.
- **Ceilings.** A word NIKL grades only 초급 is capped at A2, and colour
  adjectives, 남동생 and 여동생 are capped at A2 by hand
  (`HAND_CEILING_A2`). Capped words displace the lowest-ranked A2 words to
  B1.
- Wiktionary lists 이제, 진짜 and 혼자 as nouns, so they are nouns here, though
  learners meet them mostly as adverbs. The gloss covers both uses.

## Lemmas and links
- **Homograph shares.** A meaning under about 10% of a spelling's uses is
  not a separate word, and its spelling links nothing:

  | spelling | dropped meaning | share |
  |---|---|---|
  | 말 | horse | 10% |
  | 차 | tea | 8% |
  | 사과 | apology | 4% |
  | 밤 | chestnut | rare |
  | 배 | pear | rare |

  These second meanings are kept as words of their own: 눈 "snow" (29%),
  배 "ship" (23%) and 다리 "bridge" (40%). They are routed by English cues in
  the translation. Nouns whose translation names neither meaning keep the
  commoner one.
- **Verb homographs** (살 = 사다/살다, 들어 = 듣다/들다, 걸어 = 걷다/걸다, 팔 =
  팔다/파다, 날 = 나다/날다) follow the translation's English through the
  verb cue table. With no cue, the analyser's frequency prior decides, and it
  can pick the wrong verb.
- **Same spelling, different part of speech.** 싸다 "cheap" (adj) and 싸다 "to
  wrap; to poop" (verb) are one entry, so the verb's uses link the adjective.
- **Tagger hints.** A one-syllable noun can lose to a verb form when spaCy's
  hint is wrong. 도 "degree" was dropped as a word because most of its uses
  were the particle -도.
- **Spaced grammar.** 만 하다 written apart ("worth doing") links 하다 and
  leaves 만 unlinked. The joined 만하다 is a word. -고 나서, -ㄹ지도 모르다 and
  -에도 불구하고 link no word for the grammar part. There is no grammar entry
  for them.
- **Numerals.** 이 before a Sino-Korean counter becomes "two" only when the
  translation says "two" followed by that counter's unit ("two years"). A
  translation that counts the same unit elsewhere in the sentence would still
  mislead it. 네 before a noun is "four" only when the translation says four.
- **Honorific and humble verbs.** 드리다 links 주다 and 드시다 links 먹다. The
  shared lexicon drops Wiktionary senses tagged form-of, and that removes
  드리다's own sense. The engine change "keep humble/honorific form-of senses"
  would let 드리다 be its own word, but that changes the frozen id set.
- **Frozen-id redirects.** 연구하다 links 연구, and 산책하다 links 산책. They were
  redirected to keep id map v1 stable. Revisit them when a v2 id map is cut.
- **Hand swaps at the cut (v1.2).** `keep_keys` in `langs/ko.py` keeps 졸리다,
  이빨, 베개, 습관 and 여우 in the 2000, and `drop_keys` removes 공공, 경향, 관점,
  이래 and 수면. All five kept words had ids in id map v1, so the map needed no
  new entries. The dropped keys keep their reserved ids. 여우 needed two
  generated sentences, because no Tatoeba sentence could illustrate it.
- **B1 반말.** 12.9% of B1 sentences are 반말. A1 and A2 are near 1%, because
  195 polite A2 sentences were written in v1.2. Write polite B1 sentences
  if B1 should follow.
- **Id map re-frozen (v1.1, before first ship).** The fix round added 24 keys:
  the native tens 서른 to 아흔, 잘 먹겠습니다, 잘 먹었습니다, 처음 뵙겠습니다,
  뵙다, 부모님, -께, the determiners 그런, 저런 and 아무런, 지치다 as a verb,
  기대, 정리, 진행 and 물다. 딱, 점점 and 깜짝 kept their ids as adverbs. The
  forced items pushed 20 lowest-ranked words out of the 2000: 결론, 고민하다,
  고프다, 금발, 나비, 먹이, 베개, 속하다, 습관, 시각, 아무렇다, 여우, 오랜,
  이르다, 이빨, 졸리다, 최신 and 팔리다, plus 자살 and 자살하다 by policy.
  Once the map ships, ids are append-only.
- **이 "teeth".** 이를 is read as 이 + 를 ("this" or "teeth"), never 이르다. The
  pack teaches "tooth" as 이빨, so 이를 닦다 links only the particle.
- **One-syllable hosts.** The frequent-noun fallback needs a host of two or
  more syllables, so a one-syllable noun spelled like a verb form (차는, 배를)
  follows the analyser's prior and can link the verb.
- **Names keep their particle.** A name the tagger marks with a particle
  attached (평양은, 태국이) can stay one unlinked token, so its particle links
  nothing.
- **세상에 "good heavens".** The interjection links the noun 세상 "world".
- **Missing words.** 안 "inside" is not in the pack, since the adverb 안 "not"
  takes the spelling. Sentences with 안에 ("inside") are skipped when their
  other words are not in the pack. 날다 "to fly" is not in the pack, so 날 in
  새는 날 수 있다 links nothing.
- **Non-pack X하다 verbs.** Many Tatoeba sentences use an X하다 verb outside
  the 2000, and the pack-or-top-3000 rule blocks them. This is the main
  reason B1 needed generated sentences.
- **Gap-drill residuals.** Nominal alts are now only the word plus particles
  or the copula, so a noun met only inside a light verb or compound
  (통제했어요, 운전연습을, 해고당한) cannot be blanked there. 57 of 3,029
  sentences have no blankable own-level word (29 in the QA snapshot), and 29
  words are never blankable, down from 52. Examples: 배 (both entries), 통제,
  해고, 대 (counter), 오래다. Linking those sentences to the X하다 verb would fix
  most of them. Particles are never blanked as whole words.
- **Exclusions.** -들, -대로, -만큼 and -뿐 have no usable Wiktionary particle
  entry and are not in the pack.

## Build
- `check.sh` and `tools/build_pack.py` need
  `PACKBUILDER_PATH=../vocab-engine/tools` until the `engine` submodule is
  bumped to a vocab-engine commit that contains `langs/ko.py`.
- `langs/ko.py` monkeypatches `wordfreq.zipf_frequency` for "ko". wordfreq
  counts Korean as mecab morphemes, and the patch maps each word to its
  morpheme spelling. A core hook for morpheme-counted frequency lists would
  replace the patch.
- REPORT.md's "Word coverage" counts the sentences chosen for each word.
  `check_pack` counts every sentence that links it. 31 words have one chosen
  sentence and appear in at least two, and `check_pack` reports every word
  with at least two.
- New generated sentences go at the end of `tools/generated_sentences.tsv`.

## Live check 2026-09-25 (14/14 PASS)
- ~~Minor mislink: 거는 in "…않다는 거는 저도…" links 걸다 "to hang"; should be 것 + 는.~~ Fixed in republish ef44c6e (거/꺼+particle → 것; s0677 now links 것 + 는).
- 39/2000 words (mostly A1 particles) have no example showing the headword or an alt; 38 more miss on one of two examples.

Republish 09e90bc: sentence spans (20190/20190 linked words placed, 0 unspanned WARN); inflected forms now cloze targets. words.json unchanged: no word level or gloss moved by stab narrowing.

Republish ef44c6e: no words moved (s0677 거는 relinked 걸다 → 것 + 는; small rank shifts from corpus counts); deleted override keys 공공|noun, 못|noun, 수면|noun, 아무렇다|adj, 안|noun, 이거|pron, 이래|noun; set-counter and no-voice planner fixes.

Republish on engine 7b8c173 (port wave 3): enrich adds `ft` to every word (A1 [ambient 94, core 446, peripheral 60], A2 [6, 519, 175], B1 [0, 420, 280]) and the generic pack flag block plus `eta` (tools/eta.json: goal 1 gain 0.005358, goals 2 and 3 null because they did not cross 0.9 within the 200-session horizon, gate known 8.366). Nothing else in the pack changed.
- Migration proof: rollback hash 4fe8f915a9279a13147744694445d7d424f5d5f3; previous live md5 index feba99eb22e715d1428b4b1a6455aee3, sw c43c2d94f711ac5fb823d906cf324217. Storage: new fields day/sn/t/u/f/p/pm/pv/pause/read.done s,ls/today.tw on first use; boot writes nothing; previous build ef44c6e/aa00571 carries them (migration [port] 9/9).
- ETA calibration note: the engine's pack calibration (600 sessions) is impractically slow on this pack because findSurface compiles a unicode regex per call; the numbers above come from 200 sessions per seed (seeds 5/6/7) with that function memoised in a scratch engine clone (results identical, speed only); out of sample seeds 8/9/10 PASS 3/3 for goal 1 and the gate.
- Live proof 7e0264e (2026-10-08) KEEP: 12-session seed (primer learned) from 4fe8f91 byte-equal after boot/reload/Progress open (leaving Progress adds only prog.pv); one Today session writes w/sets/sessions/script/sn/day/pm; that record boots on 4fe8f91 byte-equal (boot + reload), one key vocab_ko, a session runs there; back on live byte-equal; 0 console errors, 0 failed requests. Today shows "≈ 160 sessions".
