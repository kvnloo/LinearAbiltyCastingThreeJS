# Line casts (Q / E / R / F)

Four skillshots share one aim language: press the key to arm, a League-of-Legends style arrow appears on the ground and swings with the mouse, left-click the canvas to fire. Frost Lance (Q), Storm Lance (E), Cinder Fall (R), Nova Beam (F).

## Sub-features

- `line-arm-q` — KeyQ / Digit1 arms Frost Lance; arrow indicator visible on the ground plane.
- `line-arm-e` — KeyE / Digit2 arms Storm Lance.
- `line-arm-r` — KeyR / Digit3 arms Cinder Fall (lobbed meteor; still a line aim).
- `line-arm-f` — KeyF / Digit4 arms Nova Beam (holds after impact).
- `line-fire` — Left click on `#viewport` while armed confirms the cast; HUD ability bar uses `is-armed` while aiming.
- `line-cancel` — Escape or right-click on the viewport cancels without firing.
- `line-hud-slot` — Clicking an `.ability-card` in `#hud` arms the same slot as the key.

## How to get to it (user POV)

- Load the app, wait until the `#loader` veil can be dismissed / has finished (status in `#loader-status`).
- Press **Q**, **E**, **R**, or **F** (or 1–4), or click the matching HUD card.
- Move the mouse; the arrow tracks. Left click the scene to cast. Esc / right click cancels.

## Driving it with chiro-verify

Preconditions:

- `chiro-verify doctor` did not FAIL.
- `chiro-verify shell` PASS (HTML contains `canvas#viewport` and `#loader`).
- Browser with WebGL. If GL is missing: stop, write INCONCLUSIVE for these sub-features, keep the shell PASS.

- HTML shell: run `chiro-verify shell`; save `GET /` to `evidence/$RUN_ID/shell.html`; assert `id="viewport"` and `id="loader"`.
- Arm Q: focus the page, send `KeyQ` (not into an INPUT). Screenshot the canvas (`evidence/$RUN_ID/line-arm-q.png`). Expect a ground arrow, not a circle. HUD bar has class `is-armed` on `.hud__abilities`.
- Fire: `pointerdown` button 0 with `event.target` the canvas. Screenshot after (`line-fire-q.png`). Expect VFX along the line and/or a HUD toast; bar not armed.
- Repeat E/R/F at least one other slot in a full map pass.
- Cancel path: arm, then `Escape`; arrow gone, no new eruption.

## Gotchas

- Clicks that start on `#hud` or lil-gui never confirm a cast (`InputManager` ignores non-canvas targets).
- Do not require WebGL in CI; a green `GET /` is not a line-cast proof.
- Digit keys 1–4 duplicate Q/E/R/F; do not treat them as extra abilities.
- Nova Beam (F) still plays after landing; do not treat a lingering beam as a stuck arm.
- Character FBX (`public/models/Idle.fbx`) missing yields a broken boot; that is a product/asset failure, not a harness bug.
