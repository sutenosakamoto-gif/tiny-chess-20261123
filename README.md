# Tiny chess engine in 4 KB of Python

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/sutenosakamoto-gif/tiny-chess-20261123/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/sutenosakamoto-gif/tiny-chess-20261123/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

An original chess engine for make.fun's [tiny-chess-01 bounty](https://make.fun/bounty/tiny-chess-01): the strongest engine I could fit in 4,096 bytes of Python (standard library only).

`main.py` (3,459 bytes) is the whole engine: the LZMA-packed form of the readable source `engine_src.py`, produced with the kit's own `makefun compress` (the rules allow a compressed payload; payload and unpacker count toward the 4,096 bytes). The bundle sends the readable source beside it under `packs/`. `engine_src.py` stays uncommitted: at 8,648 bytes it would break the size caps itself.

## How it plays

It talks to the env at `MAKEFUN_ENV_URL` (POST /game, POST /move), parses each served FEN, and answers with a UCI move. Search is deterministic: a fixed budget of 65,000 nodes per move, never a clock, so replays match move for move.

Design: mailbox board, full legal move generation (castling checks, en passant, promotions), make/unmake with incremental evaluation, original piece-square tables with an endgame king table, negamax alpha-beta with PVS, null-move pruning, LMR, killers, MVV-LVA ordering, quiescence search, and repetition awareness at the root.

## Tests

- Perft verified against known values (startpos, kiwipete, and three further standard positions); every move asserted legal by python-chess in sparring: 8/8 wins vs a random mover and 8/8 vs a one-ply greedy player.
- Practice trial 0 against the kit's own env and scorer: **Elo 1451** (3 wins, 2 draws, 1 loss), clearing the 1,154 practice floor and the house baseline's 1,365 practice mean. About 0.9 s per move on average, inside the 10 s limit.
