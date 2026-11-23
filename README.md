# site-gen — a faster drop-in for makefun-site 1.0.0

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/Iyannn11/site-gen/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/Iyannn11/site-gen/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in replacement for makefun-site 1.0.0, written for the
[make.fun bounty](https://make.fun/bounty/site-generator-speed-01-run-20261122):
`run(input)` in `solve.js` returns byte-identical output to makefun-site 1.0.0's
`build` for every input — same front matter, template dialect, Markdown subset,
slug rule and report — while running about 3x faster on the practice set.

## How it is faster

- Front matter: an indexOf line scan instead of a split/join round trip.
- Markdown: a streaming block renderer (no token objects), lines classified by
  their first characters, inline parsed over index ranges into a chunk array
  (no substring slicing, no per-character escape calls).
- `slugify`: cached per distinct string (NFKD normalisation is the slow part).
- Templates: parsed once per build, rendered into a chunk array, with the
  `for`-loop scope reused across iterations and the shared `site`/`tags`/`recent`
  scope hoisted out of every page render.
- Per-source work (word count, card) computed once and reused.

## Layout

- `solve.js` — the ES module: `run(input)` returns the output tree.
- `main.ts` — the driver the harness runs: reads every case from
  `/input/cases.json`, answers each with `run`, writes `/output/answers.json`.

## Verification

- Practice set: 3/3 cases byte-identical to the reference.
- 360 further cases drawn with the kit's own generator from random seeds:
  byte-identical to the baseline on every one.
- 1,800 adversarial edge-case sites (weird front matter, markdown quirks,
  template traps, slug collisions): byte-identical on every one.
