# ⚡ EV Load Management (Safe Start, House Power Based)

## 🇸🇮 Opis (Slovenščina)

Blueprint za **dinamično upravljanje polnjenja električnega vozila** glede na trenutno porabo hiše in aktivni tarifni blok.

Ta izboljšana različica (“Safe Start”) omogoča **varnejše delovanje** polnilnice in preprečuje napake, ki se lahko pojavijo pri **ABB Terra AC** in podobnih polnilnicah, če se polnjenje zažene z nastavitvijo toka 0 A.  
Namesto tega se avtomatizacija zažene ob priklopu vozila, vendar **počaka z začetkom polnjenja**, dokler ni na voljo dovolj energije za *dejansko polnjenje (6 A + buffer)*.

---

### 🔋 Ključne funkcije

- ✅ Avtomatizacija se **zažene ob priklopu vozila**, vendar polnilnice **ne vklopi takoj**  
- ⚡ Polnilnica se **vklopi šele, ko je na voljo dovolj razpoložljive moči** za 6 A + buffer  
- 🔁 Tok se **dinamično prilagaja** (6–max A) glede na trenutno porabo hiše  
- 🧠 **Buffer (histereza)** se upošteva le pri povečevanju toka — manj preklopov, bolj stabilno delovanje  
- 🕒 Upošteva **tarifne bloke** (1–5) za omejevanje največje moči glede na čas dneva  
- 📴 Avtomatizacija se **zaključi**, ko polnilnica preide v stanje *State A – Idle*  

---

### 🧩 Zahtevane integracije

Ta blueprint je namenjen uporabi z:
- [**ABB Terra AC Modbus Integration for Home Assistant**](https://github.com/JernejHren/ABB-Terra-AC)  
  Omogoča nadzor polnilnice ABB Terra AC (vklop/izklop, nastavitev toka, spremljanje stanja).

Priporočena dodatna integracija:
- [**Home Assistant Network Tariff Integration**](https://github.com/frlequ/home-assistant-network-tariff)  
  Zagotavlja podatke o trenutnem **časovnem bloku** (1–5), ki jih blueprint uporablja za dinamične omejitve moči.

---

### ⚙️ Nastavitve v blueprintu

| Parameter | Opis |
|------------|------|
| `export_sensor` | Senzor, ki meri trenutno porabo/oddajo hiše (W) |
| `charger_switch` | Stikalo za vklop/izklop polnilnice |
| `charger_current` | Entiteta za nastavitev toka polnjenja (A) |
| `charging_state` | Senzor stanja polnilnice (npr. “State A – Idle”) |
| `tariff_block` | Senzor trenutnega tarifnega bloka (1–5) |
| `block_limit_1–5` | Največja dovoljena moč (W) za posamezen blok |
| `buffer` | Rezervna moč (W), ki preprečuje nihanje toka |
| `max_current` | Največji tok polnjenja (A) |
| `debug` | Omogoča izpis dogodkov v sistemski dnevnik |

> ℹ️ *Minimalni tok polnjenja je vgrajen v blueprint in fiksno nastavljen na 6 A (ni potrebno ročno nastavljati).*

---

### ⚡ Primer delovanja

1. Avto se priklopi → avtomatizacija se **zažene**, a polnilnica **še ne vklopi**.  
2. Ko sistem zazna, da je na voljo dovolj moči za **6 A + buffer**, se polnilnica **vklopi** in začne polnjenje.  
3. Tok se nato **samodejno povečuje ali zmanjšuje** glede na trenutno porabo hiše.  
4. Če poraba preseže mejo, se tok zmanjša (lahko tudi na 0 A), vendar se polnilnica **ne izklopi**.  
5. Ko polnilnica preide v stanje *Idle*, se avtomatizacija ustavi in polnilnica se izklopi.

---

### 🧠 Tehnične opombe

- Buffer se uporablja **samo pri povečevanju toka** – zagotavlja stabilno delovanje brez preklapljanja.  
- Polnilnica se **ne vklopi**, dokler ni na voljo dovolj moči za realno polnjenje (6 A).  
- Avtomatizacija preverja pogoje vsakih **30 sekund**.  
- Preverjeno in optimizirano za **slovenski tarifni sistem** in **ABB Terra AC** (Modbus).  
- Združljivo tudi z drugimi polnilnicami (OCPP/Modbus), ki podpirajo **nadzor toka** in **stanje**.  

---

## 🇬🇧 Description (English)

Blueprint for **safe and dynamic EV charging control** based on real-time house power usage and active tariff block.

The “Safe Start” version improves compatibility with **ABB Terra AC** and similar chargers that may enter a fault state if charging starts at 0 A.  
Instead of immediately starting charging when the EV is plugged in, the automation **waits until sufficient power (6 A + buffer)** is available before turning the charger on.

---

### 🔋 Key Features

- Starts automatically when EV is plugged in, but **delays charging start**  
- Charger turns **ON only when there’s enough power** for 6 A + buffer  
- Charging current **dynamically adapts** (6–max A) to house consumption  
- **Buffer (hysteresis)** only applies when increasing current — stable, smooth behavior  
- Considers **tariff blocks (1–5)** to limit power by time of day  
- Stops when charger returns to *State A – Idle*

---

### 🧩 Required Integrations

- [**ABB Terra AC Modbus Integration for Home Assistant**](https://github.com/JernejHren/ABB-Terra-AC)  
  Enables on/off control, current adjustment, and state monitoring.  
- [**Home Assistant Network Tariff Integration**](https://github.com/frlequ/home-assistant-network-tariff) *(optional)*  
  Provides current tariff block (1–5) information for power limit adjustment.

---

### ⚙️ Configuration Parameters

| Parameter | Description |
|------------|-------------|
| `export_sensor` | Sensor measuring house power usage/export (W) |
| `charger_switch` | Switch entity controlling charger power |
| `charger_current` | Number entity setting charging current (A) |
| `charging_state` | Sensor indicating charger state (e.g., “State A – Idle”) |
| `tariff_block` | Sensor providing current tariff block (1–5) |
| `block_limit_1–5` | Maximum power limit (W) per tariff block |
| `buffer` | Power buffer (W) for hysteresis when increasing current |
| `max_current` | Maximum charging current (A) |
| `debug` | Enables detailed logging in Home Assistant system log |

> 💡 *Minimum current (6 A) is defined internally and cannot be changed — ensures safe startup.*

---

### ⚡ Operating Example

1. EV is plugged in → automation starts, charger stays **OFF**.  
2. When there’s enough available power for **6 A + buffer**, charger turns **ON**.  
3. Current **adjusts automatically** to real-time house load.  
4. If consumption rises above the limit, current decreases (possibly to 0 A), but charger remains ON.  
5. When charger returns to *Idle*, automation stops and turns it OFF.

---

### 🧠 Technical Notes

- Buffer adds **hysteresis only for current increase**, preventing flickering.  
- Prevents *Paused/Fault* states by avoiding 0 A startup.  
- 30-second control loop for smooth behavior.  
- Optimized for **Slovenian grid** and **ABB Modbus chargers**.  
- Compatible with other OCPP/Modbus chargers supporting current control and state feedback.

---

## 🧾 License

Released under the [MIT License](https://opensource.org/licenses/MIT).
