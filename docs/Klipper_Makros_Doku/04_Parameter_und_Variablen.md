## Verwendung von Parametern und Variablen in Makros

Klipper erlaubt es, Makros **Parameter** zu übergeben, ähnlich wie Funktionsargumente. Diese Parameter werden beim Aufruf in der Form `MACRONAME PARAM=Wert ANOTHER=...` angegeben. Innerhalb des Makros sind sie über das Objekt `params` zugreifbar ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=It%20is%20often%20useful%20to,For%20example%2C%20if%20the%20macro)). Wichtig zu wissen: Klipper übergibt alle Parameter als **Strings** und wandelt die Namen in **Großbuchstaben** um ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=M117%20Now%20at%20,100)). 

**Parameter verwenden:** Angenommen, man definiert folgendes Makro:

```ini
[gcode_macro SET_PERCENT]
gcode:
  M117 Now at { params.VALUE|float * 100 }%
```

Dieses Makro erwartet einen Parameter `VALUE`. Ruft man nun `SET_PERCENT VALUE=.2` auf, würde es `M117 Now at 20%` ausgeben ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=It%20is%20often%20useful%20to,For%20example%2C%20if%20the%20macro)). Da `params.VALUE` hier den String `".2"` enthält, wird durch den Filter `|float` zuerst in eine Zahl umgewandelt, bevor die Multiplikation erfolgt. **Hinweis:** Bei arithmetischen Operationen in Makros **müssen** numerische Strings in Zahlentypen umgewandelt werden (mittels `|int` oder `|float`), da ansonsten stringarithmetische Effekte auftreten könnten (z.B. `"2" + "3" = "23"` statt `5`). 

**Default-Werte:** Oft sollen Parameter optional sein und bei Fehlen ein Standardwert genutzt werden. Hierfür bietet Jinja2 den Filter `default(...)`. Beispiel:

```ini
[gcode_macro SET_BED_TEMPERATURE]
gcode:
  {% set temp = params.TEMP|default(50)|int %}
  M140 S{temp}
```

Wird `SET_BED_TEMPERATURE` ohne Parameter aufgerufen, so würde `params.TEMP` *Undefined* sein und der `default(50)` Filter würde dafür sorgen, dass `temp` den Wert `50` annimmt ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=It%27s%20common%20to%20use%20the,For%20example)). Anschließend setzt `M140 S{temp}` die Heizbett-Solltemperatur. Gibt man hingegen einen Wert an (`SET_BED_TEMPERATURE TEMP=70`), würde dieser benutzt. In diesem Beispiel wird noch `|int` genutzt, um sicherzustellen, dass der Wert als Integer interpretiert wird.

**Mehrere Parameter:** Makros können beliebig viele Parameter auswerten. Jeder definierte Parameter muss natürlich im Makro-Quelltext referenziert werden, damit er eine Wirkung hat. In Klipper sind Parameternamen per Konvention groß geschrieben (auch in der Übergabe können sie klein geschrieben werden – Klipper wandelt sie um). Nicht genutzte Parameter werden ignoriert. 

**Roh-Parameter:** Für Fälle, in denen man die komplette Befehlszeile inklusive aller Parameter und Kommentare als Text auswerten will, gibt es `rawparams`. `rawparams` enthält den unverarbeiteten Parameterstring genau so, wie er beim Makroaufruf eingegeben wurde ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=The%20)). Dies ist beispielsweise nützlich, um eigene Parser zu bauen oder Kommentare mit auszuwerten. Die Klipper-Dokumentation zeigt z.B., wie man mit `rawparams` das Standardkommando `M117` (Anzeige einer Nachricht) überschreiben könnte ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=Note%20that%20this%20will%20include,part%20of%20the%20original%20command)).

**Interne Makro-Variablen:** Innerhalb eines Makros können mit Jinja2 beliebige lokale Variablen mittels `{% set %}` erstellt werden, wie bereits gesehen (`{% set temp = ... %}`). Zusätzlich bietet Klipper die Möglichkeit, persistente Variablen pro Makro zu definieren. Dazu kann man in der Makro-Definition `variable_<name>: <Startwert>` angeben. Mit dem G-Code-Befehl `SET_GCODE_VARIABLE` lassen sich solche Variablen zur Laufzeit ändern und ihr Wert bleibt auch zwischen Aufrufen erhalten ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=Variables%C2%B6)). Zum Zugriff auf eine Variable eines anderen Makros wird das `printer`-Objekt (siehe nächster Abschnitt) verwendet, z.B. `printer["gcode_macro my_macro"].<variablenname>` ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=%5Bgcode_macro%20finish_probe%5D%20gcode%3A%20,bed_temp)). 

