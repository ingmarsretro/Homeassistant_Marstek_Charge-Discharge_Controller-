# Blueprint: Marstek Speicher Lade-Entlade-Regelung

## Installation

1. Im Home-Assistant-Config Ordner anlegen: `config/blueprints/automation/marstek/`
2. Die Datei `blueprint.yaml` in diesen Ordner kopieren (oder den Inhalt in eine neue Datei z. B. `marstek_speicher_regelung.yaml` einfügen).
3. Unter **Einstellungen** → **Automationen** → **Blueprint** erscheint der Blueprint; eine neue Automation **über Blueprint erstellen** und diesen Blueprint wählen.

## Deutsche Beschreibung (im Blueprint-GUI)

Der Blueprint enthält eine **genaue deutsche Beschreibung** im Feld `blueprint.description`: Sie erklärt, dass die Automation einen Marstek/Venus-Speicher anhand von Einspeisung und Bezug steuert, Hysterese und schrittweise Anpassung nutzt und welche Helfer nötig sind.

## Einstellbare Parameter in der GUI

### Entitäten (alle mit Erklärung)

- **Zähler Einspeisung (Export)** – Sensor Einspeisung
- **Zähler Bezug (Import)** – Sensor Bezug  
- **Schalter Steuermodus (RS485)** – Steuermodus ein/aus
- **Zahl Ladeleistung / Entladeleistung einstellen** – Number-Entitäten vom Gerät
- **Helfer Max. Ladeleistung, Max. SOC, Min. SOC** – input_number-Helfer
- **Sensor Batterie-Ladezustand (SOC)** – SOC in %
- **Select Force-Mode** – optional, Laden/Stopp/Entladen

### Grid-Zähler-Einheit

- **Kilowatt (kW)** – Zähler liefert kW, wird intern in Watt umgerechnet.
- **Watt (W)** – Zähler liefert bereits Watt.

### Zeiten (mit Erklärung)

- **Trigger-Intervall (Sekunden)** – z. B. 5 = alle 5 s Zeit-Trigger.
- **Verzögerung nach Lade-Anpassung (Sekunden)** – z. B. 1.
- **Verzögerung nach Entlade-/Reduktion-Anpassung (Sekunden)** – z. B. 4.

### Deadband und Schritte (mit Erklärung)

- **Deadband / Soll-Reserve (Watt)** – Toleranz, ab der reagiert wird.
- **Sicherheitsreserve Einspeisung (Watt)** – Abzug bei Überschuss.
- **Lade-Schritt / Entlade-Schritt Erhöhen/Reduzieren (Watt)** – maximale Änderung pro Zyklus.
- **Max. Entladeleistung (Watt)** – Obergrenze Entladung.
- **Reaktions-Schwelle Überschuss/Bezug (Watt)** – Mindest-Überschuss/Bezug zum Reagieren.

## Helfer anlegen

Siehe **HELPER_ANLEITUNG.md** – dort sind die drei nötigen **input_number**-Helfer und alle weiteren Entitäten auf Deutsch beschrieben.
