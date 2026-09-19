# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Classic Tetris in vanilla JavaScript, HTML5 Canvas, CSS. No dependencies, no build step, no package.json.

## Running

No install/build needed. Open directly or serve statically:

```bash
start index.html          # Windows, open directly
python3 -m http.server 8000
npx serve .
php -S localhost:8000
```

No test suite, linter, or bundler exists in this repo.

## Architecture

Three files, single global scope, no modules:

- `index.html` — DOM: `<canvas id="board">` (300×600, 10×20 grid at 30px/cell), `<canvas id="next-canvas">` for piece preview, HUD spans (score/lines/level), overlay div for pause/game-over.
- `style.css` — dark/retro arcade theme.
- `game.js` — all game logic, single file, top-level mutable state (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, etc.), no classes/modules.

Key mechanics in `game.js`:

- **Board**: `ROWS × COLS` matrix, cell value `0` (empty) or `1–7` (piece color index into `COLORS`).
- **Pieces**: hardcoded square matrices in `PIECES`. Rotation via `rotateCW` (transpose + reverse), not precomputed rotation states.
- **Collision**: `collide(shape, ox, oy)` checks bounds and overlap against `board`.
- **Wall kicks**: `tryRotate()` tries offsets `[0, -1, 1, -2, 2]` after rotating, keeps first that doesn't collide.
- **Game loop**: `loop(ts)` driven by `requestAnimationFrame`, accumulates `dt` into `dropAccum`, drops one row when it exceeds `dropInterval`.
- **Line clear**: `clearLines()` scans bottom-up, splices full rows, unshifts empty rows at top.
- **Scoring**: `LINE_SCORES = [0, 100, 300, 500, 800]` × `level`; hard drop = 2 pts/cell, soft drop = 1 pt/row.
- **Leveling**: level = `floor(lines / 10) + 1`; `dropInterval = max(100, 1000 - (level-1)*90)`.
- **Ghost piece**: `ghostY()` projects current piece straight down to landing row; drawn at `globalAlpha = 0.2`.
- Input handled by one `keydown` listener (Arrow keys, `X` rotate, `Space` hard drop, `P` pause).

To tune board size/speed, see the constants table in README.md (`COLS`, `ROWS`, `BLOCK`, `COLORS`, `LINE_SCORES`, `dropInterval`) — note `BLOCK`/`COLS`/`ROWS` changes require matching the `<canvas id="board">` width/height in `index.html`.

README.md is in Spanish.
