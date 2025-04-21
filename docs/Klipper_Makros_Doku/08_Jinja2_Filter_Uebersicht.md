## Nützliche Jinja2-Funktionen/Filter im Klipper-Kontext

Zum Abschluss eine Übersicht häufiger Jinja2-Template-Funktionen und Filter, die beim Erstellen von Klipper-Makros besonders hilfreich sind:

| **Funktion/Filter**        | **Bedeutung (im Makro)**                                               | **Beispiel**                         |
|----------------------------|-----------------------------------------------------------------------|--------------------------------------|
| `{% set name = wert %}`    | Lokale Variable anlegen (Binnen eines Makros gültig)                   | `{% set x = printer.gcode_move.speed %}` – aktuelle Speed in `x` speichern |
| `{% if ... %} ... {% endif %}` | Bedingte Ausführung eines Codeblocks                                  | `{% if printer.extruder.temperature < 210 %} M104 S210 {% endif %}` – Hotend heizen, falls <210°C |
| `{% for x in seq %} ... {% endfor %}` | Schleife über eine Sequenz                                      | `{% for i in range(5) %} G4 P100 {% endfor %}` – führe `G4 P100` fünfmal aus |
| `range(n)`                | Erzeugt eine Sequenz 0…n-1 für for-Schleifen                            | `range(wipes)` im Beispiel oben erzeugt 0,1,...,wipes-1            |
| `params.NAME`             | Übergabeparameter des Makros (Immer als String, NAME immer Groß)        | `params.FEEDRATE` – Wert des FEEDRATE-Params abrufen             |
| `rawparams`               | Gesamte Parameterzeile inkl. Kommentare (ungeparst)                    | Verwendung z.B. zur Nachrichtenausgabe: `M117 {rawparams}`         |
| `printer.<objekt>.<feld>` | Zugriff auf Druckerstatus (Werte siehe *Status Reference*)             | `printer.fan.speed` – aktueller Partcooling-Fan-Speed (0–1)       |
| `|default(wert)`          | Filter: Standardwert einsetzen, falls Variable undefined/leer          | `params.X|default(100)` – nutzt 100, wenn kein X-Param angegeben  |
| `|int` / `|float`         | Filter: String in Zahl umwandeln (Integer/Float)                       | `params.LAYER|int` – wandelt Parameter LAYER in Integer           |
| `|round(n)`               | Filter: Rundet eine Zahl auf n Nachkommastellen                        | `{ printer.heater_bed.temperature|round(1) }` – z.B. "60.0"       |
| `|lower` / `|upper`       | Filter: Zeichenkette in Klein- bzw. Großschreibung                     | `{ params.MODE|default("AUTO")|upper }` – liefert z.B. "AUTO"     |
| `action_respond_info(msg)` etc. | Spezielle Aktionen während Makro-Auswertung (siehe oben)           | `{ action_raise_error("Abbruch!") }` – bricht Makro mit Fehler ab |
| `SET_GCODE_VARIABLE`      | G-Code zum Setzen persistenter Makro-Variable                          | `SET_GCODE_VARIABLE MACRO=mine VARIABLE=val VALUE={42}`          |

Diese Liste ist nicht abschließend (Jinja2 bietet noch mehr eingebaute Filter wie `sum`, `max`, `join`, Tests wie `is undefined` etc. ([Template Designer Documentation — Jinja Documentation (3.1.x)](https://jinja.palletsprojects.com/en/stable/templates/#:~:text=Filters%C2%B6)) ([Template Designer Documentation — Jinja Documentation (3.1.x)](https://jinja.palletsprojects.com/en/stable/templates/#:~:text=match%20at%20L147%20Beside%20filters%2C,which%20will%20then))), aber sie umfasst die häufigsten im Klipper-Kontext genutzten. Insbesondere die Umwandlungsfilter `|int`/`|float` und `|default` sind bei Makro-Parametern fast immer nötig, und `range()` sowie Schleifen/Bedingungen sind fundamental für dynamische Makros. Das `printer`-Objekt und `action_...`-Funktionen sind spezielle Erweiterungen in Klipper, um Makros mit dem Firmware-Status und externen Aktionen zu verknüpfen.
