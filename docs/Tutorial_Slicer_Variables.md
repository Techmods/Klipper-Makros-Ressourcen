# Tutorial: Slicer-Variablen in Klipper-Makros

Lerne, wie du im Slicer (Cura, PrusaSlicer) Parameter übergibst, um automatisierte Startabläufe zu gestalten.

## 1. Slicer-Einstellungen
Cura-Beispiel:
```gcode
START_PRINT BED_TEMP={material_bed_temperature_layer_0} EXTRUDER_TEMP={material_print_temperature_layer_0}
```
PrusaSlicer:
```gcode
START_PRINT EXTRUDER_TEMP=[first_layer_temperature] BED_TEMP=[first_layer_bed_temperature]
```

## 2. Makro in `printer.cfg`
```ini
[gcode_macro START_PRINT]
gcode:
  {% set bed = params.BED_TEMP|default(60)|float %}
  {% set ext = params.EXTRUDER_TEMP|default(200)|float %}
  M140 S{bed}
  G28
  M190 S{bed}
  M109 S{ext}
  WIPE_LINE
```

## 3. Test & Anpassung
- Ändere im Slicer Temperaturen und beobachte das Heizverhalten  
- Füge zusätzliche Parameter hinzu (z.B. CHAMBER, PA)  
