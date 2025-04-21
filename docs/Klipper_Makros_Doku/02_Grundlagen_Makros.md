## Funktionsweise von Klipper-Makros

**Was sind Makros?** In Klipper sind Makros benutzerdefinierte G-Code-Befehle, die aus einer Folge von regulären G-Codes bestehen ([Voidtrance Klipper Macros Beginners Guide | Voron Documentation](https://docs.vorondesign.com/community/howto/voidtrance/Klipper_Macros_Beginners_Guide.html#:~:text=Macros%20are%20a%20collection%20of,macro%20is%20executed%20to%20completion)). Makros werden in der Klipper-Konfigurationsdatei mit einem eigenen Abschnitt definiert (Beginnend mit `[gcode_macro <Name>]`). Sobald ein Makro definiert ist, kann es wie ein normaler G-Code-Befehl durch Eingabe seines Namens (ggf. mit Parametern) ausgeführt werden. Klipper führt dann die im Makro hinterlegten Kommandos als Einheit aus ([Voidtrance Klipper Macros Beginners Guide | Voron Documentation](https://docs.vorondesign.com/community/howto/voidtrance/Klipper_Macros_Beginners_Guide.html#:~:text=Macros%20are%20a%20collection%20of,macro%20is%20executed%20to%20completion)). Da Klipper single-threaded arbeitet, wird immer nur ein Makro zur Zeit ausgeführt.

**Definition eines Makros:** Ein einfaches Makro besteht mindestens aus dem Makro-Namen und dem Block der auszuführenden G-Codes. Beispiel:

```ini
[gcode_macro LED_BLINK]
description: Blinkt eine LED zweimal
gcode:
  SET_PIN PIN=my_led VALUE=1   ; LED an
  G4 P1000                     ; 1 Sekunde warten
  SET_PIN PIN=my_led VALUE=0   ; LED aus
  G4 P1000                     ; 1 Sekunde warten
  SET_PIN PIN=my_led VALUE=1   ; LED erneut an
  G4 P1000
  SET_PIN PIN=my_led VALUE=0   ; LED aus
```

In diesem Beispiel wird mit `LED_BLINK` ein Makro definiert, das eine LED zweimal blinken lässt (vorausgesetzt, in der Konfiguration ist ein `[output_pin my_led]` definiert). Die Option `description:` ist optional und beschreibt kurz die Funktion – diese Beschreibung wird z.B. von der `HELP`-Funktion oder in der Oberfläche angezeigt ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=description%3A%20Blink%20my_led%20one%20time,G4%20P2000%20SET_PIN%20PIN%3Dmy_led%20VALUE%3D0)). Wichtig ist, dass im `gcode:`-Block alle Folgezeilen **eingerückt** sind; der Inhalt des Makros darf nicht linksbündig mit der Sektionsüberschrift stehen ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=Note%20how%20the%20,never%20start%20at%20the%20beginning)).

**Makros aufrufen:** Ein Makro wird aufgerufen, indem sein Name als G-Code eingegeben wird (z.B. per Terminal oder über die GUI). Man kann Makros auch definieren, die vorhandene G-Codes “überschreiben”. Beispielsweise könnten Sie ein Makro `[gcode_macro G28]` definieren, um das Homing-Verhalten anzupassen – beim Aufruf von `G28` würde dann Ihr Makro statt des standardmäßigen Homing ausgeführt. Hier ist Vorsicht geboten, um Standardfunktionen nicht unbeabsichtigt zu verändern.

**G-Code-Zustand beachten:** Da Makros eine Sequenz von G-Codes ausführen, muss man den **Druckerzustand** (Koordinatenmodus, Extrusionsmodus etc.) im Makro ggf. gezielt setzen. Es wird empfohlen, vor Bewegungsbefehlen den Zustand explizit festzulegen oder temporär zu speichern. Klipper stellt z.B. die Kommandos `SAVE_GCODE_STATE` und `RESTORE_GCODE_STATE` bereit, um den aktuellen Parser-Zustand zu sichern und später wiederherzustellen ([Commands templates - Klipper documentation](https://www.klipper3d.org/Command_Templates.html#:~:text=A%20common%20way%20to%20accomplish,For%20example)). Im obigen Beispiel könnte man um die Relativbewegung herum diese Kommandos einsetzen, um z.B. absolute/relative Modus sicher zu handhaben und am Ende den Originalzustand fortzuführen.
