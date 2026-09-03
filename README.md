# Chess Engine Benchmark

A coding benchmark for large language models: write a UCI chess engine from
scratch in 24 hours, unaided, and be judged purely on how well it plays.

Most benchmarks for AI coding ability are either small, well-specified tasks
or subjective code review. A chess engine is neither. It is a real, open-ended
piece of software with a brutal, objective score at the end: put the engines on
a board and see who wins. Strength is measured in Elo, Elo can be compared
across models and across time, and there is no arguing with a match result.
This repository is the template for running that test on any model.

The idea owes something to the long-running community projects that have
produced strong engines over months of work. The question here is narrower:
what can a model do in a single day, with nobody to ask?

## The task

The model is pointed at this folder and told to follow `CLAUDE.md`. From that
moment it has **24 hours of wall-clock time** to design, build, test and
deliver a chess engine. The rules, in brief:

- **Fully autonomous.** Nobody watches, nobody answers questions, no hints
  are given. Ambiguities are the model's to resolve and record.
- **From scratch.** Public documentation is fair game: chessprogramming.org,
  papers, published tables such as PeSTO. Copying or adapting source code
  from an existing engine is not, and neither is using any pre-existing
  neural network, opening book or endgame file.
- **C or C++, standard library only**, compiled with GCC. No third-party
  libraries. Compiler intrinsics are allowed.
- **Single-threaded UCI engine** that handles the standard commands, prints
  `info` lines, exposes a `Hash` option, and never crashes, hangs or loses
  on time. Reliability counts fully: a crash is a loss.
- **The only thing scored is Elo** at 10 seconds plus 0.1 second increment
  on a modern Windows laptop. Code quality, documentation and feature count
  count for nothing.
- **Time management is part of the test.** The 24 hours covers thinking,
  reading, coding, compiling, testing and writing. The model decides what to
  build, what to measure and what to skip, and must keep an hourly log.

The full rules are in [`CLAUDE.md`](CLAUDE.md). It deliberately gives no
technical guidance on how to build a strong engine.

## What the model gets

Everything a human engine author would reasonably have on their machine,
and nothing that does the work for them.

| Resource | What it is |
|---|---|
| `resources/protocol/uci-protocol.md` | The UCI specification |
| `resources/fastchess/fastchess.exe` | Tournament manager for testing and compliance checks |
| `resources/fastchess/UHO.pgn` | Unbalanced opening book, 223,070 games, for low-draw testing |
| `resources/engines/stash-*.exe` | Stash versions 20, 21, 25, 30, 33 and 37, a ladder from about 2500 to 3400 CCRL Blitz |
| `resources/engines/StashStrength.md` | CCRL ratings of every Stash version |
| `resources/perft/perft.epd` | 126 perft positions with node counts to depth 6 |

The Stash ladder lets the model estimate its own strength during the run:
a 50% score against a Stash version at 10+0.1 means roughly that version's
rating.

## What the model must deliver

- **`final/<ModelName>chess24hrs.exe`**: a standalone, optimised Windows
  executable. Whatever is in `final/` when the clock runs out is the entry.
- **`docs/start_time.txt`**: written the moment the run starts; it defines the
  deadline and lets the model resume if its context is reset.
- **`docs/progress.md`**: an hourly log of what was done, what works, the
  current Elo estimate and the plan for the next hour, plus the time the
  engine first passed the perft suite and first played a full game.
- **`README.md`**: written only after the deadline, covering the engine's
  architecture, how the time was spent, an Elo-by-hour table, every
  assumption made and the model's own estimate of its strength.
- **`source/`**: all source, build scripts, test scripts and tuning data.

## How the engines are rated

After the run, the executable is rated by the benchmark operator in a
separate rating project, never by the model itself.

- fastchess, **10 s + 0.1 s**, one thread, 64 MB hash, `timemargin 200`.
- UHO 8-ply openings in random order, each played with both colours.
- A gauntlet against anchor engines whose ratings are fixed at their CCRL
  Blitz values, chosen to sit within about 250 Elo of the engine under test,
  plus a round robin among all the AI engines in the series.
- An anchored maximum-likelihood Elo fit over every game, reported with a
  95% confidence interval.

Ratings are quoted on the **CCRL Blitz scale under 10+0.1 conditions**.
They are not CCRL ratings. Fitting the anchors freely shows the Stash ladder
stretching by about 10% at this time control and on this hardware, so each
engine is rated only against anchors near its own level to keep that effect
small.

## Results so far

Four Claude models have taken the test. All four engines were rated in a
single 3,800-game run on 2 September 2026.

