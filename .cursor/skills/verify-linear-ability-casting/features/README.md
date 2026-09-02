# Elemental Sandbox verification map

This directory is the maintained source for verifying user-facing behavior of the Vite + Three.js sandbox (`casting-abilities`). Read this index before driving, then use the matching feature file.

## Baseline preconditions

- Repo root after `npm install`.
- Doctor PASS (`chiro-verify doctor`) or INCONCLUSIVE-only because node is missing (do not drive in that case).
- For CI: `chiro-verify shell` against `npm run preview` (or `vite preview` after `npm run build`). `GET /` includes `canvas#viewport` and `#loader`.
- For casts: a browser with WebGL. If headless GL is missing, INCONCLUSIVE for those recipes; still PASS the HTML shell.
- Bind preview/dev to an instance this run started. Default dev is `127.0.0.1:5173`; preview is typically `4173`. Do not attach to a stranger's Vite.
- Character mesh: `public/models/Idle.fbx` (plus other FBX under `public/models`).

## Driving conventions

- Start from the loaded page (loader veil may still be visible on first GET; that is enough for the shell).
- Prefer element ids from `index.html`: `#viewport`, `#hud`, `#loader`, `#loader-status`, `#loader-fill`.
- Keyboard codes from `src/input/InputManager.js` (not tab order).
- Cast clicks must target `#viewport`, not lil-gui or HUD cards (those swallow pointerdown).
- Restore nothing on disk; the sim is in-memory. `C` clears VFX. Do not delete evidence.

## Proof and skip reporting

- Capture the action and the resulting state, not only the final canvas.
- HTML shell proof: HTTP status, body containing `id="viewport"` and `id="loader"`.
- Cast proof: screenshot of aim indicator (arrow vs circle) plus post-click state; HUD toast / `[data-paused]` where relevant.
- Record feature file name with every artifact.
- Missing WebGL → INCONCLUSIVE for cast entry points, never a silent skip marked PASS.
- Do not report Q as verified by driving V.

## Feature entry contract

Each feature file starts with an H1 and one paragraph of user-visible behavior, then exactly four H2s:

1. `Sub-features`
2. `How to get to it (user POV)`
3. `Driving it with chiro-verify`
4. `Gotchas`

## Features

- [Line casts](./line-cast.md) — Q/E/R/F League-style arrow, click to fire.
- [Far cast](./far-cast.md) — V (and X) ground circle with thick boundary.
- [Pause and editor](./pause-editor.md) — P freezes the sim; ~938 lil-gui sliders stay live.
