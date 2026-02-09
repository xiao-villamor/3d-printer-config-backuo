# 3d-printer-config-backuo

Klipper configuration for Ender 3 V3 SE with Mainsail

## 🚀 Quick Start

**Just updated the configuration?** → See [**START_HERE.md**](START_HERE.md) for immediate next steps!

**¿Problemas con la primera capa?** → Consulta [**GUIA_PROBLEMAS_PRIMERA_CAPA.md**](GUIA_PROBLEMAS_PRIMERA_CAPA.md) para diagnóstico completo

**Z-offset inestable o inconsistente?** → Lee [**ENDER3_V3_SE_Z_OFFSET_REPAIR.md**](ENDER3_V3_SE_Z_OFFSET_REPAIR.md) para una guía completa de reparación

For detailed setup: [QUICK_START.md](QUICK_START.md)

## Recent Updates

**First Layer Optimization (2026-01-05)** - Optimized BLTouch probing speed, improved purge line parameters, added G-code arcs support, and comprehensive Spanish troubleshooting guide. See [GUIA_PROBLEMAS_PRIMERA_CAPA.md](GUIA_PROBLEMAS_PRIMERA_CAPA.md) for complete diagnostics.

**Advanced First Layer & Support Fixes (2026-01-05)** - Comprehensive solution for bed adhesion, zone-dependent issues, and support structure problems. See [ADVANCED_FIRST_LAYER_FIXES.md](ADVANCED_FIRST_LAYER_FIXES.md) for complete details.

**First Layer & Print Quality Fixes (2026-01-04)** - Fixed bed adhesion issues, missing layers, and mid-air printing problems. See [FIRST_LAYER_FIXES.md](FIRST_LAYER_FIXES.md) for details.

**Dynamic Meshing Fix** - Fixed adaptive bed meshing configuration to resolve irregular first layer issues. See [DYNAMIC_MESHING_FIX.md](DYNAMIC_MESHING_FIX.md) for details.

## Configuration Files

- `printer.cfg` - Main printer configuration for Ender 3 V3 SE
- `macro.cfg` - G-code macros including START_PRINT, END_PRINT, and utility macros
- `prtouch.cfg` - PR Touch sensor configuration
- `mainsail.cfg` - Mainsail web interface configuration
- `moonraker.conf` - Moonraker API configuration

## Key Features

- **Adaptive Bed Meshing**: Automatically probes only the print area for faster start times
- **BLTouch Support**: Configured for automatic bed leveling
- **PR Touch Integration**: Z-offset calibration with pressure sensor
- **TMC2209 Drivers**: Silent stepper motor operation
- **Filament Management**: Load/unload macros with temperature checks

## Usage

### Starting a Print
Use the following in your slicer's start G-code:
```
START_PRINT EXTRUDER_TEMP={material_print_temperature_layer_0} BED_TEMP={material_bed_temperature_layer_0}
```

### Calibration
- **Auto Z-Offset**: Run `AUTO_Z_OFFSET` macro from Mainsail
- **PID Tuning**: Use `PID_EXTRUDER` or `PID_BED` macros
- **Bed Mesh**: Automatically created on each print, or manually with `BED_MESH_CALIBRATE`
