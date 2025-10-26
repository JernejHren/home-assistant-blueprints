# EV Load Management (House Power Based)

![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Blueprint-blue)

## Opis
Ta **Home Assistant blueprint** omogoča dinamično polnjenje električnega vozila glede na porabo hiše in tarifni blok.

Glavne funkcionalnosti:
- Polnjenje se vklopi, ko je skupna poraba manjša od `(blok_limit - buffer - 4200 W)`  
  (buffer in 4200 W se upoštevata **samo pri vklopu**)  
- Po zagonu se tok dinamično prilagaja glede na trenutno porabo **brez bufferja**  
- Polnjenje se ustavi, ko polnilnica preide v stanje `State A - Idle`  
- Avtomatizacija se sproži **ob prehodu iz Idle v katerokoli drugo stanje** in preverja stanje vsake 30 sekund, dokler se polnilnica ne vrne v Idle

---

## Namen in uporaba
Blueprint je napisan za uporabo z integracijo **[ABB Terra AC Modbus Integration for Home Assistant](https://github.com/JernejHren/ABB-Terra-AC)**,  
prilagojen je za uporabo v Sloveniji.

Za pravilno delovanje priporočam uporabo skupaj z integracijo **[Home Assistant Network Tariff](https://github.com/frlequ/home-assistant-network-tariff)**,  
ki zagotavlja podatek o trenutnem časovnem bloku (`tariff_block`).

---

## Vhodni parametri (Inputs)
| Parameter | Opis | Privzeta vrednost |
|-----------|------|-----------------|
| Export / House Power Sensor | Senzor skupne porabe/izvoza hiše (W) | - |
| Charger switch | Stikalo za vklop/izklop polnjenja | - |
| Charger current (A) | Trenutni tok polnjenja | - |
| Charger state sensor | Stanje polnilnice (Idle/Charging/…) | - |
| Current tariff block sensor | Trenutni tarifni blok (1–5) | - |
| Block 1–5 limit (W) | Omejitev moči za vsak blok | 6000 W |
| Start buffer (W) | Buffer za zagon polnjenja | 500 W |
| Minimum current (A) | Minimalni tok polnjenja | 0 A |
| Maximum current (A) | Maksimalni tok polnjenja | 16 A |
| Debug log | Omogoči debug sporočila v HA log | false |

---

## Namestitev
1. Shrani YAML datoteko blueprint-a v mapo:
2. home-assistant-blueprints/
└── automation/
└── EvLoadManagement/
└── ev_load_management.yaml


2. V Home Assistant:
   - Settings → Blueprints → Import Blueprint → `Paste YAML or URL`  
3. Nastavi vse vhodne parametre (senzorje, omejitve, tokove).

---

## Opombe
- Blueprint je optimiziran za **3-fazno polnjenje** preko ABB Terra AC polnilnice.  
- Presežek moči se prilagaja dinamično; večji presežek pomeni hitrejše zmanjševanje toka.  
- Za uporabo v drugih državah ali z drugimi tarifnimi sistemi je morda potrebno prilagoditi **logiko tarifnega bloka** ali **step_amp**.

---
## English 🇬🇧

### Description
This **Home Assistant blueprint** enables dynamic EV charging based on house power consumption and tariff block.

Key features:
- Charging starts when total consumption is below `(block_limit - buffer - 4200 W)`  
  (buffer and 4200 W are **used only at startup**)  
- After starting, charging current is dynamically adjusted based on actual consumption **without buffer**  
- Charging stops when the charger enters `State A - Idle`  
- Automation triggers **when leaving Idle to any other state** and checks the state every 30 seconds until the charger returns to Idle

### Purpose and usage
The blueprint is designed to work with **[ABB Terra AC Modbus Integration for Home Assistant](https://github.com/JernejHren/ABB-Terra-AC)**,  
adapted for use in Slovenia.

For proper operation, it is recommended to use together with **[Home Assistant Network Tariff](https://github.com/frlequ/home-assistant-network-tariff)**,  
which provides the current tariff block (`tariff_block`) information.

### Inputs
| Parameter | Description | Default |
|-----------|-------------|---------|
| Export / House Power Sensor | Sensor of total house consumption/export (W) | - |
| Charger switch | Switch to turn charging on/off | - |
| Charger current (A) | Current charging amperage | - |
| Charger state sensor | Charger state (Idle/Charging/…) | - |
| Current tariff block sensor | Current tariff block (1–5) | - |
| Block 1–5 limit (W) | Power limit per block | 6000 W |
| Start buffer (W) | Startup buffer | 500 W |
| Minimum current (A) | Minimum charging current | 0 A |
| Maximum current (A) | Maximum charging current | 16 A |
| Debug log | Enable debug messages in HA log | false |

### Installation
1. Save the blueprint YAML file in the folder:
home-assistant-blueprints/
└── automation/
└── EvLoadManagement/
└── ev_load_management.yaml

2. In Home Assistant:
   - Settings → Blueprints → Import Blueprint → `Paste YAML or URL`  
3. Configure all inputs (sensors, limits, current settings).

### Notes
- Optimized for **3-phase charging** via ABB Terra AC charger.  
- Excess power is adjusted dynamically; larger excess reduces current faster.  
- For use in other countries or with different tariff systems, **tariff block logic** or **step_amp** may need adjustment.

---

## License
[MIT License](LICENSE)