Ein Anwendungsfall ist das Aufteilen eines Ablaufs in zwei Makros, die Daten teilen. Beispiel aus der Doku: Ein Makro `start_probe` speichert die aktuelle Bett-Solltemperatur in einer persistenten Variable und fährt dann den Sensor an, und ein zweites Makro `finish_probe` liest die gespeicherte Temperatur wieder aus, um das Heizbett zurückzustellen ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=The%20SET_GCODE_VARIABLE%20command%20may%20be,For%20example)) ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=%5Bgcode_macro%20finish_probe%5D%20gcode%3A%20,bed_temp)). Hier ein vereinfachter Auszug:

```ini
[gcode_macro start_probe]
variable_bed_temp: 0
gcode:
  SET_GCODE_VARIABLE MACRO=start_probe VARIABLE=bed_temp VALUE={printer.heater_bed.target}
  M140 S0                     ; Bett ausschalten
  PROBE
  finish_probe

[gcode_macro finish_probe]
gcode:
  M140 S{ printer["gcode_macro start_probe"].bed_temp }
```

In `start_probe` wird die aktuelle Bett-Solltemperatur ausgelesen (`printer.heater_bed.target`) und in der eigenen Variable `bed_temp` gespeichert ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=,printer.heater_bed.target)). Nach dem Probing ruft `start_probe` das zweite Makro `finish_probe` auf, welches die gespeicherte Temperatur über `printer["gcode_macro start_probe"].bed_temp` referenziert und das Heizbett wieder auf diesen Wert stellt ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.

html#:~:text=%5Bgcode_macro%20finish_probe%5D%20gcode%3A%20,bed_temp)). Solche persistenten Variablen sind hilfreich, um Zustand zwischen Makro-Aufrufen zu übertragen. Man sollte jedoch beachten, dass *auch hier* die Makro-Auswertung zu berücksichtigen ist: `SET_GCODE_VARIABLE` ändert den Wert erst **beim Ausführen** – falls man innerhalb desselben Makros noch darauf zugreifen will, klappt das nicht, da die Auswertung schon vorher stattfand ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=Be%20sure%20to%20take%20the,into%20account%20when%20using%20SET_GCODE_VARIABLE)).



















## Zugriff auf den Druckerstatus (`printer`-Objekt)

Klipper stellt im Makro-Kontext ein spezielles Objekt `printer` bereit, mit dem man auf nahezu alle internen Statuswerte des Druckers lesend zugreifen kann ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=The%20)). Über `printer` kann man z.B. Temperaturen, Positionen, Endstop-Status, Lüftergeschwindigkeiten und vieles mehr abfragen. Jede Sektion der `printer.cfg` erzeugt i.d.R. ein entsprechendes Unterobjekt unter `printer`. Zum Beispiel: 

- `printer.extruder.temperature` – aktuelle Hotend-Temperatur  
- `printer.heater_bed.target` – Zieltemperatur des Heizbetts  
- `printer.gcode_move.velocity` – aktuelle Bewegungsgeschwindigkeit  
- `printer.toolhead.position.x` – aktuelle X-Position des Toolheads  

Ein kurzes Beispiel: 

```ini
[gcode_macro SLOW_FAN]
gcode:
  M106 S{ printer.fan.speed * 0.5 * 255 }
```

Angenommen, `printer.fan.speed` gibt die aktuelle Lüfterleistung (0.0 bis 1.0) des Gehäuselüfters an, so würde dieses Makro den Lüfter auf die Hälfte seiner aktuellen Geschwindigkeit setzen, indem es den PWM-Wert entsprechend skaliert ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=%5Bgcode_macro%20slow_fan%5D%20gcode%3A%20M106%20S,255)) ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=%5Bgcode_macro%20slow_fan%5D%20gcode%3A%20M106%20S,255)). (In Klipper entspricht `M106 S<wert>` einem Lüfterbefehl, wobei Lüfter i.d.R. mit 0-255 angesprochen werden. Daher die Multiplikation mit 255 in diesem Beispiel.)

Neben einfachen Sensorwerten kann man über `printer` auch komplexere Informationen abrufen. Die Klipper-Dokumentation enthält eine **Status Reference**, in der alle Felder aufgeführt sind, die im `printer`-Objekt zur Verfügung stehen ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=gcode%3A%20M106%20S%7B%20printer.fan.speed%20,255)). Beispielsweise kann man darüber auch auf benutzerdefinierte Module zugreifen: das Objekt hinter `printer.xyz` entspricht meist einer `[xyz]`-Konfigsektion. Hat eine Sektion einen Namen mit Leerzeichen, muss dieser mit dem Index-Operator angegeben werden, z.B. `printer["generic_heater my_chamber_heater"].temperature` ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=By%20convention%2C%20the%20name%20immediately,temperature)).

