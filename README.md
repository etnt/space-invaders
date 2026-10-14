# Space Invaders

Relax, have some fun and play **Space Invaders** — the classic 2D arcade game as a single, self-contained HTML page. No build step, no dependencies, no assets; it runs fully offline.

Run it straight from [here](https://etnt.github.io/space-invaders/)

## How to Run

Open `index.html` in any modern browser — double-click it, or:

```sh
open index.html          # macOS
xdg-open index.html      # Linux
```

Everything (markup, CSS, JavaScript, sprites, sound) is embedded in that one file.

## How to Play

Shoot all 55 invaders before they reach the ground. The formation marches side to side, drops down at each edge, and speeds up as its ranks thin. Duck behind the four bunkers — but they erode pixel by pixel with every hit, and aliens grind through them too. The red UFO crossing the top is worth a mystery bonus.

| Key | Action |
| --- | --- |
| Left / Right or A / D | Move cannon |
| Space | Fire (one shot on screen at a time) |
| P or Esc | Pause / resume |
| Enter | Start game / restart after game over |
| M | Mute / unmute sound |

On touch devices, on-screen buttons appear automatically.

## Scoring

| Target | Points |
| --- | --- |
| Squid (top row) | 30 |
| Crab (middle rows) | 20 |
| Octopus (bottom rows) | 10 |
| UFO | 50–300 mystery |

- Extra life at 1,500 points (once, max 5 lives).
- Hi-score is saved locally and persists across reloads.

## Features

- 5×11 alien formation with two-frame animation, edge drops, and speed that scales with survivors and wave number
- Pixel-erasable bunkers (shots and alien bodies chew through them)
- Mystery UFO on a 20–30 s timer
- Wave progression: each wave starts lower, fires faster, and marches quicker
- Fixed-timestep 60 Hz game loop (frame-rate independent)
- Retro green-on-black presentation with CRT scanlines and optional WebAudio beeps
- Auto-pause when the tab loses visibility

## Project Structure

- `index.html` — the entire game
- `plan/` — design and implementation plan documents
- `LICENSE` — Mozilla Public License 2.0
