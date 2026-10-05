# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Asteroids clone in plain HTML5 Canvas + vanilla JavaScript. No dependencies, no bundler, no package.json, no tests, no linter, and not a git repo. UI text and comments are in Spanish; keep that convention.

## Running

Open `index.html` directly in a browser, or serve the folder (`npx serve .`, then http://localhost:3000). There is no build step; reload the page after editing.

## Architecture

All game logic lives in `game.js` (loaded by `index.html`, which only provides an 800x600 `<canvas id="canvas">`). `W`/`H` are hardcoded constants in `game.js` and must match the canvas size in `index.html`.

- **Loop**: `loop(ts)` → `update(dt)` → `draw()` via `requestAnimationFrame`. `dt` is in seconds and clamped to 0.05.
- **Entities**: classes `Ship`, `Asteroid`, `Bullet`, `Particle`, each with `update(dt)`/`draw()` and a `dead` flag. Collections (`bullets`, `asteroids`, `particles`) are reassigned with `.filter(x => !x.dead)` each frame; new asteroids from splits are collected and concatenated after the bullet-collision pass.
- **Game state**: module-level `let` globals (`ship`, `score`, `lives`, `level`, `state`, `deadTimer`). `state` is `'playing' | 'dead' | 'gameover'`; `update()` branches on it. `initGame()` resets everything, `nextLevel()` fires when no asteroids remain (spawns `3 + level`).
- **Input**: `keys[code]` holds held state (rotate/thrust are polled); `pressed(code)` consumes a one-shot "just pressed" edge (used for shooting and restart, so holding Space does not auto-fire).
- **Space is toroidal**: everything wraps with `wrap(v, max)`, except particles, which don't wrap.
- **Asteroid sizes** are 1/2/3, indexed into the parallel arrays `RADII`, `SPEEDS`, `POINTS`. Adding or changing size tiers means updating all three. Asteroid vs. ship collision uses `radius * 0.82` for forgiving hitboxes; collisions are circle-based via `dist()`.

## Notes

README.md mentions power-ups and a "shooting star" asteroid type; neither exists in `game.js` yet.
