# Guía Completa de Solución de Problemas de Primera Capa - Ender 3 V3 SE

## 🎯 Objetivo
Esta guía te ayudará a diagnosticar y resolver problemas específicos de primera capa en tu Ender 3 V3 SE con Klipper.

---

## 📋 Diagnóstico Rápido

### ¿Qué problema tienes?

#### 1. La primera capa no se pega a la cama
**Síntomas:**
- El filamento no se adhiere
- Se forman bolas de plástico
- La impresión se mueve al imprimir

**Causas comunes:**
- ✗ Z-offset muy alto (boquilla muy lejos de la cama)
- ✗ Cama sucia con grasa o polvo
- ✗ Temperatura de cama muy baja
- ✗ Cama no nivelada correctamente

**Soluciones paso a paso:**

1. **Limpiar la cama a fondo**
   ```
   - Usa alcohol isopropílico 99%
   - Limpia con movimientos circulares
   - Deja secar completamente antes de imprimir
   ```

2. **Recalibrar Z-offset**
   ```
   AUTO_Z_OFFSET
   ```
   Si el filamento aún no se pega, ajusta manualmente:
   - En Mainsail, durante la impresión usa el control "Z Offset"
   - Baja el offset en pasos de -0.02mm
   - Deberías ver que el filamento se aplasta ligeramente contra la cama

3. **Nivelar la cama manualmente**
   ```
   BED_LEVEL_SCREWS_TUNE
   ```
   Sigue las instrucciones y ajusta los tornillos exactamente como indica.

4. **Aumentar temperatura de cama**
   - PLA: Prueba 65°C (en lugar de 60°C)
   - PETG: Prueba 85°C (en lugar de 80°C)
   - ABS: Prueba 105-110°C

#### 2. La primera capa está demasiado aplastada / Raspa la cama
**Síntomas:**
- La boquilla raspa contra la cama
- El filamento está muy delgado y transparente
- Sonidos de arrastre al imprimir

**Causas comunes:**
- ✗ Z-offset muy bajo (boquilla muy cerca de la cama)
- ✗ Cama desnivelada con puntos altos

**Soluciones:**

1. **Ajustar Z-offset hacia arriba**
   ```
   AUTO_Z_OFFSET
   ```
   O durante la impresión en Mainsail:
   - Aumenta el offset en pasos de +0.02mm
   - El filamento debe verse redondo, no aplastado

2. **Verificar nivelación de cama**
   ```
   BED_LEVEL_SCREWS_TUNE
   ```
   Ajusta los tornillos según las indicaciones.

#### 3. Primera capa irregular (unas partes bien, otras mal)
**Síntomas:**
- Algunas zonas se adhieren bien, otras no
- Diferente altura en diferentes partes de la cama
- La calidad varía según la posición en la cama

**Causas comunes:**
- ✗ Cama deformada (warped)
- ✗ Malla de cama desactualizada o incorrecta
- ✗ Tornillos de nivelación flojos

**Soluciones:**

1. **Crear nueva malla de cama completa**
   ```
   G28
   BED_MESH_CLEAR
   BED_MESH_CALIBRATE
   SAVE_CONFIG
   ```

2. **Verificar que la malla se carga en cada impresión**
   - Asegúrate de usar `START_PRINT` en tu slicer
   - La malla se crea automáticamente con `ADAPTIVE=1`

3. **Apretar tornillos de la cama**
   - Verifica que los 4 tornillos de nivelación estén firmes
   - No demasiado apretados, pero sin juego

4. **Considerar superficie de vidrio**
   - Si la cama está muy deformada, una superficie de vidrio puede ayudar

#### 4. Líneas de purga no se pegan
**Síntomas:**
- Las líneas de purga inicial se despegan
- Forman bolas en lugar de líneas rectas

**Causas comunes:**
- ✗ Z-offset incorrecto
- ✗ Esquina de la cama no nivelada
- ✗ Temperatura muy baja

**Soluciones:**

1. **Ejecutar heat soak antes de imprimir**
   ```
   HEAT_SOAK BED_TEMP=60 SOAK_TIME=3
   ```
   Esto estabiliza la temperatura de la cama.

2. **Ajustar Z-offset**
   ```
   AUTO_Z_OFFSET
   ```

