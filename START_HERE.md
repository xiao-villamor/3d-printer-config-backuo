# 🚀 INSTRUCCIONES RÁPIDAS / QUICK INSTRUCTIONS

## 🇪🇸 Español - ¡LEE ESTO PRIMERO!

### ✨ Última Actualización: Optimizaciones de Primera Capa (2026-01-05)

**Nueva guía completa disponible:** [GUIA_PROBLEMAS_PRIMERA_CAPA.md](GUIA_PROBLEMAS_PRIMERA_CAPA.md)

### 🎯 Cambios Aplicados

✅ **BLTouch optimizado** - Velocidad reducida a 5mm/s para máxima precisión
✅ **Purge line mejorado** - Velocidades balanceadas (F1500) para mejor adherencia
✅ **Soporte para curvas (G-code arcs)** - Mejora calidad en esquinas y curvas
✅ **Guía completa de troubleshooting** - Diagnóstico paso a paso en español

### ⚠️ IMPORTANTE: Pasos Después de la Actualización

#### Paso 1: Reinicia Klipper
```
FIRMWARE_RESTART
```

#### Paso 2: Nivela la cama manualmente
```
BED_LEVEL_SCREWS_TUNE
```
Ajusta cada tornillo según las instrucciones en pantalla.

#### Paso 3: Recalibra Z-Offset
```
AUTO_Z_OFFSET
```

#### Paso 4: Prueba la primera capa
```
FIRST_LAYER_TEST BED_TEMP=60 EXTRUDER_TEMP=200
```

### 🔧 ¿Tienes problemas específicos?

Consulta la nueva **[Guía de Problemas de Primera Capa](GUIA_PROBLEMAS_PRIMERA_CAPA.md)** que incluye:

📌 **Diagnóstico visual** - Identifica tu problema exacto
📌 **Soluciones paso a paso** - Para cada tipo de problema
📌 **Valores óptimos** - Temperaturas y configuraciones por material
📌 **Mantenimiento preventivo** - Evita problemas futuros
📌 **Configuración de slicer** - Settings óptimos para Cura/PrusaSlicer

### 📊 Parámetros Clave Actualizados

| Parámetro | Valor Anterior | Valor Nuevo | Beneficio |
|-----------|---------------|-------------|-----------|
| BLTouch speed | 15mm/s | 5mm/s | +200% precisión |
| BLTouch lift_speed | - | 10mm/s | Probing más rápido |
| Samples | 5 | 3 | Balance precisión/velocidad |
| Purge line Z | 0.25mm | 0.3mm | Mejor flujo |
| Purge speed | F600 | F1500 | Velocidad óptima |
| G-code arcs | No | Sí | Curvas más suaves |

### 📖 Documentación Disponible

- **[GUIA_PROBLEMAS_PRIMERA_CAPA.md](GUIA_PROBLEMAS_PRIMERA_CAPA.md)** ⭐ NUEVO - Guía completa troubleshooting
- [ADVANCED_FIRST_LAYER_FIXES.md](ADVANCED_FIRST_LAYER_FIXES.md) - Correcciones avanzadas anteriores
- [FIRST_LAYER_FIXES.md](FIRST_LAYER_FIXES.md) - Historial de correcciones
- [DYNAMIC_MESHING_FIX.md](DYNAMIC_MESHING_FIX.md) - Detalles adaptive meshing
- [QUICK_START.md](QUICK_START.md) - Guía de inicio rápido

---

## 🇬🇧 English - READ THIS FIRST!

### ✨ Latest Update: First Layer Optimizations (2026-01-05)

**New comprehensive guide available:** [GUIA_PROBLEMAS_PRIMERA_CAPA.md](GUIA_PROBLEMAS_PRIMERA_CAPA.md) (Spanish)

### 🎯 Changes Applied

✅ **Optimized BLTouch** - Speed reduced to 5mm/s for maximum accuracy
✅ **Improved purge line** - Balanced speeds (F1500) for better adhesion
✅ **G-code arcs support** - Better quality on corners and curves
✅ **Complete troubleshooting guide** - Step-by-step diagnostics (Spanish)

### ⚠️ IMPORTANT: Steps After Update

#### Step 1: Restart Klipper
```
FIRMWARE_RESTART
```

#### Step 2: Manually level the bed
```
BED_LEVEL_SCREWS_TUNE
```
Adjust each screw according to on-screen instructions.

#### Step 3: Recalibrate Z-Offset
```
AUTO_Z_OFFSET
```

#### Step 4: Test first layer
```
FIRST_LAYER_TEST BED_TEMP=60 EXTRUDER_TEMP=200
```

### 🔧 Having specific issues?

Check the new **[First Layer Problems Guide](GUIA_PROBLEMAS_PRIMERA_CAPA.md)** (Spanish) which includes:

📌 **Visual diagnostics** - Identify your exact problem
📌 **Step-by-step solutions** - For each type of issue
📌 **Optimal values** - Temperatures and settings by material
📌 **Preventive maintenance** - Avoid future issues
📌 **Slicer configuration** - Optimal settings for Cura/PrusaSlicer

### 📊 Key Updated Parameters

| Parameter | Previous | New | Benefit |
|-----------|----------|-----|---------|
| BLTouch speed | 15mm/s | 5mm/s | +200% accuracy |
| BLTouch lift_speed | - | 10mm/s | Faster probing |
| Samples | 5 | 3 | Balance accuracy/speed |
| Purge line Z | 0.25mm | 0.3mm | Better flow |
| Purge speed | F600 | F1500 | Optimal speed |
| G-code arcs | No | Yes | Smoother curves |

### 📖 Available Documentation

- **[GUIA_PROBLEMAS_PRIMERA_CAPA.md](GUIA_PROBLEMAS_PRIMERA_CAPA.md)** ⭐ NEW - Complete troubleshooting guide (Spanish)
- [ADVANCED_FIRST_LAYER_FIXES.md](ADVANCED_FIRST_LAYER_FIXES.md) - Previous advanced fixes
- [FIRST_LAYER_FIXES.md](FIRST_LAYER_FIXES.md) - Fix history
- [DYNAMIC_MESHING_FIX.md](DYNAMIC_MESHING_FIX.md) - Adaptive meshing details
- [QUICK_START.md](QUICK_START.md) - Quick start guide

---

## 🎯 Expected Results / Resultados Esperados

After following the steps above / Después de seguir los pasos:

✅ Perfect first layer adhesion / Adherencia perfecta de primera capa
✅ No warping in any zone / Sin warping en ninguna zona
✅ Supports print cleanly / Soportes imprimen limpiamente
✅ No nozzle clogs / Sin atascos de boquilla
✅ Consistent quality everywhere / Calidad consistente en toda la cama

---

## ❓ ¿Necesitas Ayuda? / Need Help?

**Español:**
1. Lee la **[GUIA_PROBLEMAS_PRIMERA_CAPA.md](GUIA_PROBLEMAS_PRIMERA_CAPA.md)** - Diagnóstico detallado
2. Verifica que seguiste TODOS los pasos en orden
3. Limpia la cama con alcohol isopropílico 99%
4. Asegúrate que la temperatura de cama sea correcta para tu filamento

**English:**
1. Read the **[GUIA_PROBLEMAS_PRIMERA_CAPA.md](GUIA_PROBLEMAS_PRIMERA_CAPA.md)** - Detailed diagnostics (Spanish)
2. Verify you followed ALL steps in order
3. Clean bed with 99% isopropyl alcohol
4. Make sure bed temperature is correct for your filament
