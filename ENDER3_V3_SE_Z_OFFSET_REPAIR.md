# Ender 3 V3 SE — Z-Offset Repair Manual (BLTouch + HX711/Strain Gauge)

This manual is written for a **real failure scenario**: an Ender 3 V3 SE with both a **BLTouch** and the stock **HX711 strain-gauge Z sensor** where Z=0 is inconsistent, first layers vary, and auto-calibration disagrees between runs. Follow the sections in order; each step assumes the printer is currently misconfigured.

---

## 1) How Z-homing and Z=0 are defined on this printer

**What BLTouch does**
- Deploys a pin, touches the bed, and reports a trigger height relative to the probe body.
- Firmware stores a **probe offset** (nozzle-to-probe delta). Z=0 is interpreted as “nozzle touches bed after subtracting the stored offset.”
- Probe repeatability is limited by pin friction, dirt on the pin, bed temp, and mount rigidity.

**What HX711 strain-gauge Z sensing does**
- The hotend carriage sits on a flexure tied to an **HX711 load cell**. When the nozzle contacts the bed, the strain changes and is read by the HX711.
- Z=0 is interpreted at **actual nozzle contact**, so no separate probe offset exists, but **preload and mechanical flex** become the dominant error sources.
- If preload is too low, noise triggers early; if too high, you crash the bed before the signal trips.

**How firmware decides Z=0**
- **Marlin**: `Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN` or BLTouch/other probes set a virtual Z endstop. `M851` stores the offset; `M500` saves it. Homing sets the machine origin using the active endstop/probe.
- **Klipper**: `[probe]` or `[bltouch]` creates `probe:z_virtual_endstop`. `PROBE_CALIBRATE` writes a persistent `z_offset` to `printer.cfg` via `SAVE_CONFIG`. Homing sets Z=0 using that virtual endstop.

**Why running BLTouch and HX711 together is fragile**
- You create **two different Z references**: one at the probe pin height, another at nozzle contact. Any macro, G-code, or mesh that switches between them silently shifts Z=0.
- BLTouch offsets are temperature-agnostic; HX711 readings drift with heat, preload, and flex. Mixing them makes results non-repeatable.
- Bed meshes and Z fade are applied **after** the base Z reference; if the reference jumps between runs, the mesh can’t fix it.

**Conclusion**: Pick **one** Z reference for consistency. If you must use both, strictly separate when each is active and never let both define the virtual endstop in the same session.

---

## 2) Common root causes (ranked by impact)

### Mechanical
- Probe mount flex, loose screws, or plastic sagging when hot.
- BLTouch pin dirty, bent, or with incorrect free-fall distance (should be ~3 mm exposed when deployed).
- HX711 preload wrong: too little (false triggers), too much (nozzle digs into bed).
- Backlash or play in Z lead screw, coupler, or V-wheel tension; eccentric nuts too loose/tight.
- Bed plate not rigid (warped spring steel, loose bed screws, wobbling Y-carriage wheels).

### Electrical / signal integrity
- Noisy HX711 wiring (long unshielded leads, shared bundle with stepper wiring).
- Poor ground between mainboard and HX711 module; loose JST crimp on BLTouch.
- BLTouch 5V sag (shared with servo rail) causing random “touch” triggers.
- Cable drag changing strain-gauge reading during moves.

### Firmware / config
- Both sensors enabled as Z endstops (virtual endstop swapped by macros).
- Wrong probe offsets (`M851` in Marlin, `z_offset` in Klipper) or offsets not saved (`M500`/`SAVE_CONFIG`).
- Safe home missing, so homing over bed edges with warped frame.
- Mesh re-used after hardware changes; Z fade too aggressive (`fade_end` too low).

### G-code and EEPROM conflicts
- Start G-code re-applying old offsets (`M851`, `M206`, `SET_GCODE_OFFSET`) or re-running a different probe.
- EEPROM/`SAVE_CONFIG` disabled, so offsets vanish after reboot.
- Macros mixing BLTouch probing for mesh with HX711 contact for Z offset.

### Slicer overrides
- First-layer Z “babystep” plugins, per-filament Z-offset adjustments, or negative initial layer height.
- Elephant-foot compensation altering expected first-layer thickness.

