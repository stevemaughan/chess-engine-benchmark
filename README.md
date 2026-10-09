# Chess Engine Benchmark

A coding benchmark for large language models: write a UCI chess engine from
scratch in 24 hours, unaided, and be judged purely on how well it plays.

Most benchmarks for AI coding ability are either small, well-specified tasks
or subjective code review. A chess engine is neither. It is a real, open-ended
piece of software with a brutal, objective score at the end: put the engines on
a board and see who wins. Strength is measured in Elo, Elo can be compared
across models and across time, and there is no arguing with a match result.
This repository is the template for running that test on any model.

> **Note:** this README describes the benchmark and lives in the template.
> When a copy of this folder is used for a run, the model overwrites this
> file at the end of its 24 hours with its own write-up of the engine it
> built. The version you are reading survives only here.

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
- **`docs/resources.md`**: a log of every internet resource the model
  consulted during the run, with the time, the URL and what it took from
  it. An empty file means it used none.
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
  plus head-to-head matches against the other AI engines in the series near
  its level.
- The anchors are Stash versions 20 to 37, Juggernaut and Crafty, plus eight
  stronger engines added for Opus 5.5 once it outgrew the Stash ladder
  (see below).
- An anchored maximum-likelihood Elo fit over every game in the series,
  reported with a 95% confidence interval. Ratings are refitted each time a
  new engine is added.
- Adjudication: resign after 3 moves at ±600 cp (both engines agreeing);
  draw after move 40 when 8 consecutive moves stay within ±20 cp; 250-move
  cap.

Ratings are quoted on the **CCRL Blitz scale under 10+0.1 conditions**.
They are not CCRL ratings. Fitting the anchors freely shows the Stash ladder
stretching by about 10% at this time control and on this hardware, so each
engine is rated only against anchors near its own level to keep that effect
small.

## Results so far

Eight engines have taken the test: seven from Claude models and one from
Astra 6. They were rated in five stages, on 2, 19 and 23 September and
5 and 8 October 2026, and all 10,380 games are fitted together. Each new engine's
games shift the fit a little, so earlier engines' ratings can move by a few
points between updates.

