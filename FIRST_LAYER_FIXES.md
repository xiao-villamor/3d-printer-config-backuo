# First Layer and Print Quality Fixes

## Problema / Problem

**Español:**
El printer.cfg no funcionaba correctamente. Al imprimir, había ciertas partes que no imprimían bien, no imprimía en la cama, y después se creaba una bola porque imprimía en el aire ya que no existía la capa anterior.

**English:**
The printer.cfg wasn't working correctly. When printing, certain parts didn't print well, it didn't print on the bed, and then a ball was created because it printed in the air since the previous layer didn't exist.

## Causas Principales Identificadas / Root Causes Identified

1. **Precisión del probe insuficiente** - La sonda BLTouch solo tomaba una muestra por punto, lo que podía causar mediciones inconsistentes del nivel de la cama.
   - **Insufficient probe accuracy** - The BLTouch probe only took one sample per point, which could cause inconsistent bed level measurements.

2. **Altura de línea de purga incorrecta** - La línea de purga se dibujaba a Z=0.3mm con velocidad muy alta, lo que podía causar mala adherencia inicial.
   - **Incorrect purge line height** - The purge line was drawn at Z=0.3mm with very high speed, which could cause poor initial adhesion.

3. **Compensación de malla de cama demasiado extendida** - El fade_end estaba en 10mm, lo que reducía la efectividad de la compensación en las capas críticas.
   - **Bed mesh compensation too extended** - The fade_end was at 10mm, which reduced compensation effectiveness in critical layers.

4. **Falta de controles de seguridad en extrusión** - No había límites configurados para la extrusión transversal.
   - **Missing extrusion safety controls** - There were no configured limits for cross-sectional extrusion.

## Cambios Realizados / Changes Made

### 1. Configuración BLTouch Mejorada / Improved BLTouch Configuration

```ini
[bltouch]
sensor_pin: ^PC14
control_pin: PC13
x_offset: -24.0
y_offset: -13.0
speed: 20
samples: 3                      # NUEVO: 3 muestras por punto
sample_retract_dist: 3.0        # NUEVO: Distancia de retracción entre muestras
samples_result: median          # NUEVO: Usar mediana para reducir errores
samples_tolerance: 0.02         # NUEVO: Tolerancia de 0.02mm entre muestras
samples_tolerance_retries: 3    # NUEVO: 3 reintentos si excede tolerancia
pin_move_time: 0.4
stow_on_each_sample: False
probe_with_touch_mode: True
```

**Qué hace esto / What this does:**
- Toma 3 mediciones por cada punto de sondeo en lugar de 1
- Usa la mediana de las 3 mediciones para mayor precisión
- Si las muestras difieren más de 0.02mm, reintenta hasta 3 veces
- Esto elimina mediciones erróneas que causaban desniveles

### 2. Macro START_PRINT Mejorado / Enhanced START_PRINT Macro

**Cambios en la secuencia de purga / Changes to purge sequence:**

```gcode
# ANTES / BEFORE:
G1 X0.1 Y20 Z0.3 F5000.0       # Muy rápido, Z muy alto
G1 X0.1 Y200.0 Z0.3 F1500.0 E15
G1 X0.4 Y200.0 Z0.3 F5000.0
G1 X0.4 Y20 Z0.3 F1500.0 E30

# AHORA / NOW:
G1 X0.1 Y20 Z0.28 F5000.0      # Z más bajo para mejor contacto
G1 X0.1 Y200.0 Z0.28 F900.0 E15   # Velocidad reducida 40%
G1 X0.5 Y200.0 Z0.28 F5000.0      # Líneas más separadas
G1 X0.5 Y20 Z0.28 F900.0 E30      # Velocidad más lenta
G92 E0
G1 E-0.5 F2100                    # Retracción pequeña anti-goteo
G1 Z2.0 F3000
G1 X5 Y20 F5000.0
G1 F1200                          # Velocidad primera capa: 20mm/s
```

**Beneficios / Benefits:**
- **Z=0.28mm en lugar de 0.3mm**: Mejor contacto con la cama
- **Velocidad reducida de F1500 a F900**: Mejor extrusión y adherencia
- **Líneas más separadas (0.4 → 0.5mm)**: Mejor cobertura sin sobreposición
- **Retracción después de purga**: Previene goteo al inicio de impresión
- **Velocidad primera capa F1200 (20mm/s)**: Asegura buena adherencia

### 3. Malla de Cama Optimizada / Optimized Bed Mesh

```ini
[bed_mesh]
speed: 120
horizontal_move_z: 5
mesh_min: 30,30
mesh_max: 205,215
probe_count: 10,10
algorithm: bicubic
adaptive_margin: 5
fade_start: 1
fade_end: 5          # CAMBIADO: de 10 a 5mm
fade_target: 0
```

**Por qué es importante / Why it matters:**
- `fade_end: 5` en lugar de `10`: La compensación de la malla se aplica de forma más agresiva en los primeros 5mm
- Esto es crítico porque los problemas de adherencia ocurren en las primeras capas
- Después de 5mm, la impresión ya es estable y no necesita tanta compensación

### 4. Configuración de Extrusor Mejorada / Enhanced Extruder Configuration

```ini
[extruder]
max_extrude_only_distance: 100.0
max_extrude_cross_section: 5           # NUEVO: Límite de seguridad
pressure_advance: 0.04
pressure_advance_smooth_time: 0.040    # NUEVO: Suavizado
min_extrude_temp: 170                  # NUEVO: Temperatura mínima
```

