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

## License
[MIT License](LICENSE)