---

## 3) Decide: use BLTouch or HX711 — not both at once

**Recommended for reliability:** Use **one** Z reference.

- **If you want simplicity and consistent offsets across temps:** Use **BLTouch only**; disable HX711 endstop participation.
  - Disconnect or ignore HX711 in firmware; keep `[bltouch]` (Klipper) or BLTOUCH settings (Marlin) as the only virtual endstop.
- **If you prefer true nozzle contact and can tolerate sensitivity:** Use **HX711 only**.
  - Remove `[bltouch]` / BLTouch endstop mapping. Use `[probe]` tied to the HX711 virtual pin (Klipper) or equivalent Marlin driver. Expect more sensitivity to preload and heat.

**If you must keep both (not recommended):**
- Use BLTouch **only for meshing** and explicitly set `probe: bltouch` in mesh macros.
- Use HX711 **only for final Z offset** and ensure the virtual endstop is switched once and never redefined mid-session.
- After switching, **re-home Z** so the active endstop is applied.

---

## 4) Physical inspection checklist (do before firmware tweaks)

1) **Hotend & carriage rigidity**
   - Check all carriage screws; ensure the heatsink clamp is tight.
   - Verify V-wheels: no wobble, no flat spots; adjust eccentric nuts for smooth, backlash-free travel.
   - Confirm Z coupler tightness on the leadscrew and motor shaft.

2) **BLTouch**
   - Pin drops freely under gravity; no burrs or filament dust. Clean with IPA, do **not** lubricate.
   - Pin stroke ≈ 3 mm. If too long/short, remount per Creality spec.
   - Mount square to bed; no shims unless documented. Reprint bracket if flexing.

3) **HX711 / strain gauge**
   - Inspect flexure plate for cracks or rubbing.
   - Verify HX711 board firmly mounted and wires strain-relieved.
   - Preload: with power off, lightly press nozzle—bed should deflect slightly without hitting stops.
   - Ensure cable loom does not tug on the carriage during Z moves.

4) **Bed & frame**
   - Tighten bed screws evenly; springs or spacers should be compressed enough to prevent wobble.
   - Check Y-carriage wheels for play; adjust eccentric nuts.
   - Inspect build plate flatness; replace if visibly warped.

---

## 5) Firmware-specific guidance

> **First ask:** Are you on **Marlin** or **Klipper**? Follow the matching subsection only.

### Marlin
- Enable **one** probe:
  - BLTouch: `#define BLTOUCH`, `#define Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN` (or dedicated pin), `#define NOZZLE_TO_PROBE_OFFSET {X,Y,Z}`.
  - HX711 strain: use the specific driver for your board; map it as the single Z endstop.
- Safety: `#define Z_SAFE_HOMING`, `#define BABYSTEP_DISPLAY_TOTAL`, `#define RESTORE_LEVELING_AFTER_G28`.
- Persistence: `#define EEPROM_SETTINGS` and run `M500` after `M851` updates.
- Test probe repeatability: `M48 P10 V4`; std dev should be <0.01 mm for BLTouch.
- Disable conflicting G-code in start scripts: remove legacy `M851`/`M206` unless intentionally setting a known value.

### Klipper
- Use **one** virtual endstop:
  - BLTouch only: keep `[bltouch]`, ensure `endstop_pin: probe:z_virtual_endstop` on `stepper_z`. Remove any `[probe]` that points to HX711 for Z.
  - HX711 only: define `[probe]` with the HX711 pin/driver and use it as the sole `z_virtual_endstop`. Comment out `[bltouch]`.
- Safe homing: use `[safe_z_home]` to center over the bed.
- Bed mesh: create only **after** Z offset is stable. Set `fade_start` ≥0.3, `fade_end` 3–5 for reliability.
- Persistence: run `PROBE_CALIBRATE` → `TESTZ Z=-0.1` steps → `ACCEPT` → `SAVE_CONFIG`. Confirm `z_offset` updated in `printer.cfg`.
- Avoid mixed macros: ensure start macros do **not** call both PR-touch (HX711) and BLTouch in the same job.

