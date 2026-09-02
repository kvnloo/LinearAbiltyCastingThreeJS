# Pause and live editor (P)

Every visible parameter is a live lil-gui slider (~938 of them). They stay live while the simulation is paused. Freeze a frame with **P**, then reshape silhouette, palette, and timing against a still image.

## Sub-features

- `pause-toggle` — KeyP toggles pause; `#hud [data-paused]` shows "Paused" when frozen.
- `pause-sliders-live` — Changing a lil-gui control while paused updates the frozen frame (no rebuild).
- `editor-toggle` — KeyG toggles the editor panel.
- `help-toggle` — KeyH toggles `.hud__help`.
- `clear-vfx` — KeyC clears spawned effects without toggling pause.

## How to get to it (user POV)

- Cast anything (or not). Press **P** to pause. Press **G** if the editor is hidden. Drag sliders. Press **P** again to resume. Help is **H**.

## Driving it with chiro-verify

Preconditions:

- Doctor not FAIL; HTML shell PASS (`#viewport`, `#hud`, `#loader`).
- For slider visual proof, WebGL. Without it: INCONCLUSIVE for slider pixels; you may still PASS pause **DOM** if `[data-paused]` can be observed after a synthetic key event in a real document. In CI without a browser, do not claim pause works — only the shell.

- After `chiro-verify shell`, open the preview URL in a browser.
- Send `KeyP`. Assert `#hud [data-paused]` is visible / active (`Paused`). Screenshot `evidence/$RUN_ID/pause-on.png`.
- Send `KeyG` if needed so `.lil-gui` exists. Change one labeled control (e.g. a colour or `timeScale`). Screenshot `pause-slider.png` showing both the paused badge and the GUI.
- Send `KeyP` again; badge hides; motion resumes if anything was playing.

## Gotchas

- Pause does not block editor writes; a "frozen" screenshot that still matches the old slider values is a FAIL for `pause-sliders-live`.
- Pointerdown on the GUI does not cast (by design).
- Do not count slider widgets as 938 by scraping unless you need that number; the product claim is "938 live sliders" from the README. A missing GUI is FAIL; a different count is a map/product drift to report, not to paper over.
- `npm run preview` is enough; `npm run dev` also works for local interactive proof.
