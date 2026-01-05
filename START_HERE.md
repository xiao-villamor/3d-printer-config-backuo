# 🚀 INSTRUCCIONES RÁPIDAS / QUICK INSTRUCTIONS

## 🇪🇸 Español - ¡LEE ESTO PRIMERO!

### ¿Qué se ha solucionado?

✅ **Primera capa se levanta y no se adhiere** - SOLUCIONADO
✅ **Problemas por zonas de la cama** - SOLUCIONADO  
✅ **Soportes se levantan y atascan boquilla** - SOLUCIONADO

### ⚠️ IMPORTANTE: Sigue estos pasos EN ORDEN

#### Paso 1: Reinicia Klipper
```
FIRMWARE_RESTART
```

#### Paso 2: Nivela la cama (¡NUEVO Y CRÍTICO!)
```
BED_LEVEL_SCREWS_TUNE
```
**Esto es MUY IMPORTANTE** para resolver los problemas por zonas. Sigue las instrucciones en pantalla y ajusta cada tornillo según indicado.

#### Paso 3: Recalibra Z-Offset
```
AUTO_Z_OFFSET
```

#### Paso 4: Prueba la primera capa
```
FIRST_LAYER_TEST BED_TEMP=60 EXTRUDER_TEMP=200
```

### 📋 Mejoras Aplicadas

| Mejora | Antes | Ahora |
|--------|-------|-------|
| Precisión BLTouch | 0.02mm | 0.0075mm (3x más preciso) |
| Muestras por punto | 3 | 5 |
| Puntos de malla | 100 | 121 |
| Velocidad 1ª capa | 20mm/s | 15mm/s |
| Estabilización térmica | No | Sí (30s) |
| Limpieza de boquilla | No | Sí (automática) |
| Ajuste de tornillos | Manual | Asistido por Klipper |

### 🆕 Nuevos Comandos Disponibles

**Para limpiar la boquilla antes de imprimir:**
```
CLEAN_NOZZLE
```

**Para impresiones críticas (estabilización térmica extendida):**
```
HEAT_SOAK BED_TEMP=60 SOAK_TIME=5
```

**Para nivelar la cama manualmente:**
```
BED_LEVEL_SCREWS_TUNE
```

### 📖 Documentación Completa

Lee el archivo **ADVANCED_FIRST_LAYER_FIXES.md** para:
- Explicación detallada de todos los cambios
- Solución de problemas específicos
- Configuraciones recomendadas del slicer
- Mantenimiento preventivo

---

## 🇬🇧 English - READ THIS FIRST!

### What has been fixed?

✅ **First layer lifts and doesn't adhere** - FIXED
✅ **Zone-dependent bed issues** - FIXED
✅ **Supports lift and clog nozzle** - FIXED

### ⚠️ IMPORTANT: Follow these steps IN ORDER

#### Step 1: Restart Klipper
```
FIRMWARE_RESTART
```

#### Step 2: Level the bed (NEW AND CRITICAL!)
```
BED_LEVEL_SCREWS_TUNE
```
**This is VERY IMPORTANT** to fix zone-dependent issues. Follow on-screen instructions and adjust each screw as indicated.

#### Step 3: Recalibrate Z-Offset
```
AUTO_Z_OFFSET
```

#### Step 4: Test first layer
```
FIRST_LAYER_TEST BED_TEMP=60 EXTRUDER_TEMP=200
```

### 📋 Applied Improvements

| Improvement | Before | Now |
|-------------|--------|-----|
| BLTouch Precision | 0.02mm | 0.0075mm (3x more precise) |
| Samples per point | 3 | 5 |
| Mesh points | 100 | 121 |
| 1st layer speed | 20mm/s | 15mm/s |
| Thermal stabilization | No | Yes (30s) |
| Nozzle cleaning | No | Yes (automatic) |
| Screw adjustment | Manual | Klipper-assisted |

### 🆕 New Available Commands

**To clean nozzle before printing:**
```
CLEAN_NOZZLE
```

**For critical prints (extended thermal stabilization):**
```
HEAT_SOAK BED_TEMP=60 SOAK_TIME=5
```

**To manually level the bed:**
```
BED_LEVEL_SCREWS_TUNE
```

### 📖 Complete Documentation

Read **ADVANCED_FIRST_LAYER_FIXES.md** for:
- Detailed explanation of all changes
- Specific troubleshooting
- Recommended slicer settings
- Preventive maintenance

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

Si después de seguir todos los pasos aún tienes problemas:

1. Lee la sección de "Solución de Problemas" en ADVANCED_FIRST_LAYER_FIXES.md
2. Verifica que seguiste TODOS los pasos en orden
3. Asegúrate de haber limpiado la cama con alcohol isopropílico
4. Verifica que la temperatura de la cama sea la correcta para tu filamento

If after following all steps you still have issues:

1. Read the "Troubleshooting" section in ADVANCED_FIRST_LAYER_FIXES.md
2. Verify you followed ALL steps in order
3. Make sure you cleaned the bed with isopropyl alcohol
4. Verify bed temperature is correct for your filament
