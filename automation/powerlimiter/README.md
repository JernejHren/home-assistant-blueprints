# ⚡ Predvidljivi Power Limiter (VAREN – uporabnik ima prednost)
**Predictable Power Limiter (SAFE – user preference first)**

> ⚠️ **OPOZORILO:** Še vedno v fazi razvoja!  
> ⚠️ **WARNING:** Still under development!

---

## 🇸🇮 Slovensko

### 🎯 Namen
Blueprint je zasnovan za uporabnike v Sloveniji, ki želijo **zmanjšati špice v električni porabi** znotraj **tarifnih blokov 1 in 2**.  
Avtomatika spremlja **projekcijo 15-minutne moči** in po potrebi **izklopi prednostno določene naprave**, da prepreči preseganje meje.  
Ko poraba pade, naprave ponovno vklopi — vendar **samo tiste, ki jih je sama izklopila** in šele po preteku **minimalnega časa izklopa** (privzeto 15 minut).  
Ročno izklopljene naprave ostanejo izklopljene.

---

### ⚙️ Zahtevana integracija
- **[frlequ / home-assistant-network-tariff](https://github.com/frlequ/home-assistant-network-tariff)**  
  Ta integracija ustvari senzor **`tariff_sensor`**, ki označuje trenutno tarifo (1, 2, ali 3).  

---

### 🔑 Ključne funkcionalnosti
- Izklop **samo v tarifah 1 in 2**, ko projekcija preseže mejo + prag (`projection_threshold`).
- Vklop **samo naprav, ki jih je avtomatika izklopila** in so izklopljene vsaj `min_off_time` minut.
- Ročno izklopljene naprave **niso prisilno vklopljene**.
- Shrani seznam izklopljenih naprav v **en `input_text` helper** (v JSON obliki).
- Pošilja **obvestila** na do 2 napravi (npr. `notify.mobile_app_pixel_8a_2`).
- **Stabilizacija**: prvih X sekund po začetku 15-min intervala se projekcija ignorira.
- Podprti tipi naprav: `switch`, `climate`, `water_heater`.
- Naprave se izklapljajo po vrstnem redu **1 → 5** (od najmanj do najbolj kritične).

---

### 🔧 Parametri
| Parameter | Opis | Privzeto |
|------------|-------|-----------|
| `tariff_1_limit` | Meja moči v tarifi 1 (W) | 7000 |
| `tariff_2_limit` | Meja moči v tarifi 2 (W) | 9000 |
| `projection_threshold` | Dovoljen presežek nad mejo za izklop (W) | 500 |
| `hysteresis` | Histerezija – koliko pod mejo mora pasti projekcija za ponovni vklop (W) | 500 |
| `min_off_time` | Minimalni čas izklopa pred ponovnim vklopom (minute) | 15 |
| `projection_stabilization_delay` | Čas ignoriranja projekcije po začetku 15-min intervala (sekunde) | 60 |

---

### 🪜 Uporaba
1. Namesti integracijo **frlequ / home-assistant-network-tariff** in preveri delovanje `tariff_sensor`.
2. Ustvari `input_text` helper (npr. `input_text.power_limiter_off_devices`) za shranjevanje izklopljenih naprav.
3. Importaj ta blueprint v Home Assistant:
   - Shrani YAML datoteko v `config/blueprints/automation/<tvoje_ime>/predvidljivi_power_limiter.yaml`
   - Nato pojdi v **Nastavitve → Avtomatizacije → Blueprints** in osveži seznam.
4. Ustvari novo avtomatizacijo iz blueprinta:
   - Izberi **senzor tarife** (`tariff_sensor`)
   - Izberi **projekcijo moči** (`projected_power_sensor`)
   - Določi `input_text` helper
   - Dodaj do **5 naprav** (po vrstnem redu pomembnosti)
   - Vnesi notify storitev (npr. `notify.mobile_app_pixel_8a_2`) za obvestila
5. Nastavi meje in druge parametre po želji.
6. Shrani in aktiviraj avtomatizacijo.

---

### 💡 Opombe
- Če je poraba pod mejo minus histereza, se izklopljene naprave postopno ponovno vključijo.  
- Minimalni čas izklopa (`min_off_time`) zagotavlja stabilno delovanje brez utripanja.
- Uporabnik ima **vedno prednost** — če napravo ročno izklopi, avtomatika je ne bo vklopila nazaj.
- Deluje v **varnem načinu**, primeren tudi za ogrevalne sisteme in bojlerje.

---

## 🇬🇧 English

### 🎯 Purpose
This blueprint helps users in Slovenia manage **electricity peak consumption** in **tariff blocks 1 and 2**.  
It monitors **15-minute projected power** and safely **turns off selected devices** to prevent exceeding the configured limit.  
Once power drops, devices are turned back on — but **only those previously switched off by automation**, and only after the **minimum off-time** (default 15 min).  
Manually switched-off devices remain off.

---

### ⚙️ Required Integration
- **[frlequ / home-assistant-network-tariff](https://github.com/frlequ/home-assistant-network-tariff)**  
  Provides the `tariff_sensor` for active tariff level.

---

### 🔑 Key Features
- Turns off **only during tariffs 1 and 2**, when projected power exceeds limit + threshold.  
- Turns on **only devices that were off by automation** and after `min_off_time` minutes.  
- Manually turned-off devices stay off.  
- Stores off-device list in a single `input_text` helper (JSON).  
- Sends **notifications** to up to 2 devices (`notify.mobile_app_*`).  
- Supports **stabilization delay** — ignores projection during the first seconds of each 15-min interval.  
- Supports `switch`, `climate`, and `water_heater` domains.  
- Devices are controlled in **priority order 1 → 5**.

---

### ⚙️ Parameters
| Parameter | Description | Default |
|------------|-------------|----------|
| `tariff_1_limit` | Limit for tariff 1 (W) | 7000 |
| `tariff_2_limit` | Limit for tariff 2 (W) | 9000 |
| `projection_threshold` | Extra watts allowed above limit before turning off | 500 |
| `hysteresis` | Margin below limit before turning back on | 500 |
| `min_off_time` | Minimum off-time before re-enable (minutes) | 15 |
| `projection_stabilization_delay` | Delay to ignore projections after interval start (seconds) | 60 |

---

### 🪜 Usage
1. Install **frlequ / home-assistant-network-tariff** and verify `tariff_sensor`.  
2. Create an `input_text` helper (e.g., `input_text.power_limiter_off_devices`).  
3. Import this blueprint into Home Assistant:  
   - Save the YAML under `config/blueprints/automation/<your_name>/predvidljivi_power_limiter.yaml`  
   - Reload Blueprints in HA.  
4. Create automation from the blueprint:  
   - Select tariff and projected power sensors  
   - Choose the input_text helper  
   - Add up to 5 devices (priority order)  
   - Set notify service(s) for phone alerts  
5. Adjust thresholds and limits.  
6. Save and activate.

---

### 🧠 Notes
- Devices are restored only after both: projected power < (limit – hysteresis) **and** `min_off_time` elapsed.  
- Manually toggled devices are never overridden.  
- Designed for **safe and stable load control** without custom YAML.  
- Ideal for heating, boilers, or EV chargers where user override is essential.

