# Guía de Inicio Rápido / Quick Start Guide

## 🇪🇸 Español

### ✅ Cambios Aplicados

Se han realizado mejoras críticas en la configuración de tu impresora para solucionar:
- ❌ Problemas de adherencia a la cama
- ❌ Capas que no se imprimen (impresión en el aire)
- ❌ Formación de bolas de filamento

### 🚀 Pasos Siguientes (IMPORTANTE)

#### 1️⃣ Reinicia Klipper
En la interfaz de Mainsail, ejecuta:
```
FIRMWARE_RESTART
```

#### 2️⃣ Nivela la Cama Manualmente (NUEVO - CRÍTICO)
Este es un paso **OBLIGATORIO** para resolver problemas por zonas:
```
BED_LEVEL_SCREWS_TUNE
```
Sigue las instrucciones en pantalla y ajusta cada tornillo según lo indicado. Repite hasta que todos los tornillos estén dentro de tolerancia.

#### 3️⃣ Recalibra el Z-Offset (CRÍTICO)
Esto es **OBLIGATORIO** después de los cambios. Ejecuta:
```
AUTO_Z_OFFSET
```
Este comando recalibrará automáticamente el offset Z con la nueva configuración de alta precisión y guardará el resultado.

#### 4️⃣ Prueba de Adherencia (Recomendado)
Antes de imprimir una pieza real, prueba la adherencia:
```
FIRST_LAYER_TEST BED_TEMP=60 EXTRUDER_TEMP=200
```
Ajusta las temperaturas según tu filamento:
- **PLA**: BED_TEMP=60 EXTRUDER_TEMP=200-210
- **PETG**: BED_TEMP=80 EXTRUDER_TEMP=230-240
- **ABS**: BED_TEMP=100 EXTRUDER_TEMP=240-250

#### 5️⃣ Primera Impresión Real
Usa el mismo comando START_PRINT en tu slicer:
```gcode
START_PRINT EXTRUDER_TEMP={material_print_temperature_layer_0} BED_TEMP={material_bed_temperature_layer_0}
```

### 🔧 Ajustes Finos

**Si la primera capa está muy alta (no se adhiere):**
1. Durante la impresión, ve a "Ajustes" → "Z-Offset"
2. Reduce el valor en pasos de -0.02mm
3. Cuando esté bien, ejecuta: `AUTO_Z_OFFSET` para guardar

**Si la primera capa está muy baja (raya la cama):**
1. Durante la impresión, ve a "Ajustes" → "Z-Offset"
2. Aumenta el valor en pasos de +0.02mm
3. Cuando esté bien, ejecuta: `AUTO_Z_OFFSET` para guardar

### 📚 Más Información
- **Solución completa y avanzada**: [ADVANCED_FIRST_LAYER_FIXES.md](ADVANCED_FIRST_LAYER_FIXES.md) - **¡NUEVO!**
- **Detalles técnicos**: [FIRST_LAYER_FIXES.md](FIRST_LAYER_FIXES.md)
- **Solución de problemas**: Ver sección "Troubleshooting" en FIRST_LAYER_FIXES.md

---

## 🇬🇧 English

### ✅ Applied Changes

Critical improvements have been made to your printer configuration to fix:
- ❌ Bed adhesion problems
- ❌ Missing layers (printing in air)
- ❌ Filament blob formation

### 🚀 Next Steps (IMPORTANT)

#### 1️⃣ Restart Klipper
In the Mainsail interface, run:
```
FIRMWARE_RESTART
```

#### 2️⃣ Level Bed Manually (NEW - CRITICAL)
This is a **MANDATORY** step to fix zone-dependent issues:
```
BED_LEVEL_SCREWS_TUNE
```
Follow the on-screen instructions and adjust each screw as indicated. Repeat until all screws are within tolerance.

#### 3️⃣ Recalibrate Z-Offset (CRITICAL)
This is **MANDATORY** after the changes. Run:
```
AUTO_Z_OFFSET
```
This command will automatically recalibrate the Z offset with the new high-precision configuration and save the result.

#### 4️⃣ Adhesion Test (Recommended)
Before printing a real part, test adhesion:
```
FIRST_LAYER_TEST BED_TEMP=60 EXTRUDER_TEMP=200
```
Adjust temperatures for your filament:
- **PLA**: BED_TEMP=60 EXTRUDER_TEMP=200-210
- **PETG**: BED_TEMP=80 EXTRUDER_TEMP=230-240
- **ABS**: BED_TEMP=100 EXTRUDER_TEMP=240-250

#### 5️⃣ First Real Print
Use the same START_PRINT command in your slicer:
```gcode
START_PRINT EXTRUDER_TEMP={material_print_temperature_layer_0} BED_TEMP={material_bed_temperature_layer_0}
```

### 🔧 Fine Tuning

**If first layer is too high (doesn't stick):**
1. During print, go to "Settings" → "Z-Offset"
2. Decrease value in -0.02mm steps
3. When good, run: `AUTO_Z_OFFSET` to save

**If first layer is too low (scrapes bed):**
1. During print, go to "Settings" → "Z-Offset"
2. Increase value in +0.02mm steps
3. When good, run: `AUTO_Z_OFFSET` to save

### 📚 More Information
- **Complete advanced solution**: [ADVANCED_FIRST_LAYER_FIXES.md](ADVANCED_FIRST_LAYER_FIXES.md) - **NEW!**
- **Technical details**: [FIRST_LAYER_FIXES.md](FIRST_LAYER_FIXES.md)
- **Troubleshooting**: See "Troubleshooting" section in FIRST_LAYER_FIXES.md

---

## 📊 Resumen de Mejoras / Summary of Improvements

| Mejora / Improvement | Antes / Before | Ahora / Now |
|---------------------|----------------|-------------|
| Muestras BLTouch / BLTouch Samples | 3 | 5 |
| Tolerancia probe / Probe tolerance | 0.02mm | 0.0075mm |
| Puntos mesh / Mesh points | 100 (10x10) | 121 (11x11) |
| Altura purga / Purge height | 0.28mm | 0.25mm |
| Velocidad purga / Purge speed | F900 | F600 |
| Compensación cama / Bed fade | 5mm | 3mm |
| Velocidad 1ª capa / 1st layer speed | F1200 (20mm/s) | F900 (15mm/s) |
| Estabilización térmica / Heat soak | ❌ No | ✅ 30s |
| Limpieza boquilla / Nozzle clean | ❌ No | ✅ Sí/Yes |
| Ajuste tornillos / Screw adjust | Manual | ✅ Asistido/Assisted |
| Macro de prueba / Test macro | ✅ FIRST_LAYER_TEST | ✅ FIRST_LAYER_TEST |

## ⚠️ Recordatorio Importante / Important Reminder

**¡DEBES ejecutar `BED_LEVEL_SCREWS_TUNE` y `AUTO_Z_OFFSET` antes de imprimir!**
**You MUST run `BED_LEVEL_SCREWS_TUNE` and `AUTO_Z_OFFSET` before printing!**

Los cambios en la precisión del probe y la densidad del mesh requieren una recalibración completa para funcionar correctamente.

The changes in probe accuracy and mesh density require a complete recalibration to work correctly.
