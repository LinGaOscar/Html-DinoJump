# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the Project

No build step required. Open `index.html` directly in a browser, or serve with a local HTTP server:

```
python -m http.server
```

No package manager, no dependencies, no linter, no test suite. Verify changes by playing through the flow in a browser: start, jump, double jump, collision/game over, restart, and fly mode trigger (10 obstacles cleared).

## Architecture

Single-file vanilla JS game built on HTML5 Canvas 2D API. No assets directory — the dinosaur and cactus are drawn as vector silhouettes directly on canvas (`drawDinoShape`, `drawCactusShape` in `script.js`), not sprite images.

**Key files:**
- `index.html` — canvas element + start/game-over overlay UI, loads `style.css` and `script.js`
- `js/script.js` — all game logic (~445 lines)
- `css/style.css` — UI styling (start/gameover screens, CSS custom properties for theming)
- `doc/github-pages.md` — GitHub Pages deployment walkthrough
- `dev.md`, `README.md` — human-facing docs in Traditional Chinese; both describe gameplay rules and structure, so update them when behavior changes

UI strings and code comments are in Traditional Chinese — keep new ones consistent.

**Game loop** (`gameLoop()` in `script.js`): driven by `requestAnimationFrame`.

**Two entity types:**
- `player` object — dinosaur with jump physics (gravity `0.6`, jump force `-12`, scaled by canvas height); supports a **double jump** (`jumpCount < 2`)
- `Obstacle` class — cactus that moves left; speed = `obstacleSpeedBase + (score / 100)`; size/timing randomized

**State:** global variables (`gameActive`, `score`, `highScore`, `frameCount`, `obstaclesPassed`). High score persists via `localStorage` (`dinoHighScore`).

**Collision:** AABB with intentionally shrunk hitboxes (`checkCollision`) for better gameplay feel. Press `D` in-game to toggle a debug overlay that draws the actual hitboxes.

**Fly mode (easter egg):** every time the player clears an increasing threshold of obstacles (starts at 10, then +10 each time), a 5-second invulnerable "flight" mode triggers (`startFlyMode`/`endFlyMode`) with a confetti particle effect and a 1-second post-flight grace period before collisions resume.

**Responsive layout:** `resize()` recalculates canvas size, ground height, dino size, gravity, and speed from the container's current dimensions on load and on window resize. All sizes derive from `canvas.height / 400`. The container itself is sized in CSS (`#game-container`: fixed 2:1 aspect ratio, max 800px wide); portrait phones (≤768px) get a full-screen `#rotate-overlay` asking the user to rotate.

## Non-obvious behavior

- **Mixed time bases:** physics, obstacle movement and spawn timing (`nextSpawnFrame`, every 90–159 frames) advance per frame with no delta-time, so game speed follows the display refresh rate. Fly mode duration and the grace period use `performance.now()` wall-clock time instead.
- **Obstacle speed is fixed at spawn** (`Obstacle` constructor). The `score / 100` term is not multiplied by the canvas scale factor, unlike `obstacleSpeedBase`.
- **Fly mode counter:** `obstaclesPassed` is never reset during a run; `flyModeTriggerAt` advances by 10 instead. Obstacles cleared while flying still add score but do not increment `obstaclesPassed`.
- **Canvas colors are hard-coded hex literals** in `script.js` that duplicate the CSS custom properties in `style.css` (`#2C2825` = `--ink`, `#D4CEC4` = `--ground`, `#B8916A` = `--accent`, `#F5F2EC` = `--bg`). A palette change must be made in both files.
- **Input:** Space starts the game only from the start screen and jumps while playing; after game over, only the restart button restarts. `mousedown` on the canvas jumps.
- **`checkCollision` also draws** the debug hitboxes, so they appear only while collisions are being evaluated (not during fly mode or the grace period).
- **Fonts** (Playfair Display, DM Mono) load from Google Fonts in `index.html`; the fly mode banner on canvas uses Playfair Display.

## Deployment

GitHub Pages: enable Pages in repository settings, deploy from root of `main` branch.
