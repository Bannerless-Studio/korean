# Korean trainer — agent notes

```
kaikki (Wiktionary ko) ──┐
Tatoeba kor/eng ─────────┼─> tools/build_pack.py (packbuilder, langs/ko.py)
FrequencyWords + wordfreq┤        │
NIKL grade list (build-time)┘     │  gloss_overrides.json, gloss_display.json,
spaCy ko_core_news_sm (build-time)│  forced_a1.txt, generated_sentences.tsv,
                                   │  mispaired.tsv, passages_src.json, id_map_v1.json
                                   v
                          pack/*.json --jsonify_pack.py--> pack/*.js
                                   v
              build.sh (engine/build.sh: engine/app.html + engine/core.js + pack js)
                                   v
                    index.html + sw.js -> GitHub Pages
                    https://bannerless-studio.github.io/korean/
progress lives in localStorage key vocab_ko on the shared origin
```

Why it is built this way: single-file site + service worker for offline; engine as a
git submodule so every language ships the same drills; pack ids frozen
(`tools/id_map_v1.json`) so learner progress survives rebuilds. Korean text isn't
looked up morpheme by morpheme: the builder's analyser reads each spaced eojeol
(noun+particle chain, conjugated verb/adjective, predicative noun+하다/되다, noun+copula,
or closed-set word) because Korean grammar glues particles and endings onto words
rather than leaving them separable like English affixes (see README.md "Korean rules"
in tools/README.md for the full analyser description). Korean-language text renders
with `word-break: keep-all` (engine CSS for `lang="ko"`, not pack data) so it wraps at
eojeol boundaries instead of mid-word.

## Commands (pinned)

- Rebuild pack: `python3 tools/build_pack.py` (equivalent to
  `PYTHONPATH=engine/tools python3 -m packbuilder build --lang ko --repo .`), then
  `python3 engine/tools/jsonify_pack.py pack`
- Rebuild reading passages: `PYTHONPATH=engine/tools python3 -m packbuilder passages --lang ko .`
  then `python3 engine/tools/jsonify_pack.py pack`
- Build site: `./build.sh`
- Check (must pass before every commit of index.html): `./check.sh`
- The `engine` submodule here is pinned to a vocab-engine commit that already has
  `tools/packbuilder/langs/ko.py`, so no `PACKBUILDER_PATH` override is needed
- QA helpers: `PYTHONPATH=engine/tools python3 -m packbuilder {check,scan,sample} --lang ko --repo .`
- Engine tests live in vocab-engine (see its CLAUDE.md)

## Always

- Commit index.html and sw.js together; check.sh's stale-build guard runs post-commit.
- Bump the engine submodule only to a vocab-engine main sha; rebuild after every bump.
- Keep ids append-only; never renumber (`tools/id_map_v1.json`).
- Append new lines at the end of `tools/generated_sentences.tsv` (order sets ids).
- Path-limited commits: engine, index.html, sw.js, pack/, tools/, README.md, TODO.md;
  never .venv or .cache.

## Never

- Edit pack/*.json by hand; change tools/gloss_overrides.json, tools/gloss_display.json,
  tools/forced_a1.txt or tools/mispaired.tsv and rebuild instead.
- Edit pack/*.js, index.html or sw.js by hand (generated).
- Delete sw.js (use engine/engine/sw.disable.js).
- Add comments that say what the code does; only why, or an external reference.
- Push to main without `git merge-base --is-ancestor origin/main HEAD`.

## Generated files

pack/*.js, pack/*.json, index.html, sw.js, tools/REPORT.md, tools/REPORT_passages.md,
tools/id_map_v1.json (frozen, hand-edit never).

## Where things are

README.md (end users), tools/README.md (builder inputs, file by file, incl. the Korean
analyser summary), TODO.md (residuals + QA history), engine/ (submodule, read-only
here).
