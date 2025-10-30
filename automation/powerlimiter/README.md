# Predvidljivi Power Limiter / Predictable Power Limiter

OPOZORILO še vedno v fazi razvoja !!
WARNING still under development!!

## 🇸🇮 Slovensko

### Namen
Ta blueprint je zasnovan za uporabnike v Sloveniji, ki želijo **zmanjšati špice v električni porabi** v **tarifnih blokih 1 in 2**.  
Avtomatika spremlja **projekcijo porabe moči (15-minutna)** in po potrebi **izklopi prednostno določene naprave**, da ne presežejo nastavljene meje. Po padcu porabe naprave ponovno vklopi, vendar le, če so bile prej izklopljene in je minil **minimalni čas izklopa** (privzeto 15 minut), s čimer preprečimo hitro izklapljanje in vklapljanje (flapping).

### Zahtevana integracija
Za pravilno delovanje je potrebna integracija **[frlequ / home-assistant-network-tariff](https://github.com/frlequ/home-assistant-network-tariff)**, ki zagotavlja senzor tarife (`tariff_sensor`) v Home Assistantu.

### Ključne funkcionalnosti
- Izklop **samo v tarifah 1 in 2**, ko projekcija preseže mejo + prag.
- Vklop **samo naprav, ki jih je avtomatika izklopila** in so bile izklopljene vsaj `min_off_time` minut.
- Podpora za do **5 naprav**: switch, climate, water_heater.
- Shramba izklopljenih naprav v **enem `input_text` helperju**.
- Nastavljivi parametri:
  - `tariff_1_limit` in `tariff_2_limit` (W)
  - `projection_threshold` (W nad mejo)
  - `hysteresis` (W pod mejo za vklop)
  - `min_off_time` (minute)
  
### Uporaba
1. Namesti integracijo **frlequ / home-assistant-network-tariff** in preveri, da senzor tarife (`tariff_sensor`) deluje.
2. Ustvari `input_text` helper za shranjevanje izklopljenih naprav.
3. Importaj ta blueprint v Home Assistant.
4. Ustvari avtomatizacijo iz blueprinta in izberi:
   - Senzor tarife (`tariff_sensor`)
   - Projekcija porabe (`projected_power_sensor`)
   - `input_text` helper za shranjevanje izklopljenih naprav
   - Do 5 naprav (lahko pustiš prazne)
5. Nastavi meje in parametre po želji.

### Opombe
- Naprave se izklapljajo **po vrstnem redu 1 → 5**.
- Minimalni čas izklopa preprečuje, da bi naprave hitreje kot `min_off_time` minut vklopilo nazaj, tudi če projekcija pade pod mejo.
- Blueprint je zasnovan za **stabilno upravljanje porabe** brez potrebe po dodatnih senzorjih ali konfiguraciji YAML.

---

## 🇬🇧 English

### Purpose
This blueprint is designed for users in Slovenia who want to **reduce electricity peaks** in **tariff blocks 1 and 2**.  
The automation monitors the **15-minute projected power consumption** and, if necessary, **turns off priority devices** to avoid exceeding the set limit. Devices are turned back on once consumption drops, but **only those previously turned off and after the minimum off‑time** (default 15 minutes), preventing rapid on/off flapping.

### Required Integration
This blueprint requires the **[frlequ / home-assistant-network-tariff](https://github.com/frlequ/home-assistant-network-tariff)** integration, which provides the tariff sensor (`tariff_sensor`) in Home Assistant.

### Key Features
- Turn-off **only in tariff blocks 1 and 2**, when projected power exceeds limit + threshold.
- Turn-on **only devices previously turned off by automation** and after `min_off_time` minutes.
- Supports up to **5 devices**: switch, climate, water_heater.
- Stores off devices in **a single `input_text` helper**.
- Configurable parameters:
  - `tariff_1_limit` and `tariff_2_limit` (W)
  - `projection_threshold` (W above limit)
  - `hysteresis` (W below limit for turn-on)
  - `min_off_time` (minutes)

### Usage
1. Install the **frlequ / home-assistant-network-tariff** integration and ensure the tariff sensor (`tariff_sensor`) is available.
2. Create an `input_text` helper to store the turned-off devices.
3. Import this blueprint into Home Assistant.
4. Create an automation from the blueprint and select:
   - Tariff sensor (`tariff_sensor`)
   - Projected power sensor (`projected_power_sensor`)
   - `input_text` helper for storing turned-off devices
   - Up to 5 devices (can leave some empty)
5. Set your limits and parameters as desired.

### Notes
- Devices are turned off in **priority order 1 → 5**.
- Minimum off‑time prevents devices from turning back on faster than `min_off_time` minutes, even if projected power drops below the limit.
- Blueprint is designed for **stable consumption management** without additional sensors or YAML configuration.
