# ⚡ EV Load Management (Safe Start, House Power Based)

## 🇸🇮 Opis (Slovenščina)

Blueprint za **dinamično upravljanje polnjenja električnega vozila** glede na trenutno porabo hiše in aktivni tarifni blok.

Ta izboljšana različica (“Safe Start”) omogoča **varnejše delovanje** polnilnice in preprečuje napake, ki se lahko pojavijo pri ABB Terra AC in podobnih polnilnicah, če se polnjenje zažene z nastavitvijo toka 0 A.  
Namesto tega se avtomatizacija zažene ob priklopu vozila, vendar **počaka z začetkom polnjenja**, dokler ni na voljo dovolj energije.

### 🔋 Ključne funkcije
- ✅ Avtomatizacija se **zažene ob priklopu vozila**, vendar polnilnice ne vklopi takoj  
- ⚡ Polnilnica se **vklopi šele, ko je dovolj razpoložljive moči**  
- 🔁 Tok se **dinamično prilagaja** glede na trenutno porabo hiše  
- 🧠 **Buffer (histereza)** se upošteva le pri povečevanju toka — manj preklopov, bolj stabilno delovanje  
- 🕒 Upošteva **tarifne bloke** za omejevanje največje moči glede na čas dneva  
- 📴 Avtomatizacija se zaključi, ko polnilnica preide v stanje *State A - Idle*

---

### 🧩 Zahtevane integracije

Ta blueprint je napisan za uporabo z:
- [**ABB Terra AC Modbus Integration for Home Assistant**](https://github.com/JernejHren/ABB-Terra-AC)  
  Omogoča nadzor polnilnice ABB Terra AC (vklop/izklop, nastavitev toka, spremljanje stanja).

Priporočena dodatna integracija:
- [**Home Assistant Network Tariff Integration**](https://github.com/frlequ/home-assistant-network-tariff)  
  Zagotavlja podatke o trenutnem **časovnem bloku** (1–5), ki jih blueprint uporablja za omejevanje moči polnjenja.

---

### ⚙️ Nastavitve v blueprintu

| Parameter | Opis |
|------------|------|
| `export_sensor` | Senzor, ki meri trenutno porabo/oddajo hiše (npr. "House Power Sensor") |
| `charger_switch` | Stikalo za vklop/izklop polnilnice |
| `charger_current` | Entiteta za nastavitev toka polnjenja (A) |
| `charging_state` | Senzor stanja polnilnice (npr. "State A - Idle") |
| `tariff_block` | Senzor trenutnega tarifnega bloka (1–5) |
| `block_limit_1–5` | Največja dovoljena moč (W) za posamezen tarifni blok |
| `buffer` | Dodatna rezerva moči (W) za histerezo pri povečevanju toka |
| `min_current` | Najnižji tok polnjenja (A) |
| `max_current` | Najvišji tok polnjenja (A) |
| `debug` | Omogoča izpis dogodkov v sistemski dnevnik |

---

### ⚡ Primer delovanja
1. Avto se priklopi → avtomatizacija se **zažene**, a polnilnica **še ne vklopi**.  
2. Ko sistem zazna, da je na voljo dovolj moči (`export_raw < block_limit - buffer - 4200`),  
   se polnilnica **vklopi** in nastavi minimalni tok (npr. 6 A).  
3. Tok se nato **samodejno povečuje ali zmanjšuje** glede na porabo hiše.  
4. Če poraba preseže mejo, se tok zmanjša — vendar se polnilnica ne izklopi.  
5. Ko polnilnica preide v stanje *Idle*, se avtomatizacija ustavi in polnilnica izklopi.

---

### 🧠 Tehnične opombe
- Buffer se uporablja **samo pri povečevanju toka** (histereza).  
- Polnilnica se **ne vklopi, dokler ni dovolj moči**, kar preprečuje *Paused/Fault* stanje.  
- Avtomatizacija preverja pogoje vsakih 30 sekund.  
- Zasnovano in testirano v Sloveniji (SLO tarifni sistem + ABB Modbus integracija).  
- Združljivo tudi z drugimi Modbus/OCPP polnilnicami, ki podpirajo nadzor toka in stanja.

---

## 🇬🇧 Description (English)

Blueprint for **safe and dynamic EV charging control** based on house power usage and active tariff block.

This “Safe Start” version improves compatibility with ABB Terra AC and similar chargers that can enter fault mode if charging starts at 0 A.  
Instead of immediately starting charging when the EV is plugged in, the automation **waits until sufficient power is available** before turning the charger on.

### 🔋 Key Features
- Automation starts when EV is plugged in but **waits to start charging**
- Charger turns **ON only when enough power is available**
- Charging current **dynamically adapts** to total house load
- **Buffer (hysteresis)** applied only when increasing current
- Considers **tariff blocks (1–5)** for time-of-day limits
- Stops when charger returns to *State A – Idle*

---

### 🧩 Required Integrations
- [**ABB Terra AC Modbus Integration for Home Assistant**](https://github.com/JernejHren/ABB-Terra-AC)  
  Enables on/off control, current adjustment, and state feedback.  
- [**Home Assistant Network Tariff Integration**](https://github.com/frlequ/home-assistant-network-tariff) *(optional)*  
  Provides tariff block (1–5) information for block-based power limits.

---

### ⚙️ Configuration Parameters

| Parameter | Description |
|------------|-------------|
| `export_sensor` | Sensor measuring house power usage/export (W) |
| `charger_switch` | Switch entity controlling charger on/off |
| `charger_current` | Number entity setting charging current (A) |
| `charging_state` | Sensor showing charger state (e.g., “State A – Idle”) |
| `tariff_block` | Sensor providing current tariff block (1–5) |
| `block_limit_1–5` | Max power limit (W) per tariff block |
| `buffer` | Power buffer (W) for hysteresis when increasing current |
| `min_current` | Minimum charging current (A) |
| `max_current` | Maximum charging current (A) |
| `debug` | Enable debug messages in system log |

---

### ⚡ Operating Example
1. EV is plugged in → automation starts (charger **remains OFF**).  
2. Once sufficient power is available, charger turns **ON** at minimal current.  
3. Current dynamically adjusts based on real-time house load.  
4. If consumption exceeds the limit, current is reduced (charger stays ON).  
5. When charger returns to *Idle*, automation stops and turns it OFF.

---

### 🧠 Technical Notes
- Buffer only affects current **increase** (adds hysteresis).  
- Prevents *Paused/Fault* state by avoiding “Start at 0 A”.  
- 30 s check interval ensures smooth adaptation.  
- Optimized for **Slovenian tariff system** and **ABB Modbus chargers**.  
- Compatible with other chargers supporting current control and state feedback.

---

## 🧾 License
Released under the [MIT License](https://opensource.org/licenses/MIT).
