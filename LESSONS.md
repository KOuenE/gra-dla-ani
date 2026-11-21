# Lessons ledger (append-only)

Read at the start of every session. One lesson per line group, with the measured fact.

## 2026-11-21
- The whole game lives encrypted inside `index.html` (AES-GCM, PBKDF2 600 000 rounds, 3 key slots = 3 passwords).
  Every key slot unwraps the same content key, so re-encrypt with the decrypted content key and keep the `w` array
  as it is: all old passwords keep working. Round trip checked: decrypt(encrypt(x)) == x byte for byte.
- `beforeunload` saves the game state, so `localStorage.clear(); location.reload()` does NOT give a fresh game in a
  test: the old state is written back on unload. Stub `__app.save` first.
- New features go in their own `<script>` placed before the main bundle (it only defines `window.__ania`); the bundle
  calls it after the scenes are registered. Five one-line hooks in the bundle: init, camp depth list, camp effects
  layer (under the UI), battle units built, one simulated day. Keeping the bundle untouched otherwise made every
  feature testable on its own.
- Camp tutorial values after the first days are "upgrades", "injury", "bed", "standings" (not only "done"): a guard
  on `tuto.camp === "done"` hid every new feature after the first upgrade hint.
- Overlays freeze `scene.frame`, so anything that walks in the camp waits while the newspaper is open (wanted).
- Test in the browser with `tools/web-eye` (`serve`, `drive --stage 650x450 --browser /opt/pw-browsers/chromium`,
  `send`); its Python Playwright needs `pip install playwright==1.58.0` against the preinstalled Chromium.

## 2026-11-21 (nowa gra)
- The game is now an original title (own engine, art, synth music); the old bundle and its audio are gone:
  plain build 560 KB instead of 2.4 MB. Source lives only inside the encrypted payload, as one script.
- A headless balance run (whole game, auto-casting player, 30 runs) is the quick check after any stat change:
  last run 42 matches to the end, battles ~30 s, win rate by league 92/66/79/84 %.
- Headless Chromium draws ~110 crowd heads per frame at 41 fps; drawing the arena once into a buffer and the crowd
  from cached sprites gave 44 fps there and 60 fps in camp. Keep static layers cached.
- Stage coordinates in web-eye are game pixels (800x450 here), not screenshot pixels: a click from a 2x screenshot
  must be halved.