| Engine | Model | Elo | 95% CI | Games | Score | Repository |
|---|---|---:|:---:|---:|---:|---|
| Opus 5.5 chess 24hrs | Claude Opus 5.5 | **3463** | ±16 | 2180 | 50.4% | [opus-5.5-chess-24hrs](https://github.com/stevemaughan/opus-5.5-chess-24hrs) |
| Sonnet 5.5 chess 24hrs | Claude Sonnet 5.5 | **3420** | ±16 | 2180 | 50.9% | [sonnet-5.5-chess-24hrs](https://github.com/stevemaughan/sonnet-5.5-chess-24hrs) |
| Fable 5.1 chess 24hrs | Claude Fable 5.1 | **3260** | ±19 | 1580 | 49.8% | [fable51-chess-24hrs](https://github.com/stevemaughan/fable51-chess-24hrs) |
| Opus 5 chess 24hrs | Claude Opus 5 | **3229** | ±19 | 1580 | 45.9% | [opus5-chess-24hrs](https://github.com/stevemaughan/opus5-chess-24hrs) |
| Astra 6 chess 24hrs | Astra 6 | **3144** | ±20 | 1520 | 44.4% | [astra6-chess-24hrs](https://github.com/stevemaughan/astra6-chess-24hrs) |
| Fable 5 chess 24hrs | Claude Fable 5 | **3045** | ±21 | 1300 | 50.3% | [fable5-chess-24hrs](https://github.com/stevemaughan/fable5-chess-24hrs) |
| Haiku 5.5 chess 24hrs | Claude Haiku 5.5 | **2765** | ±24 | 1060 | 46.7% | [haiku-5.5-chess-24hrs](https://github.com/stevemaughan/haiku-5.5-chess-24hrs) |
| Sonnet 5 chess 24hrs | Claude Sonnet 5 | **2703** | ±23 | 1360 | 30.9% | [sonnet5-chess-24hrs](https://github.com/stevemaughan/sonnet5-chess-24hrs) |

Each repository holds the full source, the hourly progress log, a README the
model wrote after the deadline, and a release with the executable.

Opus 5.5 was stronger than Stash 37, the top of the Stash ladder, so eight
stronger anchors were added to rate it: Marvin 6.3.0 (3458), Leorik 3.2
(3490), Tucano 12.00 (3491), Carp 3.0.1 (3525), Sirius 9.0 (3528), Patricia
5.0 (3539), Elixir 3.0 (3567) and Motor 0.9.0 (3695). Against the anchors
alone it rates 3448 ±18. Its lopsided wins over the other AI engines pull the
combined fit up to 3463. Together with Sonnet 5.5's wins they lowered the
older engines by about 15 Elo compared with their first published ratings
(Fable 5.1 was 3277).

Sonnet 5.5 was rated against nine anchors from Stash 33 (3274) to Patricia
(3539), in two gauntlets that agree (3410 ±24 and 3423 ±25; 3416 ±18 against
the anchors alone). Its games against the other AI engines tell the same
story, so the combined fit barely moves it.

Haiku 5.5 was rated against the same five anchors as Sonnet 5, from Stash 20
(2512) to Crafty (2970), plus games against Sonnet 5 and Fable 5. Against the
anchors alone it rates 2768 ±27, and its AI head-to-heads agree.

### Head-to-head among the AI engines

Scores are for the row engine against the column engine, 100 or 160 games per
pairing. A dash means the pair did not play: Opus 5.5 and Sonnet 5.5 were
not matched against Fable 5, Haiku 5.5 or Sonnet 5, which are 350 or more Elo
weaker, and Haiku 5.5 played only its two nearest AI neighbours.

| | Opus 5.5 | Sonnet 5.5 | Fable 5.1 | Opus 5 | Astra 6 | Fable 5 | Haiku 5.5 | Sonnet 5 |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Opus 5.5** | | 56.9% | 86.6% | 85.6% | 92.5% | – | – | – |
| **Sonnet 5.5** | 43.1% | | 76.6% | 77.8% | 80.0% | – | – | – |
| **Fable 5.1** | 13.4% | 23.4% | | 63.5% | 68.1% | 80.0% | – | 98.5% |
| **Opus 5** | 14.4% | 22.2% | 36.5% | | 62.2% | 79.0% | – | 98.5% |
| **Astra 6** | 7.5% | 20.0% | 31.9% | 37.8% | | 67.5% | – | 92.0% |
| **Fable 5** | – | – | 20.0% | 21.0% | 32.5% | | 85.0% | 87.5% |
| **Haiku 5.5** | – | – | – | – | – | 15.0% | | 58.1% |
| **Sonnet 5** | – | – | 1.5% | 1.5% | 8.0% | 12.5% | 41.9% | |

### How fast they got going

Each model logged when it first passed the full perft suite and when its
engine first played a complete game.

| Engine | Perft suite passed | First full game |
|---|---:|---:|
| Opus 5.5 | 6 min | ~11 min |
| Sonnet 5 | 6 min | 46 min |
| Sonnet 5.5 | 7 min | ~13 min |
| Fable 5.1 | 8 min | 15 min |
| Fable 5 | 9 min | 17 min |
| Haiku 5.5 | 8 min (depth 5), 15 h (depth 6) | 9 min |
| Astra 6 | 12 min (depth 5), 22 min (depth 6) | 12 min |
| Opus 5 | ~40 min | ~45 min |

### Notes on the eight runs

**All eight chose C++** and bitboards with PEXT sliding-piece attacks, and all
eight built the conventional modern search: iterative deepening, principal
variation search, transposition table, quiescence, null move, late move
reductions and a suite of pruning heuristics. The differences lay in
evaluation, tuning discipline and how the time was spent.

**Opus 5.5** committed to a self-trained network from the start. It passed
perft six minutes in and had a PeSTO-evaluated engine in `final/` at 16
minutes. Within the first hour it had also written a self-play data generator
and an NNUE trainer and started generating data. Its first net, trained on
5.3 million positions, beat the hand-crafted evaluation by about 150 Elo in
hour two. From then on each network generated the training data for the
next. The shipped engine carries a (768→256)×2 network with eight output
buckets, trained on 86.7 million self-play positions. The rest of the run
went on measured search and time-management tuning, typically 300–1,200
games per decision. Low-clock stress tests at hour nine exposed a
time-forfeit weakness, which it fixed. It estimated its own strength at
3500 ±40 and was rated 3463, about 200 Elo clear of Fable 5.1. It scored 86%
against both Fable 5.1 and Opus 5.

**Sonnet 5.5** took a similar route and finished second, 43 Elo behind Opus
5.5. It passed perft seven minutes in, and its first engine used a
hand-crafted evaluation. About two hours in, it replaced that with an NNUE
trained only on its own self-play: a (768→256)×2 SCReLU network with eight
output buckets. Over the day it trained eight generations of nets on about 62
million positions, each generation's data labelled by the previous net. It
leaned heavily on fast fixed-node matches for ablations and SPSA tuning, and
an ablation at hour six showed that razoring was costing about 110 Elo with
the NNUE. Removing it was one of the largest single gains of the run. The net gains
were flattening out by the last generations, and the final hours went on
time-management tests and verification. It estimated 3425 ±40 and was rated
3420. It scored 43% against Opus 5.5 and 77–78% against Fable 5.1 and Opus 5.

**Fable 5.1** was the first to train its own network. About five and a half
hours in it wrote its own self-play data generator and an NNUE trainer,
neither of which was provided or suggested. Its first nets lost heavily to
its hand-crafted evaluation. By hour ten, after switching to score-only
training targets, a net reached parity, and it kept iterating: later nets
were trained on data generated by the previous NNUE engine. The shipped
engine carries a 768→384×2 network trained on roughly 17 million of its own
self-play positions, with the weights compiled into the executable and the
hand-crafted evaluation kept as a fallback option.

**Opus 5** decided at the outset that NNUE was not affordable in 24 hours and
put the time into a well-tuned hand-crafted evaluation and careful
measurement of its pruning. It finished 31 Elo behind Fable 5.1 and lost
their head-to-head 23–27–50.

**Astra 6** is the name the model was given after its run. A system reboot
interrupted the run, and the operator extended the deadline by 7 hours 41
minutes to make up for it, so it had 31 h 41 min of wall-clock time in total.
It built a fitted hand-crafted evaluation of about 480 parameters plus a
small 16-unit neural residual. Both were trained on about 435,000 positions
from its own games, labelled by fixed 100,000-node searches from Stash 37.
It then SPSA-tuned search and evaluation over more than 13,000 games. It
estimated about 3170 and was rated 3144.

**Fable 5** kept the whole engine in a single C++ file and chained a long
series of SPRT-verified improvements. It measured itself at 3030–3045 and was
rated 3045.

**Haiku 5.5** wrote its whole first engine, about 1,000 lines in one C++ file,
in eight minutes: perft passed to depth 5 at minute eight and it played its
first games against Stash 20 a minute later. It kept a hand-crafted
evaluation built on the Simplified Evaluation Function tables and added terms
one at a time, each accepted only after an SPRT or a fixed self-play match
against the previous release: pawn structure (+106), mobility (+49), king
safety (+22), late-move pruning with an "improving" flag (+29),
history-adjusted reductions (+23) and internal iterative reduction (+22). Early
test runs exposed time forfeits, which it traced and fixed in the second hour,
and a robustness pass caught a crash on malformed FENs. Its last seven hours
of experiments were all flat or negative, so it stopped engine work with
about three hours to spare. It estimated 2755 ±50 and was rated 2765, about 60
Elo above Sonnet 5, which it beat 58–42 in their head-to-head.

**Sonnet 5** had one of the fastest perft passes but spent much of the first
half of its run on reliability, including a crash traced to running the
search on a spawned thread under MinGW. It finished at 2703, close to its own
estimate of 2720–2730.

Every model's self-estimate landed within its stated uncertainty of the
measured rating. None of the eight engines lost a game on time, disconnected
or played an illegal move in any of the 10,380 rating games.

## Running the benchmark on a new model

1. Copy this folder to a new location, one copy per run. Do not reuse a
   folder: the presence of `docs/start_time.txt` tells the model it is
   resuming, not starting.
2. Make sure GCC (MinGW-w64) is on the PATH and the machine has the CPU
   features listed in `CLAUDE.md`. Note the physical core count.
3. Open the folder with the model's agent tooling and tell it to read and
   follow `CLAUDE.md`. An `AGENTS.md` is included for tools that look for
   that name instead. The model is told to ignore this README during the
   run and replace it with its own after the deadline.
4. Walk away. Do not answer questions. If the tool's context resets, restart
   it in the same folder; the instructions cover resuming.
5. After 24 hours, take the executable from `final/` and rate it against
   anchor engines as described above. Fill in the "Official results" section
   of the model's README.

## Repository layout

```
CLAUDE.md          the benchmark instructions the model follows
AGENTS.md          one-line pointer to CLAUDE.md for other agent tools
README.md          this file (for humans); replaced by the model's own README at the end of a run
resources/         read-only inputs, described above
```

## Acknowledgements

- [Stash](https://gitlab.com/mhouppin/stash-bot) by Morgan Houppin, whose
  numbered releases make an ideal calibration ladder.
- [fastchess](https://github.com/Disservin/fastchess) for tournament
  management and the UCI compliance checker.
- The [UHO opening book](https://www.sp-cc.de/uho_2024.htm) by Stefan Pohl.
- [CCRL](https://www.computerchess.org.uk/ccrl/404/) for the anchor ratings.
- Crafty, Juggernaut, Marvin, Leorik, Tucano, Carp, Sirius, Patricia,
  Elixir and Motor served as additional anchors in the rating runs.
