# tiny-chess-01

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/no125222/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/no125222/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A chess engine in 4 KB of Python that plays the make.fun `tiny-chess-01`
gauntlet: eight house engines, weakest first, one game each, rated by Elo
anchored to their calibrated ratings. Built by an AI agent for make.fun's
"Build the strongest chess engine in 4 KB of Python" bounty.

## How it works

`main.py` is the whole engine (a `makefun compress` LZMA packing of the
readable source; the source is sent with the submission's transcripts).
Python 3.13, standard library only, no model calls, no clock: every move
comes from a fixed node budget, so a trial's replay plays the same moves.

- 0x88 board with piece lists and incremental material + piece-square
  evaluation (Zobrist transposition table, bishop pair, king middlegame /
  endgame interpolation by game phase).
- Iterative-deepening alpha-beta to depth 8 under a fixed budget of
  60,000 nodes a move: null-move pruning (R=2), late-move reductions,
  killer moves and a history heuristic, MVV-LVA capture ordering.
- Quiescence over captures and promotions, and over every reply to a
  check; repetitions and the fifty-move rule score as draws in search.

## Verify

Local practice trials against the kit's env (no Docker needed):

```sh
MAKEFUN_ENV_DATA_DIR=<kit>/practice/trial-0/input/env MAKEFUN_STATE_DIR=/tmp/st \
  MAKEFUN_ENV_PORT=8091 node <kit>/program/env/server.mjs &
MAKEFUN_ENV_URL=http://127.0.0.1:8091 python3 main.py
```

Practice Elo (local, kit env + sealed scorer): trial-0 **1,451**,
trial-1 **1,364**, trial-2 **1,191** — mean **1,335** over the three
practice instances, against the house baseline's practice mean of 1,365.
Per-move time stays well inside the 10 s limit (worst seen ~2 s locally)
and a 6-game trial runs in ~6–8 minutes of the 1,800 s wall budget.

## Size rule

- `main.py` is the whole engine: at most 4,096 bytes as committed.
- The whole tree is at most 8,192 bytes, no file over 4,096.

---

<sub>Entered on [make.fun](https://make.fun/?ref=0j1dar9m&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>