3. **Nivelar la cama especialmente en la esquina frontal izquierda**
   ```
   BED_LEVEL_SCREWS_TUNE
   ```

#### 5. Esquinas de la primera capa se levantan (warping)
**Síntomas:**
- Las esquinas de la pieza se despegan
- La pieza se curva hacia arriba en los bordes

**Causas comunes:**
- ✗ Enfriamiento excesivo en primera capa
- ✗ Temperatura de cama muy baja
- ✗ Falta de adhesión

**Soluciones:**

1. **Configurar slicer correctamente**
   - Enfriamiento primera capa: 0%
   - Temperatura primera capa: +5°C sobre normal
   - Usar brim (falda) de 5-8mm

2. **Aumentar temperatura de cama**
   - PLA: 65°C
   - PETG: 85°C
   - ABS: 110°C + enclosure

3. **Usar adhesivos**
   - Pegamento en barra (PLA)
   - Laca para el pelo (ABS)
   - Cinta de pintor azul (PLA)

#### 6. Soportes se levantan y atascan la boquilla
**Síntomas:**
- Los soportes no se pegan bien
- Se forman bolas que atascan la boquilla
- Impresión falla después de algunas capas

**Causas comunes:**
- ✗ Boquilla sucia con filamento quemado
- ✗ Velocidad de soportes muy alta
- ✗ Configuración incorrecta en el slicer

**Soluciones:**

1. **Limpiar boquilla antes de imprimir**
   ```
   CLEAN_NOZZLE
   ```

2. **Reducir temperatura 5°C**
   - Ejemplo: PLA 200°C → 195°C

3. **Configurar soportes en el slicer:**
   ```
   Patrón de soporte: Grid o Gyroid
   Densidad: 15-20%
   Velocidad de soportes: 30-40mm/s (más lento)
   Interface de soporte: Sí (2-3 capas)
   Distancia Z interface: 0.2mm
   Enfriamiento: 100% desde capa 2
   ```

4. **Usar brim en piezas con soportes**
   - Ancho: 5-8mm
   - Ayuda a mantener soportes pegados

---

## 🔧 Procedimientos de Mantenimiento

### Calibración Completa (Hacer después de aplicar estas correcciones)

Ejecuta estos comandos EN ORDEN:

```gcode
# 1. Reiniciar Klipper
FIRMWARE_RESTART

# 2. Home
G28

# 3. Nivelar cama manualmente
BED_LEVEL_SCREWS_TUNE
# Ajusta tornillos según indicaciones

# 4. Recalibrar Z-offset
AUTO_Z_OFFSET

# 5. Crear malla de cama nueva
G28
BED_MESH_CLEAR
BED_MESH_CALIBRATE
SAVE_CONFIG

# 6. Prueba de primera capa
FIRST_LAYER_TEST BED_TEMP=60 EXTRUDER_TEMP=200
```

### Mantenimiento Preventivo

**Antes de cada impresión importante:**
```gcode
HEAT_SOAK BED_TEMP=60 SOAK_TIME=3
CLEAN_NOZZLE
```

**Semanalmente:**
1. Limpiar cama con alcohol isopropílico
2. Verificar nivel: `BED_LEVEL_SCREWS_TUNE`
3. Limpiar boquilla: `CLEAN_NOZZLE`

**Mensualmente:**
1. Recalibrar Z-offset: `AUTO_Z_OFFSET`
2. Nueva malla completa: `BED_MESH_CALIBRATE`
3. Verificar tensión de correas
4. Lubricar eje Z si es necesario

---

## 📊 Valores Óptimos de Configuración

### Temperaturas Recomendadas

| Material | Primera Capa Boquilla | Boquilla Normal | Primera Capa Cama | Cama Normal |
|----------|----------------------|-----------------|-------------------|-------------|
| PLA | 205-215°C | 200-210°C | 60-65°C | 60°C |
| PETG | 240-250°C | 235-245°C | 80-85°C | 80°C |
| ABS | 245-255°C | 240-250°C | 105-110°C | 100-105°C |
| TPU | 220-230°C | 215-225°C | 50-60°C | 50°C |

### Configuración de Slicer para Primera Capa

