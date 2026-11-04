# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas). No build, no deps, no package.json, no tests, no linter. README.md (Spanish) documents features/controls; UI strings in code are Spanish too.

## Run

Open `index.html` directly, or serve statically: `python -m http.server 8000` → http://localhost:8000.

## Architecture

- `index.html`: DOM + two canvases (`#board` 300×600, `#next-canvas` 120×120), HUD spans, `#overlay` (pause/game over).
- `game.js`: all logic in one file, global script (`'use strict'`, no modules). Mutable state is module-level `let` vars declared together (`board`, `current`, `next`, `score`, ...), reset in `init()`.
- Flow: `init()` → `spawn()` → `requestAnimationFrame(loop)`. `loop` accumulates `dropAccum` vs `dropInterval`, then drops or `lockPiece()` (merge → clearLines → spawn). `spawn()` calls `endGame()` if new piece collides.
- Pieces/board use numeric ids 1–7 indexing `COLORS`/`PIECES`; 0 = empty. Rotation = `rotateCW` + simple kicks `[0,-1,1,-2,2]` (not SRS).
- Pause/game over cancel the rAF; `togglePause` restarts `loop` manually. Keydown handler ignores input when paused/over.

## Gotchas

- Changing `COLS`/`ROWS`/`BLOCK` requires updating canvas `width`/`height` in `index.html` (`COLS*BLOCK` × `ROWS*BLOCK`).
- Speed formula duplicated in `clearLines` and `init` (initial 1000ms): `max(100, 1000 - (level-1)*90)`.
- README mentions project dir `03-tetris/` and `style.css` exists but is not in `game.js` scope (styling only).
