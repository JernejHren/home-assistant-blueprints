# ⚡ EV Load Management (House Power Based)

## 🇸🇮 Opis (Slovenščina)

Blueprint za **dinamično upravljanje polnjenja električnega vozila** glede na trenutno porabo hiše in aktivni tarifni blok.

Ta avtomatizacija omogoča, da se polnjenje **samodejno prilagaja** glede na razpoložljivo moč, ki jo določa trenutni blok omrežne tarife (npr. blok 1–5) in trenutna hišna poraba.  
Ko ni dovolj moči, se polnjenje ne izklopi, ampak se **tok polnjenja nastavi na 0 A**, kar omogoča, da avto ostane pripravljen brez ponovne avtorizacije.  
Ko se sprosti moč, se polnjenje nadaljuje samodejno.

### 🔋 Ključne funkcije
- ✅ Samodejno **vklopi polnjenje ob priklopu vozila**
- ⚡ Če ni dovolj moči, **nastavi tok na 0 A** (vozilo čaka)
- 🔁 Dinamično prilagajanje toka na podlagi porabe hiše
- 🧠 **Buffer (histereza)** se upošteva le pri povečevanju toka — manj preklopov, bolj stabilno delovanje
- 🕒 Upošteva **tarifne bloke** za omejevanje največje moči glede na čas dneva
- 📴 Avtomatizacija se zaključi, ko polnilnica preide v stanje *State A - Idle*

---

### 🧩 Zahtevane integracije

Ta blueprint je napisan za uporabo z:
- [**ABB Terra AC Modbus Integration for Home Assistant**](https://github.com/JernejHren/ABB-Terra-AC)  
  Integracija omogoča nadzor polnilnice ABB Terra AC (vklop/izklop, nastavitev toka, spremljanje stanja).

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
| `block_limit_1–5` | Določitev največje moči (W) za posamezen tarifni blok |
| `buffer` | Dodatna rezerva moči (W) za histerezo pri povečevanju toka |
| `min_current` | Najnižji tok polnjenja (A) |
| `max_current` | Najvišji tok polnjenja (A) |
| `debug` | Omogoča izpis dogodkov v sistemski dnevnik |

---

### ⚡ Primer delovanja
1. Avto se priklopi → polnilnica se **vklopi**.  
2. Če trenutna hišna poraba presega dovoljeno mejo, se **tok nastavi na 0 A**.  
3. Avto ostane pripravljen – ni ponovne avtorizacije.  
4. Ko se poraba zmanjša ali blok spremeni, polnjenje **samodejno začne**.  
5. Tok se **povečuje z bufferjem**, da se prepreči preveč pogoste spremembe.  
6. Ko se polnilnica vrne v stanje *Idle*, se avtomatizacija ustavi in stikalo izklopi.

---

### 🧠 Tehnične opombe
- Buffer se uporablja **samo pri povečevanju toka** (dodaja histerezo).  
- Polnilnica ABB Terra AC podpira stanje “ON + 0 A” – avto ostane pripravljen brez porabe energije.  
- Avtomatizacija uporablja interval 30 s za preverjanje pogojev.  
- Zasnovano in testirano v Sloveniji (SLO tarifni sistem in ABB Modbus integracija).

---

## 🇬🇧 Description (English)

Blueprint for **dynamic EV charging control** based on house power usage and active network tariff block.

The automation continuously **adapts the EV charging current** according to available house power and time-of-day tariff limits.  
If not enough power is available, the charger remains **enabled at 0 A**, keeping the EV session active and ready to resume charging automatically once conditions allow it.

---

### 🔋 Key Features
- Automatically **activates charging when the EV is plugged in**
- If insufficient power, **sets current to 0 A** (car waits)
- Dynamically adjusts current based on total house power
- Uses **buffer (hysteresis)** only for increasing current — avoids oscillations
- Considers **tariff blocks (1–5)** to limit charging during high-tariff periods
- Stops when charger returns to *State A - Idle*

---

### 🧩 Required Integrations

Designed for use with:
- [**ABB Terra AC Modbus Integration for Home Assistant**](https://github.com/JernejHren/ABB-Terra-AC)  
  Enables full control of ABB Terra AC chargers via Modbus (on/off, current control, status).

Recommended integration:
- [**Home Assistant Network Tariff Integration**](https://github.com/frlequ/home-assistant-network-tariff)  
  Provides the current **tariff block (1–5)** used to dynamically set the allowed power limit.

---

### ⚙️ Configuration Parameters

| Parameter | Description |
|------------|-------------|
| `export_sensor` | Sensor measuring house power export/usage (W) |
| `charger_switch` | Switch to enable/disable the charger |
| `charger_current` | Number entity for setting charging current (A) |
| `charging_state` | Sensor showing charger state (e.g., "State A - Idle") |
| `tariff_block` | Sensor providing current tariff block (1–5) |
| `block_limit_1–5` | Maximum allowed power (W) for each tariff block |
| `buffer` | Power buffer (W) used as hysteresis for current increase |
| `min_current` | Minimum charging current (A) |
| `max_current` | Maximum charging current (A) |
| `debug` | Enable debug logs in Home Assistant system log |

---

### ⚡ Operating Example
1. EV is plugged in → charger turns **ON**.  
2. If insufficient power is available, **current is set to 0 A**.  
3. Car remains ready – no re-authorization required.  
4. When power becomes available, charging **starts automatically**.  
5. Charging current **increases gradually** with buffer-based hysteresis.  
6. When charger returns to *Idle*, automation turns charger **OFF**.

---

### 🧠 Technical Notes
- Buffer is only applied when **increasing current** (adds hysteresis).  
- ABB Terra AC supports “ON + 0 A” safely – no battery or charger stress.  
- Automation runs checks every 30 seconds.  
- Optimized for the **Slovenian network tariff system** and ABB Modbus-based chargers.

---

## 🧾 License
Released under the [MIT License](https://opensource.org/licenses/MIT).


