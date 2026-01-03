# 3d-printer-config-backuo

Klipper configuration for Ender 3 V3 SE with Mainsail

## Recent Updates

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