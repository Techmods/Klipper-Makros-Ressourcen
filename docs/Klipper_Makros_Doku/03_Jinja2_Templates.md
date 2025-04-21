## Jinja2-Template-Sprache in Makros

Ein besonders mächtiges Feature von Klipper-Makros ist die Unterstützung der **Template-Sprache Jinja2**. Damit lassen sich Variablen, Bedingungen und Schleifen innerhalb der Makro-GCodes verwenden, um dynamische Abläufe zu gestalten ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=The%20gcode_macro%20,further%20information%20on%20the%20syntax)). Der `gcode:`-Abschnitt eines Makros wird beim **Aufruf** durch den Jinja2-Interpreter ausgewertet. Erst nach dieser Auswertung werden die resultierenden G-Code-Befehle an den Drucker geschickt ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=Available%20fields%20are%20defined%20in,the%20Status%20Reference%20document)).

**Syntax:** In Klipper werden Ausdrücke in **geschweiften Klammern** `{ ... }` eingebettet, um ihren Wert im Makro einzusetzen. Code-Blöcke für Logik wie Bedingungen oder Schleifen werden mit `{% ... %}` eingeleitet ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=The%20gcode_macro%20,further%20information%20on%20the%20syntax)). (Hinweis: Anders als in der offiziellen Jinja2-Dokumentation mit `{{ ... }}` nutzt Klipper hier ein angepasstes Delimiter-Schema mit einfachen `{ }` Klammern.) Kommentare können mit `#` in Makros eingefügt werden – alles nach `#` auf einer Zeile wird von Klipper ignoriert (kann hilfreich sein, um Makros zu dokumentieren oder abzustimmen).

**Beispiel – Bedingung und Schleife:** Untenstehendes Makro demonstriert die Verwendung von Jinja2 für eine Schleife und eine Bedingung. Es wischt z.B. die Nozzle mehrmals hin und her (zwei X-Positionen), abhängig von einer Zählvariablen:

```ini
[gcode_macro NOZZLE_WIPE]
gcode:
  {% set wipes = 3 %}
  G90                        ; absoluter Modus
  {% for i in range(wipes) %}
    G0 X{ 50 + i*10 } Y20 F6000   ; fahre versetzt in X-Richtung
    {% if i < wipes - 1 %}
      ; Falls nicht letzter Durchgang, zurück zur Startposition:
      G0 X50 Y20 F6000
    {% endif %}
  {% endfor %}
  M117 Nozzle Wipe abgeschlossen
```

Hier wird per `{% set %}` eine lokale Variable `wipes` gesetzt. Dann iteriert `{% for i in range(wipes) %}` eine bestimmte Anzahl Durchläufe. Innerhalb der Schleife wird mit `{% if ... %}` geprüft, ob **nicht** der letzte Durchlauf erreicht ist (`i < wipes - 1`); wenn ja, fährt die Düse wieder zur Ausgangsposition zurück. Die Ausdrücke in `{ ... }` – z.B. `{50 + i*10}` – werden zur Makro-Ausführungszeit berechnet und als konkrete Werte in den G-Code eingesetzt.

**Achtung – Auswertungszeitpunkt:** Alle Jinja2-Anweisungen im Makro (also die `{% ... %}` und `{ ... }` Blöcke) werden **vollständig ausgewertet, bevor** auch nur der erste echte G-Code an den Drucker geschickt wird ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=Available%20fields%20are%20defined%20in,the%20Status%20Reference%20document)). Das bedeutet, dass Abfragen des Druckerstatus in Bedingungen/Schleifen den Zustand **zum Zeitpunkt des Makroaufrufs** sehen, nicht den veränderten Zustand nach einigen Befehlen. Beispiel: Wenn ein Makro zuerst `M109 S200` (Heizen und Warten auf 200°C) aufruft und danach in einer Schleife per `printer.extruder.temperature` prüft, ob die Temperatur > 100°C ist, würde man erwarten, dass die Bedingung wahr ist. Tatsächlich wird aber die Bedingung **vor** Ausführung von `M109` ausgewertet, also noch im kalten Zustand, und somit als *false* gewertet – der Code innerhalb des If-Blocks würde unerwartet ausgeführt ([Voidtrance Klipper Macros Beginners Guide | Voron Documentation](https://docs.vorondesign.com/community/howto/voidtrance/Klipper_Macros_Beginners_Guide.html#:~:text=The%20following%20is%20an%20example,Consider%20the%20following%20example%20macro)) ([Voidtrance Klipper Macros Beginners Guide | Voron Documentation](https://docs.vorondesign.com/community/howto/voidtrance/Klipper_Macros_Beginners_Guide.html#:~:text=However%2C%20as%20described%20above%20Klipper,GCode%20steam%20generated%20would%20be)). Dieses Verhalten erfordert beim Schreiben komplexer Makros etwas Umdenken. Faustregel: Jinja2-Logik kann nicht auf Änderungen reagieren, die durch vorherige Befehle im selben Makro erst hervorgerufen würden. Gegebenenfalls muss man die Reihenfolge umstellen oder mehrere getrennte Makros verwenden.

**Weitere Kontrollstrukturen:** Neben `if`/`else` und `for` unterstützt Jinja2 in Makros auch `elif` sowie verschachtelte Strukturen. Es gibt zudem spezielle Template-Konstrukte wie Schleifen-*Helper* (z.B. `loop.index`) oder Filters (siehe unten). Allerdings stehen **nicht alle** Python- oder Jinja-Funktionen zur Verfügung – Klipper integriert einen “abgespeckten” Jinja2-Interpreter ([Advanced macro templating support - Developers - Klipper](https://klipper.discourse.group/t/advanced-macro-templating-support/1116#:~:text=I%E2%80%99ve%20been%20looking%20at%20Klipper%E2%80%99s,commands)). Im Wesentlichen sind mathematische Operationen, Vergleiche, Strings und die in der Jinja2-Dokumentation beschriebenen Standardfilter nutzbar, **jedoch keine eigenen Python-Funktionen oder importierten Module** ohne Erweiterungen. Die vorhandenen Möglichkeiten reichen aber für die meisten Anwendungsfälle von Drucker-Makros aus.