---

## 6) Correct calibration order (do not mix steps)

1. Complete the **physical checklist** (Section 4).
2. Choose **one** sensor path (Section 3) and disable the other in firmware.
3. **Clear old offsets:**  
   - Marlin: `M502` (load defaults), `M500`.  
   - Klipper: remove stale `SET_GCODE_OFFSET` lines from macros; ensure only one `z_offset` entry remains.
4. **Home with the chosen probe only** (`G28` in Marlin, `G28`/`QUICK_HOME` in Klipper via macro).
5. **Probe repeatability test** (Marlin `M48`, Klipper `PROBE_ACCURACY`) until std dev <0.01 mm.
6. **Z-offset calibration at bed center, hot** (no mesh yet):
   - Heat bed to your normal first-layer target **plus 10°C** to preload for thermal expansion (example: target 60°C → set 70°C); set hotend to 200°C; wait 5 minutes.
   - Use paper/feeler gauge while stepping Z in -0.02 mm until light drag.
   - Store offset (Marlin `M851 Z<value>` + `M500`; Klipper `ACCEPT` + `SAVE_CONFIG`).
7. **Create fresh mesh** (Marlin `G29`, Klipper `BED_MESH_CALIBRATE`; then `SAVE_CONFIG` if required).
8. **First-layer validation print** with no slicer Z tweaks. Adjust slicer elephant-foot compensation only after firmware offset is stable.

Never run **auto Z-offset** and **mesh** simultaneously in a macro; always lock the offset first, then mesh.

---

## 7) Repeatable Z-offset procedure (detailed commands)

### BLTouch path (recommended for consistency)
**Marlin**
1. `M502` → `M500` (clear old values).  
2. `G28` (all axes).  
3. `M48 P10 V4` (repeatability check).  
4. Heat: `M190 S60`, `M109 S200`.  
5. `G1 X110 Y110 Z10 F6000` (center).  
6. `G1 Z0.2` then `M211 S0` (disable soft endstops temporarily).  
7. Lower in 0.02 mm steps (`G1 Z-0.02`) until paper drags. Note Z value.  
8. `M851 Z<noted Z>` → `M500` → `M211 S1`.  
9. `G28 Z` to verify; nozzle should just grip paper at Z=0.

**Klipper**
1. `G28`.  
2. `PROBE_ACCURACY` (repeatability).  
3. Heat bed/hotend; wait 5 minutes.  
4. `PROBE_CALIBRATE`.  
5. Use `TESTZ Z=-0.05` steps until paper drag is correct.  
6. `ACCEPT` → `SAVE_CONFIG`. Confirm `z_offset` updated.  
7. `BED_MESH_CALIBRATE` → `SAVE_CONFIG`.

### HX711 / strain-gauge path (single-sensor mode only)
**Marlin**
1. Ensure BLTouch is **disabled** in firmware.  
2. `M502` → `M500`.  
3. `G28`. If Z stalls or grinds, reduce preload before retrying.  
4. Heat to your typical print temperatures; set the bed **10°C above** the normal first-layer target to account for thermal expansion settling, then wait 5 minutes.  
5. `G1 X110 Y110 Z5` then `G30` (single probe).  
6. If firmware supports `PROBE_CALIBRATE`, follow on-screen steps; otherwise measure with paper and set `M851 Z<value>` → `M500`.  
7. `G29` for mesh.

**Klipper**
1. Comment out `[bltouch]`; ensure `[probe]` points to HX711.  
2. `G28`. If trigger is erratic, adjust preload until `PROBE_ACCURACY` std dev <0.02 mm.  
3. Heat bed/hotend; wait.  
4. `PROBE_CALIBRATE` → `TESTZ` fine steps → `ACCEPT` → `SAVE_CONFIG`.  
5. `BED_MESH_CALIBRATE` → `SAVE_CONFIG`.

**Verifying the offset actually applied**
- Marlin: `M503` should show the new `M851 Z` value. After power cycle, `M503` must still show it.  
- Klipper: `STATUS` shows the loaded config; `z_offset` line in `printer.cfg` must match. Power-cycle Moonraker/Klipper and confirm the value persists.

