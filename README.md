# Klipper-Makros & Ressourcen

> Ein umfassendes Nachschlagewerk für Klipper-Nutzer – Makros, Tutorials und externe Ressourcen an einem Ort.

## 📖 Inhaltsverzeichnis
1. [Einleitung](#einleitung)
2. [Ressourcen-Übersicht](#ressourcen-übersicht)
3. [Makrosammlung](#makrosammlung)
4. [Tutorials](#tutorials)
5. [Beispiele](#beispiele)
6. [Lizenz & Mitwirken](#lizenz--mitwirken)

---

## Einleitung
Dieses Repository bündelt alle wichtigen **Dokumentationen**, **Community-Beiträge** und **G-Code-Makros**, die du für dein Klipper-Projekt brauchst. Egal ob Einsteiger oder Profi, hier findest du:
- Verlinkungen zu offiziellen Dokus und Foren
- Kuratierte Makros mit Beschreibung und Quelle
- Schritt-für-Schritt-Tutorials für Slicer-Variablen
- Beispiel-G-Codes für deine Workflows

---

## Ressourcen-Übersicht
Im Ordner `docs/Resources.md` findest du eine thematisch sortierte Liste:
- 🌐 Offizielle Dokumentation & Interfaces (Klipper, Mainsail, Fluidd, Moonraker, KlipperScreen, CAN-Bus)
- 🧰 VzBoT CoreXY-Drucker
- 🛠️ Voron Community-Plattform
- 🔧 CPAP Fan & Flaps Projekte
- 📁 GitHub-Stars & Makrosammlungen
- 📑 Weitere Referenzlinks (Hardware, Tools, Theorie, Fehlerbehebung)

---

## Makrosammlung
Alle Makros liegen in `macros/printer_macros.cfg`. Highlights:
- **WIPE_LINE**, **RETRACT**, **UNRETRACT**, **G27** für die Druckvorbereitung
- **FLAP_CONTROL**, **FLAP_OPEN**, **FLAP_CLOSE** für CPAP-Lüfterklappen
- Parameter-gesteuerte Abläufe über Slicer-Variablen

---

## Tutorials
Detaillierte Anleitungen:
- `docs/Tutorial_Slicer_Variables.md` zeigt dir, wie du **Slicer-Platzhalter** (Cura, PrusaSlicer) an Klipper-Makros übergibst.

---

## Beispiele
Im Ordner `examples/` findest du:
- `start_print_example.gcode`: Beispiel-Startcode, der das START_PRINT-Makro aufruft.

---

## Lizenz & Mitwirken
- **Lizenz:** Dieses Projekt steht unter **GPLv3** (siehe `LICENSE`).
- Beiträge willkommen: Melde Bugs oder neue Makros via Issues! Schau in `CONTRIBUTING.md` für Richtlinien.

```
[include macros/printer_macros.cfg]
```

---

*Viel Spaß und perfekte Druckergebnisse!*