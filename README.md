# SoccerGame (HTML5 Canvas Single-File Prototype)

A self‑contained 11v11 top‑down soccer prototype implemented entirely in a single `index.html` using vanilla JavaScript and the HTML5 Canvas. Includes simple AI, human mouse controls (pass & shoot), player attributes, stamina drain, formations, and debugging overlays.

## Features

- 11v11 simulation on a 120m x 80m scaled pitch
- Human control (default left/blue team) with:
  - Left‑click: aim & release inside field to pass
  - Right‑click hold → release: charged shot (power bar)
  - Keyboard helpers: `G` give ball, `P` test pass, `S` test shot
- AI decision making: passing, dribbling, shooting heuristics per role
- Formations & tactical parameters (press, leash, progression)
- Player attributes: ability, stamina (affects speed/accel), roles (GK/DEF/MID/FWD)
- Ball physics with delta‑time integration, friction, wall bounces, kick cooldown
- Visual HUD: clock, score, formation, AI pass slider, pass intent lines, velocity vector
- Debug panel (top‑right) with live mouse, ball, owner & timing info

## Quick Start

Serve the folder with any static web server (or just open `index.html` directly; a server is recommended for consistent performance).

Using `http-server` (Node):

```bash
npx http-server . -p 8081
```

Open: <http://localhost:8081/index.html>

Or simply double‑click `index.html` (note: some browsers restrict local font/loading behaviors; server preferred).

## Controls

| Action | Input | Notes |
| ------ | ----- | ----- |
| Pass | Left‑click press & release inside field | Auto‑gives you the ball if you click while the other team has it (for testing) |
| Shoot | Right‑click hold, release | Power scales over 2.5s (SHOT_MIN→SHOT_MAX) |
| Give Ball | `G` | Chooses closest non‑GK player on human side |
| Test Pass | `P` | Programmatic forward pass with current pass intensity |
| Test Shot | `S` | Fixed‑power shot toward goal |
| Toggle Zones | Checkbox UI | Shows tactical leash circles |
| Toggle Vectors | Checkbox UI | Shows player target vectors |

## Core Systems

### Coordinate & Scaling

- Logical field size: 120 (width) x 80 (height) units
- Canvas resized to preserve aspect; `scale = canvas.width / FIELD.w`
- Mouse conversion: `(client - rect.left) / scale` → field units

### Ball Physics

- Stored as position + velocity; integrated with `dt` each frame
- Friction: `vx *= pow(0.995, dt*60)` (frame‑rate independent)
- Kick: direction normalized, velocity = `power * dir`, plus slight position offset to avoid instant re‑pickup
- Cooldown (~0.25s) before kicker can automatically re‑possess

### Passing

`kickPassTowardPoint(passer, tx, ty, passInt)`:

1. Logs debug info
2. Computes direction & small error based on ability
3. Scales pass power from pass intensity slider (≈40–52 u/s)
4. Calls `ball.kickAt(...)`

### Shooting

- Charged via right‑click hold; linear ramp from `SHOT_MIN` to `SHOT_MAX`
- Slight random vertical targeting error based on ability

### AI Outline

- Chooses passes by evaluating lane clearance, progression, distance preferences, and teammate role bands
- Shoots when in range or under pressure (simplified heuristic)
- GKs attempt outlet passes automatically

### Stamina & Movement

- Player speed/accel scaled by stamina (60% base + 40% * stamina%)
- Stamina decays with distance moved; displayed as green bar

### Debugging Aids

- Console logs for mouse events, pass/shot initiation, ball kicks
- Cyan dashed line for last pass target (fades after 4s)
- Semi‑transparent white velocity vector for ball
- `debugPanel` shows real‑time numeric state

## File Overview

| File | Purpose |
| ---- | ------- |
| `index.html` | Full game (logic + UI) |
| `test.html` | Minimal isolated mouse & ball control experiment |

## Tuning Parameters (search in code)

- `SHOT_MIN`, `SHOT_MAX`, `SHOT_CHARGE_S`
- Pass power formula inside `kickPassTowardPoint`
- AI pass slider (`#mgrPass` range input)
- Friction constants (ball: 0.995, players: 0.98 base exponential)
- Kick cooldown (`pickupCooldown` and `cooldown` usages)

## Common Issues

| Symptom | Likely Cause | Fix |
| ------- | ------------ | --- |
| Click pass does nothing | Mouse up outside field or not owning ball | Ensure release inside bounds & you own ball (auto‑give triggers on mousedown) |
| Ball seems slow | Power too low vs dt, or speed slider < 1 | Adjust pass/shot power or raise speed slider |
| Ball snaps back to kicker | Missing offset / cooldown | Confirm latest code (kick adds offset + cooldown) |
| Misaligned clicks | Canvas CSS size mismatch | Canvas JS sets width/height; avoid external CSS scaling |

## Extending

- Modularize into separate JS modules (physics, ai, input)
- Add collision with players for tackles/interceptions
- Implement passing prediction (lead target) and curved shots
- Add sound & UI animations for goals / whistles

## License

Prototype—add your preferred license here (e.g., MIT).

---

Feel free to iterate on mechanics; the structure favors quick experimentation over architecture complexity.

## Media (Screenshots & Video)

Add a `media/` folder alongside `index.html` and place assets using the suggested filenames below. Replace the placeholders once you capture real gameplay.

### Images

| Purpose | Suggested File | Markdown |
| ------- | --------------- | -------- |
| Main gameplay overview | `media/screenshot-gameplay.png` | `![Gameplay](media/screenshot-gameplay.png)` |
| Pass debug overlay | `media/screenshot-pass-debug.png` | `![Pass Debug](media/screenshot-pass-debug.png)` |
| Shot power charging | `media/screenshot-shot-charge.png` | `![Shot Charge](media/screenshot-shot-charge.png)` |

Embed examples (will show as broken until files exist):

![Gameplay](media/screenshot-gameplay.png)
![Pass Debug](media/screenshot-pass-debug.png)
![Shot Charge](media/screenshot-shot-charge.png)

### Video / Animation

Prefer a short looping GIF ( < 10 MB ) for quick preview plus a higher‑quality MP4/WebM.

1. Record with a tool like macOS QuickTime or OBS (~10–15s)
2. Trim and export MP4 (e.g., `media/demo.mp4`)
3. Convert a smaller GIF (e.g., `media/demo.gif`)

Markdown / HTML embeds:

```markdown
![Demo GIF](media/demo.gif)

<video src="media/demo.mp4" controls width="640" muted loop></video>
```

Add an external link if you upload to a platform (YouTube, Loom):

> Full video: <https://youtu.be/your_video_id>

### Capturing Tips

- Hide the debug panel for clean shots (comment out or temporarily hide element)
- Use browser zoom so the pitch fills more of the frame (but keep aspect intact)
- Capture a sequence: (1) kickoff, (2) pass, (3) shot, (4) goal message
- Keep cursor visible for instructional GIFs

---

Once images are added, this section will auto‑render them on GitHub; no further README edits needed unless you change filenames.
