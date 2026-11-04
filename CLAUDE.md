# CLAUDE.md

## What this is

A 3D prototype for a university entrepreneurship class: a **plug-in smart lid** that turns a
household's existing wheelie bin (120/240 L) into a front-door drop point for sorted recyclables.

Business idea (from the instructor's feedback): people are willing to sort but don't want to
carry waste far, and don't care much about income → put the drop point at the front door.
The payer is a saleng association (registered members with smartphones) or a buyer like
Wastebuy, whose pain is time spent sorting at each stop: residents pre-sort into bags, the
truck scans the lid to open it, takes whole bags, and sorts at its own station.
Each bag carries an owner tag — a QR sticker or the house code written in marker — so the
station can credit the right household.

## Files

- `index.html` — the whole prototype: Three.js scene + HTML overlay. No build step.
- `research/user-tests-round3.md` — notes from earlier user tests (TC1–TC10).
- The old 2D app / wireframe board / `theme.css` were removed; they're in git history
  (before the "3D smart lid" commit) if needed.

Open `index.html` directly or serve the folder (`python -m http.server`). Needs internet on
first load for three.js r128 (jsDelivr UMD build + `examples/js/controls/OrbitControls.js`)
and Google Fonts.

## Structure of `index.html`

- CSS tokens at the top (`--ink`, `--accent`, `--warn`, `--blue`, …), light + dark under
  `:root[data-theme="dark"]`. Use the tokens; check both themes.
- Scene colors for the street are in `SCENE.light` / `SCENE.dark` (applied in `applyTheme()`).
  Output is linear (no sRGB encoding) on purpose so hex colors render as written.
- Models are built from primitives in one IIFE: street, `bin` (generic wheelie bin),
  `oldLid`, `lid` (collar, 4 `clamps`, front `plate` with e-ink `screen` canvas, `hinge`,
  `lidPivot` → solar, LED strip, `latch`, fill `sensor`), bags (`makeBag`, lathe or cardboard
  bundle, sticker canvas in `qr` or `pen` mode), `truck`, 3D `phone` + scan `beam`.
- Canvas textures (fake QR, sticker, e-ink screen) redraw once web fonts load (`redraws`).
- Animation: tiny tween system (`tween`, `wait`, `seq`) cancelled by bumping `GEN`.
- `STEPS` array = the story: 0 overview · 1 install · 2 tag bags · 3 scan & drop ·
  4 truck scans & loads · 5 station scan → credit. Each step has `cam`, `cfg` (scene reset),
  optional `setup`, `body()`/`phone()` HTML for the side panel, and `play(done)`.
  `#n` in the URL opens step n.
- Overlay tags: `CALLOUTS` (part labels with leader lines, offsets in px, halved on phones)
  and `addBagTag()` for per-bag chips; both projected from 3D every frame.
- Numbers: shop prices PET ฿9.5 / cans ฿48 / cardboard ฿3.2 / glass ฿1.2 per kg; household
  share `SHARE = 0.6`, bin `CAP = 6` bags, 20 s open time, BLE unlock — all **assumptions**,
  labelled as such in the UI. Keep them labelled.

## Conventions

- Plain ES5-flavoured JS, no framework, no modules, no build. Keep it that way.
- Thai is the primary UI language; English secondary and muted.
- Business framing lives in the `#biz` dialog ("ใครจ่าย?"); keep it in sync with the steps.
- The truck is labelled just "รถรับซื้อ" (no association name on it).