**Cura:**
```
Altura primera capa: 0.3mm (0.75 de la altura normal)
Ancho línea primera capa: 120% (0.48mm para boquilla 0.4mm)
Velocidad primera capa: 20mm/s
Aceleración primera capa: 500mm/s²
Enfriamiento primera capa: 0%
Retracción primera capa: 1mm @ 40mm/s
```

**PrusaSlicer / SuperSlicer:**
```
Altura primera capa: 0.3mm
Ancho primera capa: 120%
Velocidad primera capa: 20mm/s
Temperatura primera capa: +5°C
Ventilador primera capa: 0%
```

### Configuración de Soportes Óptima

```
Patrón: Grid (más fuerte) o Gyroid (más fácil de quitar)
Densidad: 15-20%
Velocidad: 30-40mm/s
Interface: Sí (activar)
  - Capas interface: 2-3
  - Patrón interface: Lines
  - Distancia Z: 0.2mm
Brim en soportes: Recomendado para piezas grandes
```

---

## 🎓 Entendiendo los Parámetros de Klipper

### BLTouch Configuration
```ini
speed: 5           # Velocidad de bajada para medir (muy lento = preciso)
lift_speed: 10     # Velocidad de subida entre mediciones (puede ser rápido)
samples: 3         # Número de mediciones por punto
samples_tolerance: 0.0075  # Diferencia máxima entre mediciones (mm)
```

### Bed Mesh
```ini
probe_count: 11,11  # 121 puntos de medición (más = mejor mapeo)
fade_start: 0.5     # Altura donde empieza a reducir compensación
fade_end: 3         # Altura donde termina compensación (mm)
```

Esto significa:
- De 0 a 0.5mm: Compensación 100%
- De 0.5 a 3mm: Compensación gradualmente reducida
- Más de 3mm: Sin compensación (pieza ya estable)

---

## 🆘 Problemas Avanzados

### "El BLTouch parpadea rojo"
```
# Resetear BLTouch
BLTOUCH_DEBUG COMMAND=reset
G28
```

### "Error: Probe triggered prior to movement"
```
# El probe está activado antes de medir
# Solución:
1. Verificar que el pin del BLTouch sube y baja
2. Limpiar el pin con alcohol
3. Verificar cableado
```

### "Klipper se congela durante BED_MESH_CALIBRATE"
```
# Reducir puntos de probe temporalmente
[bed_mesh]
probe_count: 5,5  # En lugar de 11,11

# Después de completar, volver a 11,11
```

### "La boquilla gotea durante el mesh"
```
# Ya está solucionado en START_PRINT:
# Precalentamos a TEMP-40°C durante mesh
# Esto previene goteo
```

---

## 📞 Recursos Adicionales

### Archivos de Referencia
- [FIRST_LAYER_FIXES.md](FIRST_LAYER_FIXES.md) - Historial de correcciones anteriores
- [ADVANCED_FIRST_LAYER_FIXES.md](ADVANCED_FIRST_LAYER_FIXES.md) - Correcciones avanzadas
- [DYNAMIC_MESHING_FIX.md](DYNAMIC_MESHING_FIX.md) - Detalles sobre adaptive meshing
- [START_HERE.md](START_HERE.md) - Primeros pasos después de actualizar

### Comandos Útiles de Consola

```gcode
# Ver estado actual
STATUS

# Ver temperatura
QUERY_ADC

# Ver malla de cama actual
BED_MESH_OUTPUT

# Información del probe
QUERY_PROBE

# PID tuning (si hay problemas de temperatura)
PID_EXTRUDER TARGET=200
PID_BED TARGET=60
```

---

## ✅ Checklist de Verificación Pre-Impresión

Antes de imprimir algo importante, verifica:

- [ ] Cama limpia con alcohol isopropílico
- [ ] Filamento seco (sin absorción de humedad)
- [ ] Z-offset calibrado recientemente
- [ ] Nivel de cama verificado
- [ ] Malla de cama creada
- [ ] Temperatura de ambiente estable (sin corrientes de aire)
- [ ] Boquilla limpia
- [ ] Correas con tensión adecuada
- [ ] Tornillos de la cama firmes

---

**Última actualización:** 2026-01-05
**Versión:** 1.0
**Configuración probada en:** Ender 3 V3 SE con Klipper + Mainsail
