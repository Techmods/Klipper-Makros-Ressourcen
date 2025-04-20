Klipper-Makros & Ressourcen

Hier findest du meinen Klipper-Kauderwelsch, der sich im Laufe der Zeit angesammelt hat – und der hier ganz sicher munter weiterwachsen wird.

Die Tools, die ich erwähnt habe (BLTouch, das Clicky-Gedöns), habe ich ausprobiert. Sie funktionieren zwar, aber am Ende bin ich immer wieder bei <a href="https://github.com/john30/klipper_cartographer" target="_blank" rel="noopener noreferrer">Cartographer</a> gelandet.

Warum? Ganz einfach: Es passt immer – egal, ob du die Nozzle wechselst oder die Plate tauscht.

Außerdem ist die Dokumentation von Cartographer mindestens genauso ausführlich wie die offizielle Klipper-Doku.

Falls du einmal nicht weiterkommst oder etwas nicht findest, lohnt sich ein Blick auf die Klipper-Discord-Server. Dort gibt es jede Menge Experten, die dir sicher weiterhelfen. 😉

📖 Inhaltsverzeichnis

1. Einleitung


2. Ressourcen-Übersicht


3. Makrosammlung


4. Tutorials


5. Beispiele


6. Lizenz & Mitwirken




---

Einleitung

Dieses Repository bündelt alles, was du für dein Klipper-Projekt brauchst:

Kuratierte Makros mit Beschreibung, Code und Quelle

Thematisch sortierte Ressourcen-Links (Dokumentationen, Community, Hardware, Tools)

Schritt-für-Schritt-Tutorials für Slicer-Variablen und Makro-Integration

Beispiel-G-Codes für den direkten Einsatz



---

Ressourcen-Übersicht

Die vollständige Liste aller externen Links findest du in docs/Resources.md:

🌐 Offizielle Dokumentation & Interfaces

🧰 VzBoT CoreXY-Drucker-Quellen

🛠️ Voron Community-Links

🔧 CPAP Fan & Flaps Projekte

📁 GitHub Stars & Makrosammlungen

📑 Weitere Referenzen (Hardware, Theorie, Troubleshooting)



---

Makrosammlung

Alle Makros liegen in macros/printer_macros.cfg. Darin enthalten sind:

WIPE_LINE, RETRACT, UNRETRACT, G27 – dein Druckbett- und Kopf-Setup

FLAP_CONTROL, FLAP_OPEN, FLAP_CLOSE – CPAP-Lüfterklappen-Management

Parameter-gesteuerte Abläufe dank Slicer-Variablen (Material, Temperaturen, PA)



---

Tutorials

Die detaillierten Anleitungen findest du in docs/Tutorial_Slicer_Variables.md:

Wie du Slicer-Platzhalter (Cura, PrusaSlicer) an Klipper-Makros übergibst

Best Practices für automatisches Vorheizen, Heat Creep-Vermeidung und mehr



---

Beispiele

Im Ordner examples/ gibt es:

start_print_example.gcode: Beispiel-Startcode, der das START_PRINT-Makro aufruft



---

Lizenz & Mitwirken

Lizenz: Siehe LICENSE (GPLv3)

Contributing: Schau in CONTRIBUTING.md, um Bugs zu melden oder neue Makros beizusteuern


[include macros/printer_macros.cfg]


---

Viel Spaß und immer eine saubere Düse!

