# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris on HTML5 Canvas. No `package.json`, no build, no tests, no linter. README is in Spanish.

## Running

Open `index.html` directly, or serve statically (`python3 -m http.server 8000`, `npx serve .`) and visit `http://localhost:8000`.

## Architecture

Three files, all logic in `game.js` (~300 lines, `'use strict'`, global script scope, loaded by `<script src="game.js">` at end of `index.html`):

- `game.js` looks up DOM elements by id at load time (`board`, `next-canvas`, `score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`). Renaming an id in `index.html` breaks the script.
- Game state lives in module-level `let` variables (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, ...). `init()` resets all of it; the restart button calls `init()` too.
- Board is a `ROWS x COLS` matrix of `0` or a color index 1-7. The same index keys both `COLORS` and `PIECES`, and the piece matrices embed that index as their cell value.
- Loop: `requestAnimationFrame(loop)` accumulates time; when it exceeds `dropInterval` the piece falls or `lockPiece()` runs. Lock flow: `merge` -> `clearLines` -> score/level -> `spawn`. A spawn that collides calls `endGame()`.
- Rotation: `rotateCW` (transpose + reverse), `tryRotate` applies simple wall kicks (shifts of +-1, +-2 columns).
- Scoring: `LINE_SCORES` times level; soft drop +1/row, hard drop +2/cell. Level up every 10 lines. Fall interval is `max(100, 1000 - (level-1)*90)` ms.

## Gotchas

- Canvas size is hard-coded in `index.html` (`#board` 300x600, `#next-canvas` 120x120). If you change `COLS`, `ROWS` or `BLOCK` in `game.js`, update the canvas `width`/`height` to `COLS*BLOCK` x `ROWS*BLOCK`.
- Keep the controls list in the `index.html` side panel in sync with the `keydown` handler in `game.js` (arrows, `X`/Up rotate, Space hard drop, `P` pause).
