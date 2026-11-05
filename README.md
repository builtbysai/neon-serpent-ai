# Neon Serpent AI

An AI that plays [Neon Serpent](https://builtbysai.com/hyper-casual-games/10-neon-serpent/) (hyper-casual game №10) — with the same controls you use, making the best choices, chasing the highest possible score. Watch it play live.

**Live: https://builtbysai.com/neon-serpent-ai/**

## How it plays

The AI never touches the game's internals. Every decision goes through `window.__ns.setDir()` — the exact function a swipe calls. It only issues swipe directions, one per tick, like a player.

Its brain, per tick:

1. **Patrol (the backbone).** After the split, cyan patrols a Hamiltonian cycle over cols 0–5, rows 0–12 (78 cells). Magenta's path is the offset mirror `(12-x, y+6)`, so one command advances both snakes along their paths. The cycle is verified: 78 unique cells, closed, every food row reachable, both split start cells on the paths at the same phase. Following it can never trap either snake — the body always trails behind on the cycle and the next cell is always free.
2. **Greedy fallback.** When the cycle move is fatal (a hazard sitting on the cycle) or the head is off the cycle, it falls back to a greedy planner: simulate each legal direction for both snakes, reject any move that kills either one, then pick the move with the shortest path to food that keeps both snakes able to reach their own tails (tail-chase safety) with enough free space.
3. **Auto-restart.** When a run ends, it records the score and starts a new run, so it keeps chasing a higher best.

## Watch controls

- **1× WATCH** — real-time play, the way you'd play it.
- **4×** — four times faster.
- **MAX** — as fast as the AI can decide, to find the peak score quickly.
- **PAUSE / RESTART** — freeze the game, or start a fresh run.

The panel shows the current strategy (PATROL / HUNTING / SURVIVING / WAITING OUT HAZARD), score, best, and runs played.

## Results (headless testing)

- Greedy-only AI: best **43** over 10 runs.
- Cycle + greedy AI: best **68** and still climbing when the test ended; the cycle guarantees survival, hazards are the only thing that can force a fatal deviation.

## Files

- `index.html` — the game (logic identical to Neon Serpent №10, plus a one-line turbo gate so the AI can drive steps at 4×/MAX) with the AI and watch panel.
- `test_ai.py` — headless Playwright test: plays at MAX, logs deaths and best score.
