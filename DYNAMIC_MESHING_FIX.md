# Dynamic Meshing Fix for Ender 3 V3 SE

## Problem
The first layer was printing irregularly with bumps/mounds due to improper adaptive bed meshing configuration.

## Root Causes Identified

1. **Incompatible saved mesh data**: The SAVE_CONFIG section contained an old 5x5 mesh, but the configuration specified a 10x10 mesh. This mismatch caused Klipper to use outdated mesh data.

2. **Incorrect START_PRINT sequence**: The `PRTOUCH_PROBE_ZOFFSET` was being called AFTER `BED_MESH_CALIBRATE`, potentially overwriting or interfering with the mesh calibration.

3. **Missing mesh fade parameters**: The bed mesh configuration lacked fade parameters, which help create smooth transitions between mesh points and reduce visible artifacts.

## Changes Made

### 1. Updated `[bed_mesh]` section in printer.cfg
```
[bed_mesh]
speed: 120
horizontal_move_z: 5
mesh_min: 30,30
mesh_max: 205,215
probe_count: 10,10
algorithm: bicubic
adaptive_margin: 5
fade_start: 1          # NEW: Start fading mesh compensation at 1mm
fade_end: 10           # NEW: Completely fade out mesh by 10mm
fade_target: 0         # NEW: Target Z position for fade
```

**What this does:**
- `fade_start`: Begins gradually reducing mesh compensation starting at 1mm height
- `fade_end`: Completely stops applying mesh compensation by 10mm height
- `fade_target`: The Z position to converge to (0 means the configured Z=0)
- Fixed `mesh_max` Y from 215.5 to 215 (whole number for consistency)

### 2. Updated START_PRINT macro sequence in macro.cfg
**Before:**
```
G28
M190 S{BED_TEMP}
BED_MESH_CLEAR
BED_MESH_CALIBRATE ADAPTIVE=1
PRTOUCH_PROBE_ZOFFSET
```

**After:**
```
G28
M190 S{BED_TEMP}
PRTOUCH_PROBE_ZOFFSET           # Moved BEFORE mesh calibration
BED_MESH_CLEAR
BED_MESH_CALIBRATE ADAPTIVE=1
```

**Why this matters:**
The Z-offset needs to be established BEFORE performing the bed mesh calibration. This ensures that the mesh measurements are taken with the correct Z-offset applied.

### 3. Cleared old saved mesh data
Removed the incompatible 5x5 mesh from the SAVE_CONFIG section. Klipper will generate a fresh 10x10 adaptive mesh on the next print.

## How Adaptive Meshing Works

With `BED_MESH_CALIBRATE ADAPTIVE=1`:
- Klipper scans the G-code to find the actual print area
- It only probes the region where your print will be, plus the `adaptive_margin` (5mm)
- This saves time on smaller prints while still providing accurate leveling
- For large prints, it will probe the full area defined by `mesh_min` and `mesh_max`

## After Applying This Fix

### First Print After Update
1. Restart Klipper to load the new configuration
2. The old mesh data has been cleared
3. On the first print, a fresh adaptive mesh will be created
4. The mesh will be saved automatically to SAVE_CONFIG

### What You Should See
- More consistent first layer across the entire print area
- Smoother bed compensation (no more "mounts" or bumps)
- Proper adaptive meshing that only probes the print area

### Troubleshooting

**If the first layer is too high or too low:**
- Run `AUTO_Z_OFFSET` macro from the Mainsail interface
- This will recalibrate your Z-offset and save it

**If you still see irregularities:**
1. Home the printer: `G28`
2. Perform a full mesh calibration: `BED_MESH_CALIBRATE`
3. Save the configuration: `SAVE_CONFIG`
4. View the mesh in Mainsail to check for bed irregularities

**If adaptive meshing doesn't seem to work:**
- Ensure your slicer is configured with: `START_PRINT EXTRUDER_TEMP={material_print_temperature_layer_0} BED_TEMP={material_bed_temperature_layer_0}`
- Check that your G-code contains the actual print moves (adaptive mode needs to scan the file)

## Technical Details

### Probe Offset Calculations
- Probe offset: X=-24.0, Y=-13.0
- Print bed: 230mm x 230mm
- Mesh area: (30,30) to (205,215) - safely within probe reach
- 10x10 probe pattern = 100 measurement points

### Mesh Fade
The mesh fade feature gradually reduces the bed leveling compensation as the print height increases:
- At Z=0 to Z=1mm: Full mesh compensation applied
- At Z=1mm to Z=10mm: Gradually reduced compensation
- Above Z=10mm: No mesh compensation (assumes the print is stable)

This prevents cumulative errors and provides better print quality for taller objects.

## References
- [Klipper Bed Mesh Documentation](https://www.klipper3d.org/Bed_Mesh.html)
- [Adaptive Meshing Guide](https://www.klipper3d.org/Bed_Mesh.html#adaptive-meshes)
