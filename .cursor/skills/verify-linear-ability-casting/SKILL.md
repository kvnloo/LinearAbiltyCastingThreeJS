---
name: verify-linear-ability-casting
description: Verify the Elemental Sandbox (casting-abilities) Vite + Three.js canvas — launch, doctor the production build, drive the HTML shell and ability casts, capture evidence. Use when proving line casts (Q/E/R/F), far casts (V), pause/editor (P), or the HUD/loader shell still work.
---

# Verify linear ability casting (Elemental Sandbox)

Project-local verification skill for **kvnloo/LinearAbiltyCastingThreeJS** (`package.json` name `casting-abilities`). Surface is a Vite + Three.js page: canvas `#viewport`, HUD `#hud`, boot veil `#loader`. Dependencies: `three ^0.185.1`, `vite ^8.1.5`, `lil-gui`. License MIT; there is no `CONTRIBUTING.md`.

Read `features/` before driving. This file is for the next agent, not a human walkthrough.

## Launch

Repo root. No extra env or seed data. Vite is bound to a single host/port; do not double-drive a shared instance.

```bash
npm install
npm run dev
```

`vite.config.js` serves `127.0.0.1:5173` (`open: false`). Ready when the process prints a local URL and `GET http://127.0.0.1:5173/` returns HTML.

For CI / doctor-backed proofs prefer the production preview (no WebSocket HMR, closer to `npm run build`):

```bash
npm install
npm run build
npm run preview
```

Vite preview defaults to **4173** (config does not override `preview.port`). Record the printed URL. Assets (`public/models/Idle.fbx` and other FBX/PNG) are served from `public/`.

Teardown: kill **the PID this run started** (see Helpers). Never kill by process name.

## Doctor

Read-only health of the checkout. Prefer the helper:

```bash
.cursor/skills/verify-linear-ability-casting/bin/chiro-verify doctor
```

Rules:

- **`npm run build` must exit 0** for PASS. That is the gate (`vite build`, `target: es2022`).
- If **`node` is missing**, verdict is **INCONCLUSIVE**, not FAIL. Same if `npm` is missing.
- FAIL only when node/npm exist and the build exits non-zero, or `package.json` / `vite.config.js` are absent.

Optional notes (do not fail doctor): `package.json` scripts `dev` / `build` / `preview`; `three`, `vite`, `lil-gui` present.

## Drive

Harness: HTTP for the HTML shell; a real browser (CDP / Playwright / the desktop) only for WebGL casts. Helper:

```bash
.cursor/skills/verify-linear-ability-casting/bin/chiro-verify shell
```

`shell` runs `npm run build` if needed, starts `npm run preview` (or `vite preview` after a successful build) on a free port, then `GET /`.

**HTML shell (required in CI):** response body MUST include `canvas` with `id="viewport"` and the loader (`id="loader"`). Also expect `#hud` in the same document (`index.html`). PASS the shell when those nodes are present. Do **not** require WebGL, canvas pixels, or FBX decode in CI.

**Casts (browser, optional):** after the loader veil drops, the canvas owns input (`InputManager` on `#viewport`). Pointer events that begin on HUD/editor DOM are ignored.

| User action | Handle | Observable |
| --- | --- | --- |
| Line cast | `KeyQ`/`Digit1` Frost Lance, `KeyE`/`Digit2` Storm Lance, `KeyR`/`Digit3` Cinder Fall, `KeyF`/`Digit4` Nova Beam | LoL-style ground **arrow** while armed; left click on `#viewport` fires |
| Far cast | `KeyV`/`Digit5` Voltaic Snare (also `KeyX`/`Digit6` Glacial Crown) | Ground **circle** with thick boundary follows cursor |
| Pause | `KeyP` | `#hud [data-paused]` "Paused"; 938 lil-gui sliders stay live |
| Cancel | `Escape` or right click on viewport | Aim indicator gone |
| Editor / help | `KeyG` editor, `KeyH` help | lil-gui / `.hud__help` |

If headless GL / WebGL is missing, **INCONCLUSIVE for cast**, **PASS for HTML shell**. Do not FAIL the run solely because `webgl` / `swiftshader` is absent.

Drive recipes live in `features/`. Cover every file in that map across a full pass; a single GET `/` is not a complete product proof.

## Evidence

Write under `.cursor/skills/verify-linear-ability-casting/evidence/` (gitignored except `.gitkeep`). Use a run id subdirectory, e.g. `evidence/$RUN_ID/`.

Proof standards:

- Exercise the real user path (keys on the live page, click `#viewport`), not internal emitters or test-only endpoints. There are none.
- Capture **action + resulting state**: doctor log + build exit code; `GET /` status and a snippet showing `#viewport` and `#loader`; for casts, a screenshot of the armed arrow/circle **and** a screenshot after click (VFX or HUD toast / `is-armed`).
- Side effects: `dist/` from a passing build; preview access log. No DB. Do not commit `dist/` or `node_modules/`.
- Mocks: none. Missing WebGL is INCONCLUSIVE, not a mocked PASS.
- Cleanup must not delete this directory's artifacts.

Minimum files for a doctor+shell run:

- `doctor.log` — full helper output and `VERDICT=`
- `shell.html` — saved `GET /` body
- `shell.log` — preview URL, HTTP status

Cast runs add `armed.png` / `cast.png` and the feature id in a `meta.txt`.

## Cleanup

Only tear down instances **this run started**. Keep evidence.

```bash
# if the helper recorded a PID:
kill "$PREVIEW_PID"
# only if that PID is still the preview you started; confirm via /proc/$PID/cmdline
```

Do not kill vite by name. Do not delete `evidence/`. Do not remove `node_modules/` unless this run created a throwaway clone. `dist/` may stay; it is gitignored.

## Helpers

Executable: `.cursor/skills/verify-linear-ability-casting/bin/chiro-verify`

```bash
.cursor/skills/verify-linear-ability-casting/bin/chiro-verify doctor
.cursor/skills/verify-linear-ability-casting/bin/chiro-verify shell
.cursor/skills/verify-linear-ability-casting/bin/chiro-verify shell --port 4173
```

- `doctor` — install-if-needed is allowed; gate is `npm run build` exit 0. Missing node → INCONCLUSIVE.
- `shell` — preview after build, GET `/`, assert `#viewport` + `#loader`. Starts a child process; prints `PREVIEW_PID=` and `PREVIEW_URL=` for cleanup.

Keep the helper executable (`chmod +x`).

## Maintenance

`/maintain-verification-skill` when abilities, keys, or `index.html` ids change.
