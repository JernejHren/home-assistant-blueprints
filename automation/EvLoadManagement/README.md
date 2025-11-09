# ⚡ EV Load Management (Safe Start, House Power Based)

## 🇸🇮 Opis (Slovenščina)

Blueprint za **dinamično upravljanje polnjenja električnega vozila** glede na trenutno porabo hiše in aktivni tarifni blok.

Ta različica (“Safe Start”) omogoča **varno in stabilno delovanje** polnilnice — še posebej pri modelih, kot je **ABB Terra AC**, kjer lahko zagon z 0 A povzroči napako.  
Avtomatizacija polnjenje zažene ali nadaljuje **šele, ko je na voljo dovolj moči za najmanj 6 A + buffer**, s čimer prepreči napake in neželeno ciklanje.

---

### 🔋 Ključne funkcije

- ⚙️ **Samodejni zagon ob priklopu vozila** (Idle → Active)
- ⚡ Polnilnica se **vklopi šele, ko je dovolj moči za 6 A + buffer**
- 🔁 Tok se **dinamično prilagaja** glede na trenutno porabo hiše
- 🧠 **Buffer (histereza)** se upošteva le pri povečevanju toka
- 🕒 Upošteva **tarifne bloke (1–5)** za omejevanje največje moči
- 📴 Če ni dovolj moči za 6 A, se tok začasno zniža na 0 A (seja ostane aktivna)
- 🔄 Ob ponovni prosti moči se polnjenje **samodejno nadaljuje pri 6 A**
- 🧩 Združljivo z večino **3-faznih polnilnic (Modbus, OCPP, ABB Terra AC …)**

---

### 🧩 Zahtevane integracije

Za pravilno delovanje potrebuješ:

- [**ABB Terra AC Modbus Integration**](https://github.com/JernejHren/ABB-Terra-AC)  
  Omogoča nadzor polnilnice (vklop, izklop, nastavitev toka, spremljanje stanja).

Priporočeno (opcijsko):
- [**Home Assistant Network Tariff Integration**](https://github.com/frlequ/home-assistant-network-tariff)  
  Zagotavlja podatke o trenutnem tarifnem bloku (1–5), ki jih blueprint uporablja za omejevanje moči.

---

### ⚙️ Nastavitve blueprinta

| Parameter | Opis |
|------------|------|
| `export_sensor` | Senzor, ki meri trenutno porabo/oddajo hiše (W) |
| `charger_switch` | Stikalo za vklop/izklop polnilnice |
| `charger_current` | Entiteta za nastavitev toka polnjenja (A) |
| `charging_state` | Senzor stanja polnilnice (npr. *State A – Idle*) |
| `tariff_block` | Senzor trenutnega tarifnega bloka (1–5) |
| `block_limit_1–5` | Največja dovoljena moč (W) za posamezen tarifni blok |
| `buffer` | Rezerva moči (W) za histerezo pri povečevanju toka |
| `max_current` | Najvišji tok polnjenja (A) |
| `debug` | Če je vklopljen, beleži podrobne dogodke v sistemski dnevnik |

---

### ⚡ Primer delovanja

1. **Avto se priklopi** → avtomatizacija se zažene, a polnilnica **še ne vklopi**.  
2. Ko sistem zazna dovolj moči (`export_raw < block_limit – buffer`),  
   polnilnica se **vklopi pri 6 A**.  
3. Tok se nato **samodejno povečuje ali zmanjšuje** glede na porabo hiše.  
4. Če poraba preseže mejo, se tok **zmanjša na 0 A**, a seja ostane aktivna.  
5. Ko je spet dovolj moči, polnilnica **nadaljuje pri 6 A** in prilagaja tok navzgor.  
6. Ko polnilnica preide v stanje *Idle*, se avtomatizacija ustavi in polnilnica izklopi.

---

### 🧠 Tehnične opombe

- **Minimalni tok je vedno 6 A**, kar zagotavlja stabilen zagon in nadaljevanje.  
- **Buffer** se uporablja **le pri povečevanju toka**, da prepreči hitro ciklanje.  
- Avtomatizacija preverja pogoje vsakih 30 s (nastavljeno v `delay`).  
- Uporablja 3-fazni izračun: `690 W / A (≈ 230 V × 3)`.  
- Preizkušeno z **ABB Terra AC**, **SolarEdge**, **Fronius**, **Easee** in podobnimi polnilnicami.  
- Zasnovano in testirano v Sloveniji (SLO tarifni sistem + Modbus integracija).

---

## 🇬🇧 Description (English)

Blueprint for **safe, dynamic EV charging control** based on house power usage and active tariff block.

This “Safe Start” version ensures that charging starts only when there’s enough power for **6 A + buffer**, preventing charger errors and avoiding cycling behavior common with ABB Terra AC and similar stations.

### 🔋 Key Features
- Automation starts on plug-in (Idle → Active)
- Charger turns **on only when power ≥ 6 A + buffer**
- Charging current dynamically adapts to house consumption
- **Buffer (hysteresis)** used only when increasing current
- Supports **tariff blocks (1–5)** for power-limit scheduling
- If not enough power → current = 0 A (temporary pause)
- When power returns → charging resumes at 6 A
- Tested with **ABB Terra AC** and compatible 3-phase chargers

---

### 🧩 Required Integrations
- [**ABB Terra AC Modbus Integration**](https://github.com/JernejHren/ABB-Terra-AC)  
  Provides full control of charger (on/off, current, state).
- [**Home Assistant Network Tariff Integration**](https://github.com/frlequ/home-assistant-network-tariff) *(optional)*  
  Supplies current tariff block (1–5) for dynamic limits.

---

### ⚙️ Configuration Parameters

| Parameter | Description |
|------------|-------------|
| `export_sensor` | Sensor measuring house power usage/export (W) |
| `charger_switch` | Switch controlling charger power |
| `charger_current` | Number entity controlling charging current (A) |
| `charging_state` | Charger state sensor (e.g. “State A – Idle”) |
| `tariff_block` | Tariff block sensor (1–5) |
| `block_limit_1–5` | Max allowed power (W) per tariff block |
| `buffer` | Extra margin (W) for hysteresis on current increase |
| `max_current` | Max charging current (A) |
| `debug` | Enable detailed debug messages |

---

### 🧠 Technical Notes

- Minimum current always set to 6 A for safe start/resume  
- Power threshold based on 3-phase × 230 V (≈ 690 W/A)  
- Logic loops every 30 seconds  
- Buffer applies only to current increase  
- Stable operation tested with ABB Terra AC & SolarEdge

---

## 🧾 License

Released under the [MIT License](https://opensource.org/licenses/MIT).