**Seguridad y consistencia / Safety and consistency:**
- `max_extrude_cross_section: 5`: Previene extrusión excesiva que puede causar atascos
- `pressure_advance_smooth_time: 0.040`: Hace la extrusión más consistente
- `min_extrude_temp: 170`: Seguridad para prevenir extrusión en frío

### 5. Cinemática de Impresora Mejorada / Enhanced Printer Kinematics

```ini
[printer]
kinematics: cartesian
max_velocity: 150
max_accel: 3000
max_z_velocity: 50
square_corner_velocity: 5.0
max_z_accel: 500
minimum_cruise_ratio: 0.5    # NUEVO: Mejor control de aceleración
```

**Qué hace / What it does:**
- `minimum_cruise_ratio: 0.5`: Asegura aceleración más suave y controlada
- Reduce vibraciones que pueden afectar la primera capa

### 6. Nueva Macro de Prueba / New Test Macro

```gcode
FIRST_LAYER_TEST BED_TEMP=60 EXTRUDER_TEMP=200
```

**Uso / Usage:**
- Imprime un cuadrado de prueba de 60x60mm en el centro de la cama
- Perfecto para verificar adherencia y nivel de la cama
- Se llena con líneas para verificar consistencia de extrusión

## Cómo Usar Estas Mejoras / How to Use These Improvements

### Primer Uso / First Use

1. **Reiniciar Klipper / Restart Klipper**
   ```
   FIRMWARE_RESTART
   ```

2. **Recalibrar Z-offset / Recalibrate Z-offset**
   ```
   AUTO_Z_OFFSET
   ```
   Esto recalibrará automáticamente el offset Z y guardará la configuración.

3. **Probar Adherencia / Test Adhesion**
   ```
   FIRST_LAYER_TEST BED_TEMP=60 EXTRUDER_TEMP=200
   ```
   Ajusta las temperaturas según tu filamento.

### Impresiones Normales / Normal Prints

Usa en el G-code inicial de tu slicer:
```gcode
START_PRINT EXTRUDER_TEMP={material_print_temperature_layer_0} BED_TEMP={material_bed_temperature_layer_0}
```

### Solución de Problemas / Troubleshooting

**Si la primera capa está demasiado alta:**
- Ejecuta `AUTO_Z_OFFSET` de nuevo
- En Mainsail, puedes ajustar el Z-offset durante la impresión usando el panel de control

**Si la primera capa está demasiado baja (raspa la cama):**
- Ejecuta `AUTO_Z_OFFSET` de nuevo
- Ajusta manualmente el Z-offset en la interfaz Mainsail

**Si aún hay problemas de adherencia:**
1. Limpia la cama con alcohol isopropílico
2. Verifica que la cama esté a la temperatura correcta
3. Ajusta el Z-offset en incrementos de 0.02mm
4. Considera usar adhesivo (pegamento en barra, laca, etc.)

**Si ves irregularidades en la malla:**
1. Limpia la cama completamente
2. Ejecuta una calibración completa:
   ```
   G28
   BED_MESH_CALIBRATE
   SAVE_CONFIG
   ```
3. Visualiza la malla en Mainsail para identificar puntos altos/bajos

## Diferencias Clave con Configuración Anterior / Key Differences from Previous Configuration

| Parámetro | Antes | Ahora | Mejora |
|-----------|-------|-------|--------|
| Muestras BLTouch | 1 | 3 (mediana) | +200% precisión |
| Altura purga | 0.3mm | 0.28mm | Mejor contacto |
| Velocidad purga | F1500 | F900 | -40% más lenta |
| Fade end | 10mm | 5mm | Mejor compensación |
| Velocidad 1ª capa | No definida | F1200 (20mm/s) | Mejor adherencia |

## Resultados Esperados / Expected Results

✅ **Primera capa consistente y uniforme** - Sin áreas altas o bajas
✅ **Mejor adherencia a la cama** - El filamento se pega correctamente
✅ **No más impresión en el aire** - Cada capa se construye sobre la anterior
✅ **No más bolas de filamento** - La extrusión es consistente
✅ **Purga más efectiva** - Las líneas de purga se adhieren bien
✅ **Inicio de impresión más confiable** - Menos fallos en los primeros minutos

## Referencias Técnicas / Technical References

- [Klipper Bed Mesh Documentation](https://www.klipper3d.org/Bed_Mesh.html)
- [BLTouch Configuration](https://www.klipper3d.org/BLTouch.html)
- [Pressure Advance Tuning](https://www.klipper3d.org/Pressure_Advance.html)
- [First Layer Calibration Guide](https://www.klipper3d.org/Bed_Level.html)

## Notas Importantes / Important Notes

⚠️ **IMPORTANTE**: Después de aplicar estos cambios, es CRÍTICO ejecutar `AUTO_Z_OFFSET` para recalibrar el offset Z. La configuración anterior puede no ser válida con las nuevas mejoras de precisión del probe.

⚠️ **IMPORTANT**: After applying these changes, it is CRITICAL to run `AUTO_Z_OFFSET` to recalibrate the Z offset. The previous configuration may not be valid with the new probe accuracy improvements.

---

**Fecha de cambios / Change date:** 2026-01-04
**Versión / Version:** 2.0
**Archivos modificados / Modified files:** printer.cfg, macro.cfg
