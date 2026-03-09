# Helfer und Voraussetzungen für die Blueprint-Automation  
**Marstek Speicher: Lade-Entlade-Regelung mit force-mode**

Diese Anleitung listet alle **Helfer (Helpers)** und sonstigen **Entitäten** auf, die Sie anlegen bzw. bereitstellen müssen, damit die Blueprint-Automation lauffähig ist.

---

## 1. Benötigte Helfer (input_number)

Diese Helfer legen Grenzwerte fest und müssen **vor** dem Anlegen der Automation aus dem Blueprint existieren.

| Helfer (Typ)   | Empfohlene Entity-ID (Beispiel) | Einheit | Kurzbeschreibung |
|----------------|----------------------------------|--------|-------------------|
| **Input Number** | `input_number.marstek_ladeleistung_limit` | W (Watt) | **Max. Ladeleistung:** Obergrenze in Watt, bis zu der die Automation die Ladeleistung automatisch setzen darf. Typisch z. B. 1000–3000, abhängig vom Wechselrichter. |
| **Input Number** | `input_number.marstek_max_soc`            | %       | **Max. SOC:** Ladezustand in %, bei dem das Laden gestoppt wird (z. B. 100 = voll, 90 = Erhaltung). |
| **Input Number** | `input_number.marstek_min_soc`            | %       | **Min. SOC:** Untergrenze für Entladung; unter diesem Wert wird nicht mehr entladen (Tiefentladungsschutz). |

### Anlegen in Home Assistant

1. **Einstellungen** → **Geräte & Dienste** → **Helfer** → **Helfer erstellen**
2. Typ **Zahl** wählen.
3. Name und Entity-ID wie in der Tabelle (oder Ihrem Schema) vergeben.
4. **Min / Max / Schritt** sinnvoll setzen, z. B.:
   - **Ladeleistung-Limit:** Min 0, Max 5000, Schritt 100, Einheit W
   - **Max. SOC:** Min 50, Max 100, Schritt 5, Einheit %
   - **Min. SOC:** Min 0, Max 50, Schritt 5, Einheit %

Diese drei Helfer werden in der Blueprint-GUI als **Entitäten** auswählbar gemacht (siehe Abschnitt „Entitäten in der Blueprint-GUI“).

---

## 2. Benötigte Geräte-/Integration-Entitäten

Diese Entitäten stammen in der Regel von **Integrationen** (z. B. Modbus, Smartmeter, Venus/Marstek) und müssen bereits existieren:

| Verwendung in der Automation | Typ | Beschreibung |
|------------------------------|-----|--------------|
| **Zähler Einspeisung (Export)** | `sensor.*` | Sensor für ins Netz eingespeiste Leistung. Einheit über Blueprint-Option **Grid-Zähler-Einheit** wählbar (kW oder W). |
| **Zähler Bezug (Import)**     | `sensor.*` | Sensor für aus dem Netz bezogene Leistung (gleiche Einheit wie Export). |
| **Schalter Steuermodus (RS485)** | `switch.*` | Schalter für den RS485-Steuermodus; bei „aus“ schaltet die Automation ihn wieder „ein“. |
| **Zahl Ladeleistung einstellen** | `number.*` | Setzt die Ladeleistung am Gerät (in Watt). |
| **Zahl Entladeleistung einstellen** | `number.*` | Setzt die Entladeleistung am Gerät (in Watt). |
| **Sensor Batterie-Ladezustand (SOC)** | `sensor.*` | Aktueller Ladezustand der Batterie in %. |
| **Select Force-Mode** | `select.*` | Optionen z. B. „Laden“ / „Stopp“ / „Entladen“. Kann entfallen, wenn das Gerät keinen Force-Mode hat. |

Alle diese Entitäten sind im Blueprint als **auswählbare Felder** mit genauer Beschreibung vorgesehen.

---

## 3. Kurzüberblick: Was anlegen?

- **3 Helfer (input_number):**  
  Max. Ladeleistung (W), Max. SOC (%), Min. SOC (%).
- **Übrige Entitäten:**  
  Von Integrationen/Geräten (Smartmeter, Marstek/Venus Modbus etc.); in der Blueprint-GUI auswählen.

Wenn Sie die drei Helfer angelegt und die passenden Sensoren/Schalter/Numbers/Selects aus Ihrer Installation in der Blueprint-GUI ausgewählt haben, ist die Automation lauffähig.
