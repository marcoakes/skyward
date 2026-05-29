# Skyward — A Floating Islands Adventure

A classic-style 2D platformer that runs in any browser **and on iOS Safari** (on-screen touch controls).
The whole game is a single self-contained file — **`index.html`** — with no build step, no
dependencies, and no downloaded assets: every sprite, tile, background layer and sound is
generated in code.

### ▶ Play it

**Live:** https://marcoakes.github.io/skyward/

Or locally:

```bash
open index.html          # macOS
# or serve it (best for mobile testing on your LAN):
python3 -m http.server 8000   # then visit http://localhost:8000/
```

### Controls

| Action | Desktop | Mobile / iOS |
|--------|---------|--------------|
| Move   | `←` `→` or `A` `D` | on-screen ◀ ▶ |
| Jump   | `Space`, `↑`, `W`, `Z` | on-screen ▲ |
| Pause  | `P` or the ❚❚ button | the ❚❚ button |
| Music  | the ♪ button | the ♪ button |

Jump feel includes **coyote time**, a **jump buffer**, and variable jump height
(tap = short hop, hold = full jump).

### What's in it

- A green-hooded adventurer with idle / run / jump frames (pixel art drawn in code).
- Patrolling snails — stomp them from above (+200), touch them from the side and you lose a life.
- Spinning gold coins (+100) and a red goal flag to reach.
- A **hand-designed, paced level**: deliberate gaps, floating brick & cloud platforms,
  coin trails over each jump, and a brick stair up to the flag.
- A warm sky gradient with sun glow, parallax snow-capped mountains / clouds / floating
  grass islands, procedurally textured tiles, particles, and a CRT scanline + vignette overlay.
- 3 lives, a 300-second timer, a tiny WebAudio chiptune + SFX.

See [`CLAUDE.md`](CLAUDE.md) for the full architecture notes and ideas for extending it.

---
🤖 Generated with [Claude Code](https://claude.com/claude-code)
