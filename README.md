# Marstek Venus E3 – Smartes Laden & Entladen per Home Assistant Blueprint

> [!WARNING]  
> **BETA VERSION:** This is the first beta release. It has currently only been tested successfully on my personal system. Use this blueprint at your own risk. The author assumes no liability for any damage to your hardware or unexpected system behavior.


Dieses Repository enthält ein **Home‑Assistant Blueprint** zur dynamischen Regelung von Lade- und Entladeleistung eines **Marstek Venus E3** Batteriespeichers.  
Ziel ist es, den **Netzbezug** und die **Netzeinspeisung** möglichst nahe bei 0 W zu halten – innerhalb eines konfigurierbaren **Deadbands** – ohne starkes Schwingen.

> **Hinweis:**  
> Die Anbindung des Marstek Venus E3 an Home Assistant erfolgt über die Modbus‑TCP‑Integration  
> [`ViperRNMC/marstek_venus_modbus`](https://github.com/ViperRNMC/marstek_venus_modbus).  
> Dieses Blueprint setzt voraus, dass diese Integration installiert und konfiguriert ist (HACS).

---

## Funktionsübersicht

- **Automatische Lade-/Entlade-Regelung** anhand der aktuellen Netzleistung:
  - **Laden** bei Netzeinspeisung (Export) → Ladeleistung wird erhöht, bis die Einspeisung in den eingestellten Toleranzbereich (Deadband) fällt.
  - **Entladen** bei Netzbezug (Import) → Entladeleistung wird erhöht, bis der Bezug in den Deadband-Bereich fällt.
- **Force-Mode-Steuerung** über ein Select:
  - Modus `charge` / `discharge` / `stop` (oder frei konfigurierbar über Parameter).
  - Beim Wechsel des Force-Mode wird stets die „Gegenrichtung“ auf 0 W gesetzt (z.B. beim Wechsel auf `discharge` wird die Ladeleistung auf 0 gesetzt).
- **Deadband um 0 W**:
  - Verhindert ständiges Umschalten bei sehr kleinen Leistungen (z.B. ±5 W um 0 W).
- **SoC-Abhängige Entladung**:
  - Entladen nur oberhalb eines einstellbaren Mindest‑SoC (z.B. 40 %).
  - Laden nur unterhalb eines einstellbaren Max‑SoC (z.B. 100 %).
- **Schrittweise Anpassung**:
  - Konfigurierbare Schrittweiten für Lade- und Entladeleistung (z.B. 70 W / 100 W pro Zyklus).
  - Konfigurierbare Verzögerungen zwischen den Regelschritten.

---

## Dateien

- `blueprints/automation/marstek/marstek_lade_entlade_force_mode.yaml`  
  → Das eigentliche Blueprint für Home Assistant.

---

## Voraussetzungen

1. **Home Assistant** (Core oder OS)
2. **Modbus‑TCP Integration für Marstek Venus**  
   Installation und Konfiguration gemäß  
   [`ViperRNMC/marstek_venus_modbus`](https://github.com/ViperRNMC/marstek_venus_modbus):
   - RS485 → Modbus‑TCP‑Gateway vorhanden und konfiguriert
   - Integration über HACS installiert
   - Marstek Venus E3 in Home Assistant eingebunden
3. Folgende Entitäten sollten durch die Integration bereitgestellt werden (oder äquivalente Entities):
   - Smartmeter‑Sensor **Netzbezug** in kW (immer positiv)
   - Smartmeter‑Sensor **Netzeinspeisung** in kW (immer positiv)
   - **Batterie‑SoC** in %
   - Number‑Entity **Ladeleistung setzen** (W)
   - Number‑Entity **Entladeleistung setzen** (W)
   - Select‑Entity **Force‑Mode** (z.B. Optionen `stop`, `charge`, `discharge`)

---

## Installation des Blueprints

1. Kopiere die Blueprint‑Datei in deinen Home‑Assistant‑Blueprint‑Ordner, z.B.:
blueprints/automation/marstek/marstek_lade_entlade_force_mode.yaml
   
2. Starte Home Assistant neu oder lade die Blueprints neu.
3. Gehe zu  
   **Einstellungen → Automationen & Szenen → Blueprints**  
   und wähle das Blueprint  
   **„Marstek Speicher: Lade-/Entlade-Regelung (Force-Mode) – Smartmeter“** aus.

4. Erstelle eine **neue Automation** aus diesem Blueprint.

---

## Konfiguration im Blueprint

Beim Anlegen der Automation werden folgende Eingaben abgefragt (alle im Blueprint auf Deutsch beschrieben):

### Entitäten

- **Smartmeter: Einspeisung ins Netz (kW)**  
  Sensor für Export-Leistung, z.B. `sensor.kelagsmartmeter_power_usage_out`.

- **Smartmeter: Bezug aus dem Netz (kW)**  
  Sensor für Import-Leistung, z.B. `sensor.kelagsmartmeter_power_usage_in`.

- **Batterie-SoC (%)**  
  Ladezustand der Batterie in Prozent, z.B. `sensor.marstek_venus_modbus_batterie_ladezustand`.

- **Ladeleistung setzen (W)**  
  Number‑Entity für die Ladeleistung, z.B. `number.marstek_venus_modbus_ladeleistung_einstellen`.

- **Entladeleistung setzen (W)**  
  Number‑Entity für die Entladeleistung, z.B. `number.marstek_venus_modbus_entladeleistung_einstellen`.

- **Force-Mode (Select)**  
  Select‑Entity für den Force-Mode, z.B. `select.marstek_venus_modbus_force_mode`.

### Wichtige Parameter

Alle Parameter sind im Blueprint erklärt, u.a.:

- **Maximale Ladeleistung (W)**  
- **Maximale Entladeleistung (W)**  
- **Mindest-SoC für Entladen (%)**  
- **Max-SoC für Laden (%)**  
- **Deadband um 0 W (W)**  
  – Bereich um 0 W, in dem auf `stop` gewechselt und Leistungen genullt werden.

- **Regel-Reserve (W)**  
  – Sicherheitsabstand, damit nicht ständig über 0 W hin‑ und hergeregelt wird.

- **Mindest-Leistung aktiv (W)**  
  – Unterhalb dieser Schwelle werden Lade-/Entladeleistung als „aus“ betrachtet.

- **Schaltschwelle Laden/Entladen (W)**  
  – Ab welcher Import-/Export‑Leistung Charge bzw. Discharge erzwungen wird.

- **Schrittweiten (W)** für Laden/Entladen  
- **Verzögerungen (Sekunden)** nach Lade/Entlade/Reduktions‑Schritten

- **Force-Mode Option: Stop / Charge / Discharge**  
  – Exakte Options‑Strings des Selects (Default: `stop`, `charge`, `discharge`).  
    Hier kannst du abweichende Namen eintragen, falls dein Select anders benannt ist.

---

## Typische Startwerte (Empfehlung)

- Max. Ladeleistung: **1000 W**
- Max. Entladeleistung: **1250 W**
- Mindest-SoC Entladen: **40 %**
- Max-SoC Laden: **100 %**
- Deadband: **5 W**
- Regel-Reserve: **10 W**
- Schaltschwellen Laden/Entladen: **30 W**
- Schrittweite Laden: **70 W**
- Schrittweite Entladen: **100 W**
- Verzögerungen: **7 s** (Laden/Entladen), **4–7 s** für Reduktion

Diese Werte kannst du an dein Netzverhalten und deine Wunsch‑Dynamik anpassen.

---

## Hinweise

- Das Blueprint arbeitet rein lokal in Home Assistant, ohne Cloud.
- Eine zu kleine Deadband oder zu aggressive Schrittweiten können zu sichtbarem „Pumpen“ führen.
- Bei Änderungen an den Parametern empfiehlt sich ein Blick in die **Automations-Traces**, um das Verhalten zu verstehen und ggf. nachzujustieren.

---

## Credits

- **Marstek Venus Modbus Integration:**  
  Dieses Projekt baut auf der hervorragenden Arbeit von  
  [`ViperRNMC/marstek_venus_modbus`](https://github.com/ViperRNMC/marstek_venus_modbus) auf,  
  welche die Modbus‑TCP‑Anbindung des Marstek Venus E3 in Home Assistant ermöglicht.

- **Blueprint / Regelung:**  
  Lade-/Entlade‑Logik, Force‑Mode‑Steuerung und Hysterese wurden speziell für den Betrieb mit Smartmeter‑Import/Export‑Sensoren entwickelt, um den Netzfluss möglichst nahe bei 0 W zu halten.



  # Marstek Venus E3 – Smart Charging & Discharging via Home Assistant Blueprint

This repository provides a **Home Assistant Blueprint** for dynamic regulation of charging and discharging power for the **Marstek Venus E3** battery storage system.

The primary goal is to keep **grid import** and **grid export** as close to 0W as possible — within a configurable **deadband** — while avoiding oscillation.

> [!IMPORTANT]
> **Prerequisite:** This blueprint requires the Modbus TCP integration for Marstek Venus: 
> [`ViperRNMC/marstek_venus_modbus`](https://github.com) (available via HACS).

---

## Features

- **Automated Control** based on real-time grid data:
  - **Charging (Export):** Increases charging power when surplus energy is fed into the grid.
  - **Discharging (Import):** Increases discharge power when energy is drawn from the grid.
- **Force Mode Logic:**
  - Supports `charge`, `discharge`, and `stop` modes.
  - Automatically resets the "opposite direction" to 0W when switching modes (e.g., setting charge to 0W when discharging starts).
- **0W Deadband:** Prevents rapid switching and "pumping" at low power levels.
- **SoC Protection:**
  - Discharge only above a configurable **Min-SoC** (e.g., 40%).
  - Charge only below a configurable **Max-SoC** (e.g., 100%).
- **Smooth Adjustments:**
  - Configurable step sizes (e.g., 70W / 100W increments).
  - Adjustable delays between regulation cycles.

## Prerequisites

1. **Home Assistant** (Core or OS)
2. **Modbus TCP Gateway:** RS485 to Ethernet configured.
3. **Marstek Venus Integration:** Installed via HACS and configured.
4. **Required Entities:**
   - Grid Import/Export sensors (in kW, positive values).
   - Battery SoC sensor (%).
   - Number entities for setting Charge/Discharge power (W).
   - Select entity for Force Mode.

## Installation

1. Copy the blueprint file to your Home Assistant directory:
   `blueprints/automation/marstek/marstek_lade_entlade_force_mode.yaml`
2. Reload your Blueprints or restart Home Assistant.
3. Navigate to **Settings → Automations & Scenes → Blueprints**.
4. Find **"Marstek Storage: Charge/Discharge Control (Force Mode) – Smart Meter"** and click **Create Automation**.

## Configuration Parameters


| Parameter | Description | Recommended Value |
| :--- | :--- | :--- |
| **Max Charge/Discharge** | Maximum power limits for the battery. | 1000W / 1250W |
| **Min/Max SoC** | SoC boundaries for battery activity. | 40% / 100% |
| **Deadband** | Range around 0W where no action is taken. | 5W |
| **Control Reserve** | Safety margin to avoid constant toggling. | 10W |
| **Step Sizes** | Increments for power adjustment per cycle. | 70W (Chg) / 100W (Dch) |
| **Delays** | Wait time between regulation steps. | 7 seconds |

## Notes & Troubleshooting

- **Local Control:** This blueprint operates 100% locally.
- **Fine-Tuning:** If you experience "pumping" (power oscillating), increase the **Deadband** or the **Delays**.
- **Traces:** Use the Home Assistant "Automation Traces" feature to debug why a specific regulation step was triggered.

---

## Credits

- **Integration:** Based on the work by [`ViperRNMC/marstek_venus_modbus`](https://github.com).
- **Logic:** Custom regulation logic optimized for smart meter integration and grid-zero balancing.