---

## 8) Why a mesh does not fix first-layer inconsistency

- A mesh corrects **relative bed tilt/warp**, not the **absolute nozzle-to-bed distance**. If the base Z reference shifts, the entire mesh is wrong.
- BLTouch repeatability error (~±0.01 mm) plus HX711 drift can exceed your first-layer thickness (0.2 mm), causing random over/under squish.
- Mesh density cannot compensate for mechanical flex, cable drag, or preload drift.
- Z fade blends the mesh out; if `fade_end` is too low, compensation vanishes before the first layer finishes.

---

## 9) Validation tests (run after calibration)

1) **Home consistency test**  
   - Run `G28` → `G1 Z10` five times in a row. After each home, move to `Z=0` with paper. If feel changes, the endstop is unstable (sensor or mount).

2) **Probe repeatability**  
   - Marlin: `M48 P10 V4`. Std dev >0.01 mm = clean/repair probe.  
   - Klipper: `PROBE_ACCURACY SAMPLES=10`. Look for standard deviation (σ) ≤0.01 mm (BLTouch) or ≤0.02 mm (HX711).

3) **Thermal drift check**  
   - Heat bed to 60°C, hotend 200°C. Run `PROBE_ACCURACY`. Re-run after 15 minutes. If drift grows, inspect preload/mount and reduce strain on the HX711 harness.

4) **Bed mesh sanity**  
   - After `G29`/`BED_MESH_CALIBRATE`, inspect max-min height. If >0.3 mm on a flat plate, mechanical tilt or flex remains.

5) **First-layer stripe test**  
   - Print a 200×200 mm single-layer square at 0.2 mm. Expect uniform sheen and width. If one side is light, re-check belt tension and bed leveling; if random, probe repeatability or mixed offsets are to blame.

---

## 10) Known-good, conservative baseline (favoring reliability)

### If using BLTouch only (recommended)
- Disable HX711 as a Z endstop (Klipper: comment out `[probe]` tied to HX711 and remove macros that swap to it; Marlin: do not select HX711 driver).
- Conservative BLTouch params: `speed: 5`, `samples: 3`, `samples_tolerance: 0.0075`, `samples_tolerance_retries: 5`, `stow_on_each_sample: False`, `probe_with_touch_mode: True`.
- Safe home at bed center; mesh after offset is locked.
- Start G-code: **do not** issue `M851`, `M206`, or `SET_GCODE_OFFSET`; rely on stored firmware offset.

### If using HX711 only
- Ensure the HX711 probe is the **only** `probe:z_virtual_endstop`. Remove/comment `[bltouch]` or Marlin BLTouch defines.
- Set modest probing speed (Klipper `speed: 3-5` if supported) and verify preload allows consistent triggers without bed crash.
- Re-run offset any time preload or carriage tension changes.

### Shared slicer rules
- First layer height 0.20–0.24 mm, width 110%, speed ≤20 mm/s, fan off for the first 2–3 layers.
- Avoid slicer Z-offset hacks; keep them at 0 once firmware offset is corrected.

Apply this baseline, then perform the calibration sequence in Section 7. With a single Z reference, clean wiring, and saved offsets, Z=0 will be stable and first layers will become repeatable.

---

## 11) Quick troubleshooting map

- **Nozzle digs in after homing:** Wrong/unsaved offset or HX711 preload too high; verify only one probe defines Z. Re-run Section 7.
- **First layer alternates high/low between prints:** Mixed probes or probe repeatability failure; inspect Section 3 + Section 4; rerun `PROBE_ACCURACY`.
- **Mesh looks fine but prints lift:** Z reference shifting—redo offset with hot hardware, confirm no start-G-code overrides.
- **BLTouch “triggered prior to move”:** Cable or pin friction; clean pin, check 5V sag, reroute away from steppers.
- **HX711 reports random triggers:** Shield/shorten wires, improve ground, reduce cable drag, set stable preload.

Follow the steps sequentially; do not skip mechanical and electrical checks. The printer will only hold a stable Z=0 once **one** reference sensor is chosen, calibrated hot, and saved. With that locked in, bed mesh can finally do its job.