| Engine | Model | Elo | 95% CI | Score | Repository |
|---|---|---:|:---:|---:|---|
| Fable 5.1 chess 24hrs | Claude Fable 5.1 | **3277** | ±23 | 56.2% | [fable51-chess-24hrs](https://github.com/stevemaughan/fable51-chess-24hrs) |
| Opus 5 chess 24hrs | Claude Opus 5 | **3242** | ±23 | 51.5% | [opus5-chess-24hrs](https://github.com/stevemaughan/opus5-chess-24hrs) |
| Fable 5 chess 24hrs | Claude Fable 5 | **3049** | ±22 | 48.8% | [fable5-chess-24hrs](https://github.com/stevemaughan/fable5-chess-24hrs) |
| Sonnet 5 chess 24hrs | Claude Sonnet 5 | **2702** | ±26 | 31.4% | [sonnet5-chess-24hrs](https://github.com/stevemaughan/sonnet5-chess-24hrs) |

Each repository holds the full source, the hourly progress log, a README the
model wrote after the deadline, and a release with the executable.

### Head-to-head among the AI engines

Scores are for the row engine against the column engine, 100 games each.

| | Fable 5.1 | Opus 5 | Fable 5 | Sonnet 5 |
|---|:---:|:---:|:---:|:---:|
| **Fable 5.1** | | 63.5% | 80.0% | 98.5% |
| **Opus 5** | 36.5% | | 79.0% | 98.5% |
| **Fable 5** | 20.0% | 21.0% | | 87.5% |
| **Sonnet 5** | 1.5% | 1.5% | 12.5% | |

### How fast they got going

Each model logged when it first passed the full perft suite and when its
engine first played a complete game.

| Engine | Perft suite passed | First full game |
|---|---:|---:|
| Sonnet 5 | 6 min | 46 min |
| Fable 5.1 | 8 min | 15 min |
| Fable 5 | 9 min | 17 min |
| Opus 5 | ~40 min | ~45 min |

### Notes on the four runs

**All four chose C++** and bitboards with PEXT sliding-piece attacks, and all
four built the conventional modern search: iterative deepening, principal
variation search, transposition table, quiescence, null move, late move
reductions and a suite of pruning heuristics. The differences lay in
evaluation, tuning discipline and how the time was spent.

**Fable 5.1** is the outlier. About five and a half hours in it wrote its own
self-play data generator and an NNUE trainer, neither of which was provided
or suggested. Its first nets lost heavily to its hand-crafted evaluation.
By hour ten, after switching to score-only training targets, a net reached
parity, and it kept iterating: later nets were trained on data generated by
the previous NNUE engine. The shipped engine carries a 768→384×2 network
trained on roughly 17 million of its own self-play positions, with the
weights compiled into the executable and the hand-crafted evaluation kept as
a fallback option.

**Opus 5** decided at the outset that NNUE was not affordable in 24 hours and
put the time into a well-tuned hand-crafted evaluation and careful
measurement of its pruning. It finished 35 Elo behind Fable 5.1 and lost
their head-to-head 23–27–50.

**Fable 5** kept the whole engine in a single C++ file and chained a long
series of SPRT-verified improvements. It measured itself at 3030–3045 and was
rated 3049.

**Sonnet 5** had the fastest perft pass of the four but spent much of the
first half of its run on reliability, including a crash traced to running the
search on a spawned thread under MinGW. It finished at 2702, close to its own
estimate of 2720–2730.

Every model's self-estimate landed within its stated uncertainty of the
measured rating. None of the four engines lost a game on time, disconnected
or played an illegal move in the 3,800-game rating run.

## Running the benchmark on a new model

1. Copy this folder to a new location, one copy per run. Do not reuse a
   folder: the presence of `docs/start_time.txt` tells the model it is
   resuming, not starting.
2. Make sure GCC (MinGW-w64) is on the PATH and the machine has the CPU
   features listed in `CLAUDE.md`. Note the physical core count.
3. Open the folder with the model's agent tooling and tell it to read and
   follow `CLAUDE.md`. An `AGENTS.md` is included for tools that look for
   that name instead.
4. Walk away. Do not answer questions. If the tool's context resets, restart
   it in the same folder; the instructions cover resuming.
5. After 24 hours, take the executable from `final/` and rate it against
   anchor engines as described above. Fill in the "Official results" section
   of the model's README.

## Repository layout

```
CLAUDE.md          the benchmark instructions the model follows
AGENTS.md          one-line pointer to CLAUDE.md for other agent tools
README.md          this file (for humans; the model writes its own README in its run folder)
resources/         read-only inputs, described above
```

## Acknowledgements

- [Stash](https://gitlab.com/mhouppin/stash-bot) by Morgan Houppin, whose
  numbered releases make an ideal calibration ladder.
- [fastchess](https://github.com/Disservin/fastchess) for tournament
  management and the UCI compliance checker.
- The [UHO opening book](https://www.sp-cc.de/uho_2024.htm) by Stefan Pohl.
- [CCRL](https://www.computerchess.org.uk/ccrl/404/) for the anchor ratings.
- Crafty and Juggernaut served as additional anchors in the rating runs.
