# Space Invaders — Full Implementation Plan

## Goal
Build a classic Space Invaders game as one offline `index.html` with inline CSS and JS, canvas rendering and keyboard controls. It needs no build step, libraries or assets.

## Plan

### Architecture
- A single `/index.html` holds a `<canvas id="game">`, a small HUD and inline `<style>` and `<script>`.
- The logical resolution is 224×256, drawn at an integer scale (CSS `image-rendering: pixelated`, `imageSmoothingEnabled=false`). The canvas is letterboxed to fit the viewport.
- All JS sits in one IIFE with these parts: `state`, `input`, `update(dt)`, `render()`, and a `requestAnimationFrame` loop with a fixed timestep (60 Hz accumulator, with dt clamped).
- Sprites are small bitmap arrays (strings or bitmasks) drawn with `fillRect`. Aliens have 2 animation frames each, in 3 types: squid, crab and octopus.
- Retro style: black background, green/white/red palette, monospace font (`"Courier New", monospace`), a CSS glow on the canvas, and optional CRT scanlines through a CSS overlay.

### Entities
- **Player:** a cannon at the bottom, moving at about 100 px/s and clamped to the playfield. Only one player bullet may be on screen at a time. It has 3 lives and a brief invulnerability and respawn blink after a hit.
- **Alien grid:** 5 rows × 11 columns, with the top row squid, rows 2–3 crab and rows 4–5 octopus. The grid steps horizontally on a timer. At an edge it drops one row and reverses. Step interval = f(aliens alive), so it speeds up as aliens die. Each new wave starts the grid one row lower, up to a cap.
- **Alien bullets:** up to 3 on screen. A random bottom-most alien in a column fires, with a wave-scaled fire rate. Use 2 bullet shapes (zigzag and straight).
- **Bunkers:** 4 bunkers, each a pixel grid (for example 22×16 with an arch cutout). Hits erase a small radius of pixels (a per-pixel boolean array). Alien bodies touching a bunker also erase it.
- **UFO:** appears every 20–30 s at the top, crossing in a random direction. Hitting it gives a mystery score of 50, 100, 150 or 300. Each appearance picks randomly from those values.

### Game states
`menu → playing ⇄ paused → (waveClear → playing) → gameOver → menu`
- **menu:** title, score table (squid 30, crab 20, octopus 10, UFO ?), "Press Enter to start".
- **playing:** the normal loop.
- **paused:** P or Esc; draws a "PAUSED" overlay and freezes the simulation. Pause automatically on `visibilitychange`.
- **waveClear:** a 1.5 s "Wave N" banner, then the next wave starts with a harder setup. Score and lives carry over.
- **gameOver:** triggers when lives hit 0 or an alien reaches the player line. It shows the final score and best score, and "Enter to restart".

### Scoring and lives
- Squid 30, crab 20, octopus 10, UFO 50–300.
- Extra life at 1500 points, once (max 5 lives).
- HUD shows `SCORE`, `HI-SCORE` (saved in `localStorage` in try/catch), `LIVES` (ship icons) and `WAVE`.

### Controls
- Left/Right or A/D to move, Space to fire (ignore `e.repeat`), P/Esc to pause, Enter to start or restart.
- Call `preventDefault` on game keys. Track key state in a `Set`, and clear it on blur.
- Optional on-screen touch buttons, shown only under `(pointer: coarse)`.

## Implementation Checklist
- [x] 1. Create `index.html` with the page shell, canvas, CSS (retro theme, scaling, centering) and instructions line.
- [x] 2. Add the game loop (fixed timestep), canvas scaling and a state machine skeleton with the `menu` screen.
- [x] 3. Add input handling (keyboard, `Set`, blur reset, `preventDefault`).
- [x] 4. Add sprite data and a `drawSprite` helper. Render a static alien grid, player and bunkers.
- [x] 5. Implement player movement and shooting (one bullet at a time).
- [x] 6. Implement alien grid stepping, edge drop, animation frame toggle and speed-up as aliens die.
- [x] 7. Add collisions (AABB): player bullet vs aliens, bunkers and UFO; alien bullet vs player and bunkers; bullet vs bullet (optional).
- [x] 8. Implement alien firing, plus the player death and respawn sequence with lives.
- [x] 9. Implement pixel-erasable bunkers.
- [x] 10. Add the UFO spawn timer, movement and random score.
- [x] 11. Add scoring, HUD, hi-score persistence and the extra life.
- [x] 12. Add `paused`, `waveClear` and `gameOver` states and overlays, and make the reset logic clean.
- [x] 13. Add difficulty scaling per wave (start row, fire rate, alien speed).
- [x] 14. Add touch controls and responsive tuning.
- [x] 15. Optional: WebAudio beeps, wrapped so the game works without them. Add a mute key (M).
- [x] 16. Do a manual test pass (below), then update `README.md` with how to run and play.

## Manual Test Pass
- The game opens from `file://` with no console errors and no network requests.
- Each state transition works: menu → play → pause → resume → game over → restart.
- Bullets, bunker erosion, the UFO score, lives, the extra life and the alien-reaches-bottom loss all work.
- Clearing a wave starts a faster wave and keeps the score and lives.
- The hi-score persists across a reload, and the game still runs with `localStorage` blocked.
- The game works at different window sizes, and the tab-blur auto-pause works.
- The game runs at the same speed on 60 Hz and 144 Hz displays.

## Files to Modify
- `README.md`: add run and play instructions and the controls.
- `plan/space-invaders.md`: optionally replace it with, or merge in, the plan above.

## New Files
- `index.html`: the whole game.
- `plan/space-invaders-plan.md`: this plan, kept as a separate file alongside the scout's notes in `plan/space-invaders.md`.

## Risks
- The speed-up curve and the firing rate need playtesting.
- Per-pixel bunker erosion can be slow if it's done naively. Use a small `Uint8Array` per bunker.
- Key events can get stuck on blur, so clear the key set then.
- Use original, simple sprites and no copied arcade art or audio. The code is MPL-2.0, as the repo's `LICENSE` says.
