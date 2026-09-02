# Far cast (V)

Voltaic Snare is a ground-targeted zone: the arrow is replaced by a circle with a deliberately thick boundary that follows the cursor. The circle you measure before the click is the circle you get. X (Glacial Crown) uses the same far-cast shape.

## Sub-features

- `far-arm-v` — KeyV / Digit5 arms Voltaic Snare; circle indicator, not an arrow.
- `far-arm-x` — KeyX / Digit6 arms Glacial Crown with the same circle language.
- `far-move` — Pointer moves resize/reposition the circle on the ground (`zoneRadius` in settings).
- `far-fire` — Left click on `#viewport` starts the zone VFX inside that footprint.
- `far-hud` — HUD help text states V and X are far casts; matching `.ability-card` works.

## How to get to it (user POV)

- From the loaded sandbox, press **V** (or **5**), or click the Voltaic Snare card.
- Move the mouse; a thick-boundary circle tracks. Click the ground to commit. Esc / right click cancels.

## Driving it with chiro-verify

Preconditions:

- Doctor not FAIL; `chiro-verify shell` PASS for `#viewport` and `#loader`.
- WebGL present. Else INCONCLUSIVE for this file, shell still PASS.

- Shell: same `GET /` as line-cast; do not skip the ids.
- Arm V: send `KeyV`. Screenshot `evidence/$RUN_ID/far-arm-v.png`. The aim widget must be a **circle**, not the LoL arrow used by Q/E/R/F.
- Fire: left click on `#viewport`. Screenshot `far-fire-v.png`. Expect a disc/column in the measured radius; HUD `is-armed` clears.
- Optional: arm X and confirm the same circle, not an arrow.

## Gotchas

- Passing a line-cast screenshot does not prove this feature. The whole point is the indicator shape.
- HUD copy: "V and X are far casts — aimed with a circle, not an arrow."
- Headless CI without GL: INCONCLUSIVE, not FAIL.
- Right-drag orbits the camera (`OrbitControls`); a right press also cancels an armed far cast.
