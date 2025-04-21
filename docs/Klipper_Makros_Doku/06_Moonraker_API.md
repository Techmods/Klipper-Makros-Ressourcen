## Makros und Moonraker-API (externe Ansteuerung)

Moonraker ist der HTTP/WebSocket-Server, der zwischen Klipper und Web-UIs (wie Mainsail) vermittelt. Über die Moonraker-API können Makros auch von externen Anwendungen oder Skripten ausgelöst werden. Prinzipiell gibt es zwei relevante Wege:

1. **G-Code über die HTTP-API senden:** Moonraker bietet Endpunkte an, um G-Codes an Klipper zu schicken. Ein Beispiel ist der HTTP-POST Request auf `/printer/gcode/script` mit entsprechendem JSON-Datenfeld. Hier ein minimaler Curl-Aufruf, der das oben definierte Makro `LED_BLINK` ausführt:

   ```bash
   curl -X POST -H "Content-Type: application/json" -d '{"script": "LED_BLINK"}' http://<Drucker-IP>/printer/gcode/script
   ```

   Man kann auch Parameter mitgeben, indem man sie direkt an den Makronamen anhängt, z.B. `{"script": "SET_BED_TEMPERATURE TEMP=60"}` ([Printer Administration - Moonraker](https://moonraker.readthedocs.io/en/latest/external_api/printer/#:~:text=%7B%20)). Der Endpunkt führt die übergebenen G-Codes nacheinander aus und gibt als Antwort normalerweise `"ok"` zurück, sobald fertig ([Printer Administration - Moonraker](https://moonraker.readthedocs.io/en/latest/external_api/printer/#:~:text=HTTP%20Request)) ([Printer Administration - Moonraker](https://moonraker.readthedocs.io/en/latest/external_api/printer/#:~:text=Parameters%20Name%20Type%20Default%20Description,n)). Über die WebSocket-Variante (JSON-RPC) kann man das Gleiche erreichen, was z.B. von UIs live genutzt wird.

   Zusätzlich existiert ein GET-Endpunkt `/printer/gcode/help`, der eine Liste aller registrierten G-Code-Kommandos und ihrer Beschreibungen liefert ([Printer Administration - Moonraker](https://moonraker.readthedocs.io/en/latest/external_api/printer/#:~:text=Example%20Response)). Darin tauchen auch die Makros (mit ihrer Description oder als "G-Code macro" gekennzeichnet) auf ([Printer Administration - Moonraker](https://moonraker.readthedocs.io/en/latest/external_api/printer/#:~:text=%7B%20,Code%20macro)). Dies nutzen Oberflächen, um z.B. Autocompletion oder Hilfetexte bereitzustellen.

2. **Remote-Methoden auslösen:** Wie im vorherigen Abschnitt beschrieben, erlaubt `action_call_remote_method` aus einem Makro heraus Moonraker-Funktionen anzustoßen (z.B. Shutdown). Umgekehrt bietet Moonraker aber auch von sich aus Möglichkeiten, auf Ereignisse zu reagieren – beispielsweise kann man in der Moonraker-Konfiguration definieren, dass beim Eintreten bestimmter Zustände ein G-Code oder Makro ausgeführt wird. Ein Beispiel sind *Idle-Timeouts*, *Notifikationen* oder *Klipper-Telegram Bots*, die im Hintergrund Makros anstoßen, indem sie über die API Kommandos senden ([CreatBotMoonraker/dev_changelog.md at ... - CreatBot Git Repository](https://server.creatbot.com/Gitea/CreatBot/CreatBotMoonraker/src/commit/3d9052d711464cb5ea81cde99769c8f1fd7d3f8d/docs/dev_changelog.md#:~:text=CreatBotMoonraker%2Fdev_changelog.md%20at%20...%20,remember%20that%20it%20will)).

Für die Praxis heißt das: Sie können z.B. ein Makro per Smartphone-App oder einem Home-Assistent-System starten, indem Sie einen simplen Webhook an Moonraker schicken. Viele der dritten Anwendungen (z.B. der OctoPrint-Ersatz *Fluidd*, Home Assistant Integrationen, Telegram-Bots) nutzen nichts anderes als diese API, um Klipper-Makros fernzusteuern.

**GPIO-Buttons und Sensoren:** Nicht direkt Moonraker, aber als ergänzende Möglichkeit: Klipper kann physische Buttons oder Sensoren so konfigurieren, dass sie **automatisch Makros auslösen**. Über die Sektion `[gcode_button]` lässt sich ein Pin als Button definieren, der bei Druck/Loslassen bestimmte G-Codes ausführt ([Configuration reference - Klipper documentation](https://www.klipper3d.org/Config_Reference.html#:~:text=Execute%20gcode%20when%20a%20button,QUERY_BUTTON%20button%3Dmy_gcode_button)) ([Configuration reference - Klipper documentation](https://www.klipper3d.org/Config_Reference.html#:~:text=,commands%20on%20a%20button%20release)). Im Beispiel:

```ini
[gcode_button case_button]
pin: ^PA1
press_gcode:
  PAUSE
release_gcode:
  RESUME
```

Dies würde einen an PA1 angeschlossenen Taster so behandeln, dass ein Druck den Druck pausiert und das Loslassen fortsetzt (hier durch Aufruf der entsprechenden Makros PAUSE/RESUME). `^` vor dem Pin bedeutet Pull-Up-Aktivierung ([Configuration reference - Klipper documentation](https://www.klipper3d.org/Config_Reference.html#:~:text=Input%20pins%20may%20be%20preceded,alternatively%20be%20preceded%20by)). Innerhalb von `press_gcode`/`release_gcode` kann wiederum voller Makro-Template-Code verwendet werden. Ähnlich gibt es `[filament_switch_sensor]` für Filament-Runout-Sensoren, wo man z.B. `pause_gcode:` auf ein Makro (oder direkt G-Codes) setzen kann, das bei Filamentende ausgeführt wird. Diese hardwaregetriggerten Makros laufen vollständig auf der Klipper-Seite ab und benötigen keine zusätzliche Software.