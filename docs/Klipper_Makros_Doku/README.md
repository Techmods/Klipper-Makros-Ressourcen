# README – Klipper Makros & Jinja2 Templates

Dieses Dokument bietet eine umfassende Anleitung zur Erstellung, Nutzung und Erweiterung von G-Code-Makros in der [Klipper Firmware](https://www.klipper3d.org/), unter besonderer Berücksichtigung der [Jinja2 Template-Sprache](https://jinja.palletsprojects.com/en/stable/templates/) sowie deren Integration mit [Moonraker](https://moonraker.readthedocs.io/en/latest/), [Mainsail](https://docs.mainsail.xyz/) und Erweiterungen wie dem [TradRack](https://github.com/Annex-Engineering/TradRack) von Annex Engineering.

## Inhalt

- Grundlagen von Klipper-Makros
- Verwendung von Parametern & Variablen
- Dynamische Steuerung durch Jinja2
- Zugriff auf Druckerstatus via `printer`
- Aktionen und Remote-Methoden
- Integration in Mainsail (UI, Parameter, Dialoge)
- Kommunikation über Moonraker API
- GPIO-Buttons und Sensor-Makros
- Erweiterte Praxis: TradRack-Makros
- Nützliche Jinja2-Funktionen im Drucker-Kontext

## Zielgruppe

Diese Doku richtet sich an Maker, Power-User und Entwickler, die ihre 3D-Drucker mit Klipper auf ein neues Automatisierungs-Level heben möchten. Besonders empfohlen für:

- Benutzer, die Makros für Setup-Routinen (Preheat, Cleaning etc.) nutzen wollen
- Entwickler, die komplexe Bedingungen oder Schleifen verwenden möchten
- Fortgeschrittene, die Moonraker, MQTT, GPIO oder Zusatzmodule wie TradRack integrieren

## Voraussetzungen

- Ein funktionierendes Klipper-Setup (z. B. Octopus, SKR, EBB36...)
- Zugriff auf die `printer.cfg`
- Basiswissen in G-Code und ggf. Python-ähnlicher Syntax (für Jinja2)
- (Optional) Mainsail oder Fluidd als UI, Moonraker als Backend

## Nutzung

Die Inhalte aus `Klipper Makros Jinja2.md` können direkt übernommen und angepasst werden. Enthalten sind vollständige Codebeispiele, Strukturübersichten und Praxistipps für alle Anwendungsbereiche.

## Haftungsausschluss

Diese Dokumentation wurde mithilfe von ChatGPT (GPT-4) erstellt und dient ausschließlich zu Informationszwecken. Alle Angaben sind ohne Gewähr. Änderungen an Firmware, Konfigurationsdateien oder Hardware erfolgen **auf eigene Gefahr**. Jeder Nutzer ist selbst dafür verantwortlich, die Informationen zu prüfen, zu verstehen und auf die eigene Hardware korrekt anzuwenden. Es wird ausdrücklich empfohlen, neue Makros und Konfigurationen **vorher ohne aktiven Druckvorgang zu testen**.

## Lizenz

Dieses Dokument ist Teil eines privaten Dokumentationsprojekts. Weitergabe für nicht-kommerzielle Zwecke ist erlaubt. Eine Veröffentlichung auf GitHub, GitBook oder anderen Plattformen ist optional, jedoch erwünscht bei aktiver Weiterentwicklung.

## Autor

Erstellt & gepflegt von einem technikbegeisterten Anwender mit Fokus auf 3D-Druck, CNC und Automatisierung. Fragen oder Feedback gerne via Kommentar oder Issue melden.

---

**Letztes Update:** April 2025

