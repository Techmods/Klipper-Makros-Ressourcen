# Klipper-Makros & Ressourcen

Hier findest du meinen **Klipper-Kauderwelsch**, der sich im Laufe der Zeit angesammelt hat – und der hier ganz sicher munter weiterwachsen wird.

Tipps: 
(BLTouch, Clicky)
Sie funktionieren zwar, aber am Ende bin ich bei **<a href="https://cartographer3d.com" target="_blank" rel="noopener noreferrer">Cartographer</a>** gelandet.
Ich feier das teil! Es passt immer – egal, ob du die Nozzle wechselst oder die Plate tauscht.
Außerdem ist die Dokumentation von Cartographer mindestens genauso ausführlich und gut wie die offizielle Klipper-Doku.

Falls du einmal bei irgendetwas nicht weiterkommst, oder etwas nicht findest, 
lohnt sich immer ein Blick auf die Projekt-Discord-Server. 
Dort gibt es jede Menge Experten, die dir sicher weiterhelfen. 😉

## 📖 Inhaltsverzeichnis

👉 **Schnellnavigation:**
- 📚 [Zur Ressourcenübersicht](#ressourcen-übersicht)
- 🧩 [Zur Makrosammlung](#makrosammlung)
- 🧠 [Direkt zu den Tutorials](#tutorials)
- 🧪 [Zu den Beispielen](#beispiele)
- 📜 [Zur Lizenz & Mitwirken](#lizenz--mitwirken)
1. [Einleitung](#einleitung)
2. [Ressourcen-Übersicht](#ressourcen-übersicht)
3. [Makrosammlung](#makrosammlung)
4. [Tutorials](#tutorials)
5. [Beispiele](#beispiele)
6. [Lizenz & Mitwirken](#lizenz--mitwirken)

---

## Einleitung
Dieses Repository bündelt alles, was du für dein Klipper-Projekt brauchst:
- Kuratierte **Makros** mit Beschreibung, Code und Quelle
- Thematisch sortierte **Ressourcen-Links** (Dokumentationen, Community, Hardware, Tools)
- **Schritt-für-Schritt-Tutorials** für Slicer-Variablen und Makro-Integration
- **Beispiel-G-Codes** für den direkten Einsatz

---

## Ressourcen-Übersicht

**Interne Verweise:**
- 📑 [Makrosammlung (printer_macros.cfg)](macros/printer_macros.cfg)
- 📖 [Slicer-Variablen Tutorial](docs/Tutorial_Slicer_Variables.md)
- 🌍 [Diese Seite (Resources.md)](docs/Resources.md)

Die vollständige Liste aller externen Links findest du in `docs/Resources.md`:
- 🌐 **Offizielle Dokumentation** & Interfaces
- 🧰 **VzBoT** CoreXY-Drucker-Quellen
- 🛠️ **Voron** Community-Links
- 🔧 **CPAP Fan & Flaps** Projekte
- 📁 **GitHub Stars** & Makrosammlungen
- 📑 **Weitere Referenzen** (Hardware, Theorie, Troubleshooting)

---

## Makrosammlung
Alle Makros liegen in **`macros/printer_macros.cfg`**. Darin enthalten sind:
- **WIPE_LINE**, **RETRACT**, **UNRETRACT**, **G27** – dein Druckbett- und Kopf-Setup
- **FLAP_CONTROL**, **FLAP_OPEN**, **FLAP_CLOSE** – CPAP-Lüfterklappen-Management
- Parameter-gesteuerte Abläufe dank Slicer-Variablen (Material, Temperaturen, PA)

---

## Tutorials
Die detaillierten Anleitungen findest du in **`docs/Tutorial_Slicer_Variables.md`**:
- Wie du **Slicer-Platzhalter** (Cura, PrusaSlicer) an Klipper-Makros übergibst
- Best Practices für automatisches Vorheizen, Heat Creep-Vermeidung und mehr

---

## Beispiele
Im Ordner **`examples/`** gibt es:
- `start_print_example.gcode`: Beispiel-Startcode, der das `START_PRINT`-Makro aufruft

---

## Lizenz & Mitwirken
- **Lizenz:** Siehe `LICENSE` (GPLv3)
- **Contributing:** Schau in `CONTRIBUTING.md`, um Bugs zu melden oder neue Makros beizusteuern

---



*Viel Spaß und immer eine saubere Düse!*
