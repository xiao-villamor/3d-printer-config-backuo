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

#### 2️⃣ Recalibra el Z-Offset (CRÍTICO)
Esto es **OBLIGATORIO** después de los cambios. Ejecuta:
```
AUTO_Z_OFFSET
```
Este comando recalibrará automáticamente el offset Z con la nueva configuración de alta precisión y guardará el resultado.

#### 3️⃣ Prueba de Adherencia (Recomendado)
Antes de imprimir una pieza real, prueba la adherencia:
```
FIRST_LAYER_TEST BED_TEMP=60 EXTRUDER_TEMP=200
```
Ajusta las temperaturas según tu filamento:
- **PLA**: BED_TEMP=60 EXTRUDER_TEMP=200-210
- **PETG**: BED_TEMP=80 EXTRUDER_TEMP=230-240
- **ABS**: BED_TEMP=100 EXTRUDER_TEMP=240-250

#### 4️⃣ Primera Impresión Real
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

#### 2️⃣ Recalibrate Z-Offset (CRITICAL)
This is **MANDATORY** after the changes. Run:
```
AUTO_Z_OFFSET
```
This command will automatically recalibrate the Z offset with the new high-precision configuration and save the result.

#### 3️⃣ Adhesion Test (Recommended)
Before printing a real part, test adhesion:
```
FIRST_LAYER_TEST BED_TEMP=60 EXTRUDER_TEMP=200
```
Adjust temperatures for your filament:
- **PLA**: BED_TEMP=60 EXTRUDER_TEMP=200-210
- **PETG**: BED_TEMP=80 EXTRUDER_TEMP=230-240
- **ABS**: BED_TEMP=100 EXTRUDER_TEMP=240-250

#### 4️⃣ First Real Print
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
- **Technical details**: [FIRST_LAYER_FIXES.md](FIRST_LAYER_FIXES.md)
- **Troubleshooting**: See "Troubleshooting" section in FIRST_LAYER_FIXES.md

---

## 📊 Resumen de Mejoras / Summary of Improvements

| Mejora / Improvement | Antes / Before | Ahora / Now |
|---------------------|----------------|-------------|
| Muestras BLTouch / BLTouch Samples | 1 | 3 (mediana/median) |
| Altura purga / Purge height | 0.3mm | 0.28mm |
| Velocidad purga / Purge speed | F1500 | F900 |
| Compensación cama / Bed fade | 10mm | 5mm |
| Velocidad 1ª capa / 1st layer speed | No definida / Undefined | F1200 (20mm/s) |
| Macro de prueba / Test macro | ❌ No | ✅ FIRST_LAYER_TEST |

## ⚠️ Recordatorio Importante / Important Reminder

**¡DEBES ejecutar `AUTO_Z_OFFSET` antes de imprimir!**
**You MUST run `AUTO_Z_OFFSET` before printing!**

Los cambios en la precisión del probe requieren una recalibración del offset Z para funcionar correctamente.

The changes in probe accuracy require a Z-offset recalibration to work correctly.