Wichtig: Über `printer` kann man *lesen* und in manchen Fällen auch Aktionen auslösen, aber man verändert darüber nicht direkt den Zustand. Änderungen erfolgen stets durch geeignete G-Codes. Beispiel: `printer.toolhead.position.x` liefert die aktuelle X-Position, aber um den Druckkopf zu bewegen, nutzt man `G1 X...`. Manche Objekte bieten boolesche Flags (z.B. ob ein Endstop ausgelöst ist) oder erlauben in Sonderfällen Schreibzugriff (z.B. könnte ein Custom-Module Methoden über `printer.my_module.my_method()` anbieten, falls implementiert). Generell gilt jedoch: Steuernde Eingriffe erfolgen über definierte G-Codes oder die weiter unten beschriebenen `action`-Befehle.

## Spezialkommandos (`action_...`) in Makros

Klipper-Makros können während der **Auswertung** (nicht erst bei G-Code-Ausführung) bestimmte Aktionen ausführen, die über spezielle Funktionen mit Prefix `action_` bereitgestellt werden ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=There%20are%20some%20commands%20available,code%20commands%20are%20executed)). Dazu zählen:

- **`action_respond_info("Nachricht")`:** Sendet eine Info-Nachricht zurück an den Host (erscheint z.B. im Terminal mit `// `-Prefix) ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=Available%20)). Dies ist nützlich, um Benutzern Feedback aus Makros zu geben.
- **`action_raise_error("Fehlertext")`:** Bricht das aktuelle Makro (und aufrufende Makros) ab und meldet einen Fehler (im Terminal mit `!! `) ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=,the%20reason%20for%20the%20shutdown)).
- **`action_emergency_stop("optional Grund")`:** Versetzt den Drucker in den Shutdown-Zustand (Not-Halt) ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=There%20are%20some%20commands%20available,code%20commands%20are%20executed)) ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=,hello_world)). Dies passiert sofort beim Makro **auswerten** – also sehr früh.
- **`action_call_remote_method("name", args...)`:** Ruft eine **Remote-Methode** auf, die von einem externen Client bereitgestellt wird ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=,hello_world)). Dieses Mechanismus ist besonders in Verbindung mit *Moonraker* relevant (siehe nächster Abschnitt), da darüber der Klipper-Host bestimmte Systemfunktionen auslösen kann.

Beispielsweise könnte man folgende Makros definieren, um den Host-PC neu zu starten oder herunterzufahren:

```ini
[gcode_macro REBOOT_HOST]
gcode:
  { action_call_remote_method("reboot_machine") }

[gcode_macro SHUTDOWN_HOST]
gcode:
  { action_call_remote_method("shutdown_machine") }
```

Diese Makros nutzen Moonrakers Remote-Methoden "`reboot_machine`" bzw. "`shutdown_machine`", um den Rechner über einen HTTP-Aufruf neu zu starten bzw. auszuschalten ([Configuration - Moonraker](https://moonraker.readthedocs.io/en/latest/configuration/#:~:text=%5Bgcode_macro%20SHUTDOWN%5D%20gcode%3A%20%7Baction_call_remote_method%28)). Beim Ausführen dieser Makros würde Moonraker also das entsprechende Betriebssystem-Kommando ausführen.

Ein weiteres Beispiel für `action_call_remote_method` ist die Interaktion mit einem MQTT-Dienst. Moonraker bietet eine Remote-Methode `publish_mqtt_topic`. So kann ein Makro eine MQTT-Nachricht versenden:

```ini
[gcode_macro PUBLISH_ALERT]
gcode:
  {% set payload = params.PAYLOAD|default(None) %}
  { action_call_remote_method("publish_mqtt_topic",
                               topic="klipper/alert",
                               payload=payload,
                               qos=0, retain=False) }
```

Hier würde das Makro `PUBLISH_ALERT PAYLOAD="Hello"` das MQTT-Topic `klipper/alert` mit dem Inhalt `"Hello"` veröffentlichen ([Configuration - Moonraker](https://moonraker.readthedocs.io/en/latest/configuration/#:~:text=%5Bgcode_macro%20PUBLISH_ALERT%5D%20gcode%3A%20%7B,payload%3Ddata%2C%20qos%3D0%2C%20retain%3DFalse%2C%20use_prefix%3DTrue)). Solche Integrationen erlauben z.B. Benachrichtigungen oder die Ansteuerung externer Smart-Home-Geräte direkt aus dem Drucker heraus.

**G-Code-Zustand beachten:** Da Makros eine Sequenz von G-Codes ausführen, muss man den **Druckerzustand** (Koordinatenmodus, Extrusionsmodus etc.) im Makro ggf. gezielt setzen. Es wird empfohlen, vor Bewegungsbefehlen den Zustand explizit festzulegen oder temporär zu speichern. Klipper stellt z.B. die Kommandos `SAVE_GCODE_STATE` und `RESTORE_GCODE_STATE` bereit, um den aktuellen Parser-Zustand zu sichern und später wiederherzustellen ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=A%20common%20way%20to%20accomplish,For%20example)). Im obigen Beispiel könnte man um die Relativbewegung herum diese Kommandos einsetzen, um z.B. absolute/relative Modus sicher zu handhaben und am Ende den Originalzustand fortzuführen.