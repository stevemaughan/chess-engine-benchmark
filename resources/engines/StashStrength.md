# Stash — Version Strength Table

Stash (by Morgan Houppin) is widely used as a calibration ladder for chess
engine testing because its releases step up in fairly even Elo increments
across a huge range — roughly 1100 to 3400.

CCRL only ever rated a subset of Stash versions. The column that spans the
whole ladder is the community-compiled CCRL Blitz table, with estimates
filled in for the gaps; the directly-measured CCRL 40/15 figures are listed
alongside.

| Ver | Released | CCRL Blitz 2'+1" | CCRL 40/15 | Notes |
|---|---|---|---|---|
| 1–7 | 13 Feb 2020 | — | — | Initial repo import, never rated |
| 8 | 13 Feb 2020 | ~1090 * | — | |
| 9 | 27 Feb 2020 | 1275 | — | |
| 10 | 3 Mar 2020 | ~1620 * | — | Quiescence search |
| 11 | 11 Mar 2020 | 1690 | — | Transposition table |
| 12 | 19 Mar 2020 | 1886 | — | Null-move pruning + LMR |
| 13 | 1 Apr 2020 | 1972 | — | |
| 14 | 9 Apr 2020 | 2060 | 2084 ±30 | |
| 15 | 26 Apr 2020 | ~2140 * | — | Pawn structure + mobility in eval |
| 16 | 15 May 2020 | ~2220 * | — | |
| 17 | 9 Jun 2020 | 2298 | — | SEE corrections |
| 18 | 13 Jun 2020 | ~2390 * | 2421 ±24 | |
| 19 | 14 Jul 2020 | 2473 | — | |
| 20 | 27 Aug 2020 | 2509 | — | |
| 21 | 28 Sep 2020 | 2714 | 2786 ±21 (v21.2) | |
| 22 | 27 Oct 2020 | ~2770 * | — | |
| 23 | 8 Nov 2020 | ~2830 * | 2903 ±21 | Late move pruning |
| 24 | 24 Nov 2020 | ~2880 * | — | |
| 25 | 6 Dec 2020 | 2937 | — | Tuned futility pruning |
| 26 | 29 Dec 2020 | ~3000 * | — | +68 Elo — time management, endgame scaling |
| 27 | 14 Jan 2021 | 3057 | 3022 ±21 | +77 Elo — continuation history |
| 28 | 23 Feb 2021 | 3092 | — | +56 Elo — singular extensions |
| 29 | 5 Mar 2021 | 3137 | 3103 ±18 | +54 Elo — staged movegen, SEE pruning |
| 30 | 30 Apr 2021 | 3166 | 3130 ±20 | +51 Elo — parent-node futility pruning |
| 31 | 25 Jun 2021 | 3220 | 3180 ±18 | +62 Elo — AdaGrad tuner, king safety rewrite |
| 32 | 2 Dec 2021 | 3252 | 3207 ±14 | +65 Elo — Adam tuner, capture history |
| 33 | 12 May 2022 | 3286 | 3246 ±14 | +52 Elo |
| 34 | 3 Nov 2022 | 3328 | 3273 ±12 | +53 Elo |
| 35 | 16 Oct 2023 | 3358 | 3306 ±14 | +54 Elo |
| 36 | 28 Jun 2024 | 3376 ±14 † | 3335 ±17 | |
| 37 | 24 Apr 2025 | 3424 ±12 | (4CPU: 3437) | Latest release |

`*` = not rated by CCRL, community estimate.

`†` The testing-guide snapshot lists v36 at 3399; the live CCRL Blitz list now
has 3376 — lists drift as more games accumulate.

## Notes on using the ladder

- The average step from v9 to v37 is about **77 Elo per release**, so "roughly
  100 Elo per version" is the right mental model, but real spacing ranges from
  about 30 to 200 Elo.
- The 40/15 ratings run 20–50 Elo *below* Blitz at the top of the range and
  slightly *above* it at the bottom, so don't mix the two columns when
  anchoring a test.
- Stash is still a hand-crafted-evaluation engine — no NNUE — which is part of
  why its ladder is so stable and reproducible across hardware.

## Multi-core reference points (CCRL 40/15, 4CPU)

| Ver | Rating |
|---|---|
| 31 | 3268 ±19 |
| 32 | 3304 ±18 |
| 33 | 3340 ±18 |
| 34 | 3364 ±18 |
| 36 | 3396 ±18 |
| 37 | 3437 ±18 |

## Sources

- [CCRL 40/15 — Stash family comparison](https://www.computerchess.org/cgi/compare_engines.cgi?family=Stash&print=Rating+list)
- [CCRL Blitz complete list](https://computerchess.org.uk/ccrl/404/rating_list_all.html)
- [Determining Engine Strength — Proper Chess Engine Testing](https://dannyhammer.github.io/engine-testing-guide/determining-strength.html)
- [stash-bot CHANGELOG](https://raw.githubusercontent.com/mhouppin/stash-bot/master/CHANGELOG.md)
- [stash-bot GitLab tags](https://gitlab.com/mhouppin/stash-bot/-/tags)
- [Chessprogramming wiki — Stash](https://www.chessprogramming.org/Stash)

*Compiled 21 August 2026.*
