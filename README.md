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

Two small icons under the game, nothing else covering it: **pause** (⏸/▶) and **restart** (↻). It plays at normal speed. The game's own HUD shows the score and best.

When a run ends, the AI records the score and starts a new run on its own, so it keeps chasing a higher best.

## Results (headless testing)

- Greedy-only AI: best **43** over 10 runs.
- Cycle + greedy AI: best **68** and still climbing when the test ended; the cycle guarantees survival, hazards are the only thing that can force a fatal deviation.

## Files

- `index.html` — the game (logic identical to Neon Serpent №10, plus a one-line pause gate so the pause icon can freeze stepping) with the AI and two icon buttons.
- `test_ai.py` — headless Playwright test: plays at MAX, logs deaths and best score.
