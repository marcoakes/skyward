# Skyward — Claude Code Project Spec

A classic Mario‑style 2D platformer with a lush, parallax "floating islands" look,
playable in any browser **and on iOS Safari** (on‑screen touch controls).
The whole game ships as a single self‑contained file: **`skyward.html`** (no build step,
no dependencies, no assets to download — all graphics and sound are generated in code).

This file is written so you can drop the folder into **Claude Code** and say things like
*"run the game"* or *"add a new enemy type"* and it has everything it needs.

---

## Quick start

The game is one HTML file. There is nothing to install.

```bash
# Option A — just open it
open skyward.html            # macOS
xdg-open skyward.html        # Linux
start skyward.html           # Windows

# Option B — serve it (needed if you later split assets out, and best for mobile testing)
python3 -m http.server 8000
# then visit http://localhost:8000/skyward.html
```

**Testing on iPhone / iPad:** run the server above, find your computer's LAN IP
(`ipconfig getifaddr en0` on macOS), and open `http://<that-ip>:8000/skyward.html`
on the device. For a full‑screen app feel, tap Share → *Add to Home Screen*
(the meta tags for that are already in the file).

---

## Controls

| Action | Desktop | Mobile / iOS |
|--------|---------|--------------|
| Move   | `←` `→` or `A` `D` | on‑screen ◀ ▶ buttons |
| Jump   | `Space`, `↑`, `W`, or `Z` | on‑screen ▲ button |
| Pause  | `P` or the ❚❚ button | the ❚❚ button |
| Music  | the ♪ button | the ♪ button |

Jump feel includes **coyote time** (you can still jump just after leaving a ledge)
and a **jump buffer** (an early press still registers on landing), plus variable
jump height (tap = short hop, hold = full jump).

---

## What's in the game

- **Hero:** a green‑hooded adventurer with idle / run / jump frames (drawn as pixel art in code).
- **Enemies:** patrolling snails that turn at walls and ledges; stomp them from above to defeat them, touching them from the side costs a life.
- **Collectibles:** spinning gold coins (+100), stomps (+200).
- **Goal:** a red flag at the end of the level — reach it to win.
- **HUD:** `SCORE`, coin counter, `TIME`, and `LIFE` (hero‑head icons) — same layout as the reference screenshot.
- **Lives & timer:** 3 lives, 300‑second timer; falling into a gap or running out of time costs a life.

### The "impressive graphics" come from
- A warm **sky gradient** with a soft **sun glow**.
- Four **parallax layers** (each pre‑rendered once, then scrolled at different speeds): distant snow‑capped mountains → soft clouds → floating grass islands with little trees → big foreground trees.
- **CRT scanlines + vignette** overlay (CSS) for the retro‑console look.
- Procedurally textured **tiles** (grass‑topped dirt, stone brick, cloud platforms) and a waving flag, coin shimmer, and dust/spark particles.

---

## Architecture (single file `skyward.html`)

It's plain HTML/CSS/Canvas + vanilla JS — easy to read top to bottom. Sections, in order:

1. **CSS / DOM** — the console frame, CRT overlay, top‑right ♪ / ❚❚ buttons, touch pad, start & end screens.
2. **Constants & resize** — physics tuning (`GRAV`, `MOVE`, `JUMP_V`, …) and a 16:9 letterboxed render that fits any screen.
3. **Input** — keyboard map + touch buttons writing into a shared `keys` object.
4. **Audio** — tiny WebAudio engine: SFX (`jump`/`coin`/`stomp`/`hurt`/`win`) and a looping chiptune melody. Resumes on first tap (iOS rule).
5. **Sprites** — `drawHero()` / `drawSnail()` render pixel art to offscreen canvases once.
6. **Background** — `makeMountains/Clouds/Islands/BigTrees()` pre‑render parallax layers; `drawLayer()` tiles them with a parallax factor.
7. **Tiles** — `makeGroundTile()` etc. pre‑render the 48px tile textures.
8. **Level** — `buildLevel()` procedurally generates a 240×13 grid (ground with gaps, floating platforms, coins, snails, a goal flag) using a seeded PRNG, then parses it into entity arrays.
9. **Physics** — axis‑separated AABB collision vs the tilemap (`collideX` / `collideY`, `solidAt`).
10. **Update** — movement, jumping, enemies, coins, particles, camera follow, win/lose.
11. **Render** — sky → parallax → tiles → flag → coins → enemies → particles → player → HUD.
12. **Loop** — fixed‑timestep (120 Hz) update with `requestAnimationFrame` render.
13. **Flow** — start / pause / win / game‑over screens.

### Key knobs to tweak first
- Feel: `GRAV`, `MOVE`, `JUMP_V`, `COYOTE`, `JUMP_BUFFER` (top of the script).
- Level size / difficulty: `ROWS`, `COLS`, and the loops inside `buildLevel()`.
- Tile size: `TILE` (everything scales off it).

---

## Suggested tasks for Claude Code

Good, well‑scoped follow‑ups if you want to keep building:

- **Hand‑design levels** instead of procedural: replace `buildLevel()` with an array of
  ASCII‑map strings (`#`=ground, `X`=dirt, `B`=brick, `=`=cloud, `c`=coin, `s`=snail,
  `P`=player start, `F`=flag) and add a level‑select / next‑level flow.
- **More enemies:** a hopping/flying type, or a shelled snail that slides when stomped.
- **Power‑ups:** mushroom (extra hit), a double‑jump feather, a speed dash.
- **Real art:** swap the procedural sprites for a PNG sprite sheet — load an `Image`,
  `drawImage` source rects per frame; keep `image-rendering: pixelated` for crisp pixels.
- **Persistence:** high score via `localStorage` *(note: localStorage works when the file is
  served/opened directly, but is blocked inside the Claude.ai artifact sandbox — use in‑memory there)*.
- **Mobile polish:** larger hit areas, haptics (`navigator.vibrate`), and a fullscreen toggle.
- **Checkpoints / camera lookahead / moving platforms.**

When extending, keep the single‑file structure unless asked otherwise — it's what makes the
game trivially shareable and instantly playable on iOS.

---

## Notes / known limitations

- All audio is synthesized — there are no music/SFX files to ship.
- Respawn after a death drops the hero near the current camera position; for hand‑built
  levels you'll likely want explicit checkpoints.
- The procedural generator is seeded (`mulberry32(2024)`), so the level is the same each run;
  change the seed in `buildLevel()` for a different layout.
