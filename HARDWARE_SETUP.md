# MiSTer FPGA — Hardware Setup Guide

Complete reference for everything needed beyond the **DE10-Nano** board. Required items
first, optional add-ons after. Verified against the official MiSTer FPGA documentation at
https://mister-devel.github.io/MkDocs_MiSTer/

> **Assumption:** You already own the **Terasic DE10-Nano** (Intel Cyclone V SE 5CSEBA6U23I7,
> 110K LEs, dual-core ARM Cortex-A9 @ 800 MHz, 1GB DDR3, HDMI, Ethernet).

---

## Master Checklist

### 🔴 Required — Without these, MiSTer will not run

| # | Item | Official docs classification |
|---|------|------------------------------|
| 1 | microSD card (minimum 4GB) + reader | Required |
| 2 | USB keyboard | Required |
| 3 | HDMI display + HDMI cable | Required |
| 4 | USB OTG adapter **or** USB Hub Add-On Board | Required ("no USB devices will work without this") |
| 5 | 128MB SDRAM Add-On Board | "Optional, but many cores require it" — effectively required |
| 6 | Heatsink (20–22mm, ≤10mm tall) | Docs: "cooling for the FPGA" is recommended; community treats as mandatory |
| 7 | 5V / 4A+ barrel-jack power supply (5.5mm/2.1mm, center positive) | Required (stock 2A supply is underpowered for full stack) |

### 🟠 Official Optional Add-On Boards (from official docs)

| Board | What it adds |
|-------|-------------|
| USB Hub Add-On Board | 7× USB 2.0 ports, power-only rear port; daughter board that mounts under DE10-Nano |
| Digital I/O Add-On Board | Buttons, LEDs, secondary microSD, optical audio (TOSLINK), User IO port, fan header |
| Analog I/O Add-On Board | Everything in Digital IO + VGA for CRT, barrel-jack power input, 3.5mm audio in |
| A/V Pro I/O Board (v9.2) | Premium combo: 24-bit ADV7125 DAC, simultaneous HDMI + VGA, Hi-Fi audio DAC |
| MT32-Pi Lite Add-On Board | Roland MT-32 synth emulation via Raspberry Pi Zero 2 W; plugs into User IO port |
| MT32-Pi Raspberry Pi HAT | Same MT-32 function via RPi 3A+/3B/4 + USB-A 3.0 cable |
| Real Time Clock (RTC) Board | Offline timekeeping via CR1220 coin cell; LTC connector on DE10-Nano |
| ADC Analog Audio Input Board | Tape/audio line-in (3.5mm) for computer cores that load from cassette |

### 🟡 Accessories & Peripherals

| Item | Notes |
|------|-------|
| Case / enclosure | Protects the stacked boards |
| USB gamepad | Any USB HID device; docs: "recommended" |
| Wi-Fi USB dongle | For OTA updates, ROM transfer, NTP sync |
| Bluetooth USB dongle | Wireless controller support |
| SNAC / SNAX adapter | Original console controllers at near-zero latency |
| VGA-to-SCART cable | For CRT TV output (requires 470Ω resistor on sync line) |
| USB mass storage | External drive for large ROM libraries |
| Internet connection | For updates and NTP; docs list as "recommended" |

---

## 1. microSD Card (Required)

The MiSTer OS, all cores, and optionally ROMs live on the microSD card.

**Size:** 64GB is comfortable for most setups; 128GB if using Amiga or DOS cores heavily;
256GB for large ao486 (DOS) software libraries. The DE10-Nano ships with an 8GB card —
too small for real use.

**Speed class:** Does not matter. The DE10-Nano's SD interface is the bottleneck, not the
card. Any Class 10 / UHS-I card saturates it.

**Recommended brands (reliability over speed):**
- Samsung PRO Endurance — top community pick for longevity
- SanDisk MAX ENDURANCE — excellent alternative
- Samsung EVO Plus / SanDisk Ultra — fine for most users

**Avoid:** Off-brand cards and suspicious Amazon sellers (fake capacities are common).

**Initial setup:** Flash with the [Mr. Fusion image](https://github.com/MiSTer-devel/mr-fusion)
— it auto-expands the partition on first boot.

---

## 2. USB OTG Adapter or USB Hub Board (Required)

The DE10-Nano has a single Micro-USB OTG port. You need either:

- A **Micro-USB OTG adapter** (cheap, ~$2–5) — allows plugging in a single USB hub or
  device temporarily. Fine for initial setup.
- A **USB Hub Add-On Board** — the proper solution (see §5 below).

---

## 3. USB Keyboard (Required)

Any standard USB keyboard works. Avoid gaming keyboards with extensive macro/anti-ghosting
features — they generate excessive input events that can interfere with MiSTer's input
handling.

---

## 4. HDMI Display + Cable (Required)

The bare DE10-Nano has one HDMI port. Any modern TV or monitor + any HDMI 1.4+ cable
works out of the box. No special cable required.

For CRT output, see §11 (Display & Video).

---

## 5. Power Supply (Required)

**Specs required:**
- Connector: **5.5mm outer / 2.1mm inner barrel jack, center positive**
- Voltage: **5V DC**
- Current: **4–5A minimum** for a full stack (DE10-Nano + IO board + USB hub + peripherals)

The stock 2A Terasic supply is underpowered for a full build. The IO board's barrel jack
powers both the IO board and USB hub via an included DC jumper cable — one PSU for
everything.

> ⚠️ Do not use phone chargers, laptop bricks with adapters, or USB-C adapters without a
> proper barrel-jack output. The DE10-Nano uses barrel jack exclusively (unless you have
> the IO Analog Pro which adds USB-C input).

| Product | Price | Retailer |
|---------|-------|---------|
| MiSTer FPGA Power Supply 5V 4A | £22.99 | [misterfpga.co.uk](https://misterfpga.co.uk/product/mister-fpga-power-supply/) |
| MiSTer FPGA 5V 5A DC PSU | $17.50 | [misteraddons.com](https://misteraddons.com/products/5v-5a-dc-power-supply) |
| **Mean Well GST25A05-P1J** (5V/5A) | ~$15–20 | Mouser / Digi-Key / Amazon |

The **Mean Well GST25A05-P1J** is the community favorite — regulated, reliable, correct
barrel connector. Available globally.

---

## 6. 128MB SDRAM Add-On Board (Effectively Required)

**Why it's needed:** The DE10-Nano's DDR3 RAM has variable latency, disqualifying it from
cycle-accurate cores. The SDRAM daughter board plugs into the GPIO0 header and provides
synchronous, low-latency memory.

**Without SDRAM you cannot run:** SNES, NES, PlayStation, Game Boy / GBA, Neo Geo, Amiga
(Minimig), Sega Mega CD, Atari ST, MSX, CPS1/CPS2, and most arcade cores.

**32MB vs 128MB:** Always buy 128MB. The 32MB fails on ~15% of the Neo Geo library
(Metal Slug 3, Garou), Mega CD, Acorn Archimedes, and some GBA titles. Price difference
is small.

**Form factor:** Buy a **vertical (top-mounted)** board (XS-DS v3.0 style) — it stands
perpendicular to the DE10-Nano, doesn't widen the footprint, allows airflow over the
FPGA chip, and fits inside most cases.

> ⚠️ Support the DE10-Nano from below when inserting or removing the SDRAM — the GPIO
> header is tight and the board can flex and crack if forced.

| Product | Price | Retailer |
|---------|-------|---------|
| **MiSTer SDRAM XS-DS v3.0 128MB** | £46.99 | [misterfpga.co.uk](https://misterfpga.co.uk/product/mister-sdram-128mb-module/) |
| MiSTer FPGA 128MB SDRAM XS-D V3.0 | $50 | [misteraddons.com](https://misteraddons.com/products/sdram) *(often sold out)* |
| Generic 128MB SDRAM v3.0 clone | ~$25–35 | Amazon / eBay |

---

## 7. Heatsink (Required for Stable Operation)

**Why:** The Cyclone V FPGA + ARM SoC runs hot. Without cooling, chip temperatures rise
until timing-sensitive cores become unstable — the FPGA logic runs ~10–15% slower at
100°C vs 0°C, causing errors exactly where timing margins are tightest.

**Cores that crash without cooling:**
- **ao486** (DOS/PC) — overheats fastest; a heatsink is the #1 fix for ao486 crashes
- **Amiga Minimig** — timing-sensitive
- **Battle Bakraid** (arcade) — crashes within minutes without active cooling

**Sizing constraints:**
- Footprint: **22×22mm** ideal; **20×20mm** is most commonly available
- Height: **maximum 10mm** if an IO board is stacked above (taller shorts against
  the IO board's underside)
- Thermal interface: use **3M 8810 thermal adhesive tape** (pre-applied on retail boards),
  not thermal paste (hard to remove). If sourcing generic, use a 0.5mm thermal pad.

| Product | Price | Retailer | Notes |
|---------|-------|---------|-------|
| Aluminium heatsink DE10-Nano | £3.29 | [misterfpga.co.uk](https://misterfpga.co.uk/product/mister-heatsink-de10-nano-cooling/) | 3M 8810 pre-applied; entry-level |
| Solid Copper Heatsink | £9.99 | [misterfpga.co.uk](https://misterfpga.co.uk/product/mister-fpga-ultimate-heatsink/) | ~2× conductivity of aluminium; best passive |
| Fan + Heatsink | £8.49 | [misterfpga.co.uk](https://misterfpga.co.uk/product/mister-fpga-fan-and-heatsink/) | 40mm fan @ 22 dB(A); no IO board needed |
| Fan Plate + Heatsink | £11.99 | [misterfpga.co.uk](https://misterfpga.co.uk/product/mister-fan-plate-and-heatsink/) | Fan on acrylic plate; screws to DE10-Nano |
| Heatsink (ultimatemister) | varies | [ultimatemister.com](https://ultimatemister.com/product/mister-de10nano-heatsink/) | |

> **Simplest path:** The **Aluminum Armor case** (§9) contacts the FPGA chip directly
> and acts as the heatsink — no separate heatsink needed.

The fan can connect to GPIO0/GPIO1 pin 11 (5V) / pin 12 (GND), or the IO board's fan
header (preferred — allows software fan speed control).

---

## 8. USB Hub Add-On Board (Optional — Strongly Recommended)

Sits **below** the DE10-Nano in the triple stack. Connects via a right-angled Micro-USB
bracket connector into the DE10-Nano's OTG port. Provides 7× USB 2.0 data ports + 1
power-only port. Kit includes brass standoffs and power splitter cable.

**Stack order (bottom to top):**
```
[TOP]    IO Board
         DE10-Nano
[BOTTOM] USB Hub
```

**Standard vs BlisSTer:**
- **USB Hub v2.1** — 7 USB ports, covers keyboard + 2 gamepads + Wi-Fi + BT
- **BlisSTer Rev.3** — 5 USB ports + 2 LLAPI low-latency ports for Bliss-Box original-
  console-controller adapters; worth it if you use original hardware controllers

| Product | Price | Retailer |
|---------|-------|---------|
| USB Hub v2.1 For MiSTer | £28.99 | [misterfpga.co.uk](https://misterfpga.co.uk/product/usb-hub-for-mister-fpga/) |
| MiSTer FPGA USB Hub + Bracket | $50 | [misteraddons.com](https://misteraddons.com/products/usb-hub) |
| BlisSTer Hub Rev.3 | £58.99 | [misterfpga.co.uk](https://misterfpga.co.uk/product/blisster-v3/) |
| Ultimate BlisSTer + Case bundle | €99.90 | [ultimatemister.com](https://ultimatemister.com/product/ultimate-blisster-hub-case/) |

---

## 9. Cases & Enclosures (Optional — Strongly Recommended)

Without a case the stacked boards are fragile and exposed. All mounting hardware
(standoffs, screws) is included in case kits.

**IO board installs on top via the Arduino 2×20 GPIO headers. USB hub installs below via
Micro-USB bracket. The three boards form a self-contained stack.**

### Aluminum Passively Cooled Armor — $75 USD
[misteraddons.com](https://misteraddons.com/products/aluminum-passively-cooled-case)
- Industrial aluminum with external heatsink fins; the case *is* the heatsink
- Tested: 40–50°C exterior; FPGA stays well under 100°C max
- No separate heatsink or fan needed
- Fits full triple stack; IO board buttons must be ~4.25mm height
- Remove any separate heatsink before installing (case contacts chip directly)

### Acrylic Case — £22.99
[misterfpga.co.uk](https://misterfpga.co.uk/product/mister-case-acrylic/)
- Laser-cut acrylic, 6 color options (Crystal Clear, Neptune Blue, Shadow Grey, etc.)
- Ventilation slots align with fan; LEDs visible through sides
- Requires separate heatsink + fan for heavy cores (ao486/Minimig)
- Includes brass standoffs + silicone anti-slip feet

### Official Ultimate MiSTer Case + Hub 2.1 + ADC — €59.90
[ultimatemister.com](https://ultimatemister.com/product/official-mister-case/)
- Bundles: case + USB Hub v2.1 + ADC audio input board
- Available in Black, White, Blue, Black & White
- IO board sold separately; portion of sale donated to MiSTer development

### Amazon Budget Metal Case — ~$45 USD
[Amazon](https://www.amazon.com/Mister-Metal-DE10-Nano-Board-Stack/dp/B0C5V1DLR1)
- Black metal; fits full triple stack; basic but functional

### ATLANTIS Carrier Board
[battyetech.com](https://battyetech.com/) — AUD $15–49
- Mounts DE10-Nano inside any standard ATX / Mini-ITX PC case with ATX power

### 3D Printed / Rack Mount
- [1U 10-inch rack mount](https://www.printables.com/model/1374561-mister-fpga-and-pi-1u-10-inch-rack-mount) — Printables; fits full stack in 1U
- Extensive library on Printables / Thingiverse for custom builds

---

## 10. IO Boards (Optional — Add VGA/Audio/Buttons)

IO boards stack **on top** of the DE10-Nano and provide physical controls, audio out, and
(for analog boards) VGA video for CRT gaming. Without an IO board, you have HDMI only.

### Comparison

| Feature | Digital IO | Analog IO | A/V Pro v9.2 | IO Direct |
|---------|-----------|-----------|-------------|-----------|
| Physical buttons (OSD/Reset/User) | ✅ | ✅ | ✅ | ✅ |
| Status LEDs | ✅ | ✅ | ✅ | ✅ |
| Secondary microSD slot | ✅ | ✅ | ✅ | ✅ |
| Optical audio (TOSLINK) | ✅ | ✅ | ✅ | ✅ |
| User IO port (SNAC/MT32-Pi) | ✅ | ✅ | ✅ | ✅ |
| Fan header | ✅ | ✅ | ✅ | ✅ |
| VGA / analog video out | ❌ | ✅ 18-bit | ✅ 24-bit ADV7125 | ✅ Saturn AV port |
| Simultaneous HDMI + analog | ✅ N/A | ✅ | ✅ | ❌ |
| RGB, S-Video, Composite native | ❌ | ❌ | ❌ | ✅ |
| Barrel-jack power input | ❌ | ✅ | ✅ | ✅ USB-C |
| Dual SDRAM compatible | ✅ | ❌ | ❌ | ✅ |
| Approx price | ~£27 | ~£35–45 | £42–58 / €50 | $65 |

**Which to choose:**
- HDMI only, want dual SDRAM later → **Digital IO**
- CRT via VGA + simultaneous HDMI → **A/V Pro v9.2** (best all-rounder)
- Native RGB/S-Video/Composite + dual SDRAM → **IO Direct**

### Products

| Product | Price | Retailer |
|---------|-------|---------|
| A/V Pro v9.2 (inc. Noctua fan option) | £41.99–£57.99 | [misterfpga.co.uk](https://misterfpga.co.uk/product/mister-av-pro-io-board/) |
| A/V Pro v9.2 | €49.90 | [ultimatemister.com](https://ultimatemister.com/product/mister-av-pro/) |
| IO Analog Pro (MiSTer Addons) | $65 | [misteraddons.com](https://misteraddons.com/products/mister-fpga-io-analog-pro) |
| IO Direct (MiSTer Addons) | $65 | [misteraddons.com](https://misteraddons.com/products/mister-fpga-io-direct) |

---

## 11. Display & Video Output (Optional — Required for CRT)

### Video output paths

| Target display | Method | Key settings in mister.ini |
|---------------|--------|---------------------------|
| Modern TV/monitor | HDMI (bare board) | none needed |
| VGA monitor (31kHz) | IO board + VGA cable | `forced_scandoubler=1` |
| CRT TV via RGB SCART | IO board + VGA-to-SCART cable (470Ω resistor) | `composite_sync=1` |
| CRT TV via Component | IO board + VGA-to-component cable | `vga_mode=ypbpr` |
| CRT via S-Video | IO Direct board | `vga_mode=svideo`, `composite_sync=1` |
| CRT via Composite | IO Direct board | `vga_mode=svideo`, `ntsc_mode=0` |
| PVM/BVM | IO board + VGA-to-BNC cable | `composite_sync=1` |
| 24-bit analog (no IO board) | HDMI-to-VGA adapter + Direct Video | `direct_video=1`, `composite_sync=1` |

### Direct Video method (no IO board needed for analog)

Plug a **passive HDMI-to-VGA adapter** into the DE10-Nano's HDMI port and enable
`direct_video=1` in `mister.ini`. Provides 24-bit color (better than standard IO board's
18-bit) and minimal lag.

**Known working adapters (must be AG6200/AG6201 chip):**
- ISY IAD-1007 HDMI Adapter
- HAMA HDMI-to-VGA Adapter (0.15m)
- Generic AG6200/AG6201-based adapters (~$5–10 on Amazon/eBay)

**Known incompatible:** Rankie H001 and many generic Amazon adapters.

> ⚠️ With `direct_video=1`, the HDMI port outputs raw unprocessed signals — your modern
> HDMI TV will not work while this mode is active.

### CRT via VGA-to-SCART

> ⚠️ **Critical:** Only use a cable with a **470-ohm resistor on the composite sync line**.
> TTL sync voltage from the board can damage unprotected CRT equipment.

- Set `composite_sync=1` in mister.ini
- [MiSTer Addons VGA to SCART cable](https://misteraddons.com/products/vga-to-scart-video-cables) — resistor built-in ✅
- Retro Access cables — also correct; widely available
- **No OSSC or RetroTINK needed** — MiSTer outputs native 15kHz directly

### Additional CRT notes
- Trinitron TVs may need an extra resistor/potentiometer to correct colour levels
- Composite output via RGB converters produces severe rainbowing (no luma trap)
- Component via Direct Video requires a Sync-on-Green circuit modification

---

## 12. Controllers & Input (Optional)

### USB controllers

Any USB HID gamepad works. Docs confirm: PS5 DualSense, Xbox, Switch Pro, arcade
spinners, Raphnet adapters, Daemonbite adapters, 8BitDo gamepads and receivers.
MiSTer supports **up to 6 players** (individual cores may have lower limits).

**Best wired options:**
- **8BitDo M30 2.4g** (wired) — excellent for Sega/Genesis style
- **Sony DualShock 4 / DualSense** (wired USB)
- **Hori Fighting Commander** (PS4 or Xbox One)

**Wireless via 8BitDo Wireless Bluetooth Adapter 2** (~$20): pairs with Xbox One S/X,
PS3, PS4, Wii, Switch, 8BitDo controllers. Mode: SELECT+UP (3s) = X-Input;
SELECT+DOWN = PSC mode. One adapter per controller for multiplayer.

### Lightguns

Officially supported lightguns: **GUN4IR**, **Guncon 2**, **Guncon 3**, **Wiimote**.
Cores: NES Zapper, PSX Guncon, SNES SuperScope, and others.

### SNAC adapters (original console controllers — near-zero latency)

Plug into the **User IO port** on the IO board — not USB. Bypasses the USB stack for
0.5–2ms less input latency vs USB adapters. Requires an IO board.

| Product | Price | Retailer |
|---------|-------|---------|
| SNAC V1 (single player) | $15 | [misteraddons.com](https://misteraddons.com/products/serial-native-accessory-converter-snac) |
| SNAX V2.1 (2-player) | $35 | [misteraddons.com](https://misteraddons.com/products/serial-native-accessory-converter-snac) |

SNAC supports: NES, SNES, Genesis/Master System, N64, PlayStation, Saturn, PC Engine,
Neo Geo, light guns, memory cards. Enable in the core's OSD menu.

USB DB9 alternatives: **Daemonbite** and **Raphnet** adapters (official docs confirmed).

### JammaSD

Arcade cabinet encoder that auto-assigns Player 1 and Player 2 inputs. Useful for
building MiSTer into an arcade cabinet.

---

## 13. MT32-Pi Add-On Board (Optional — for DOS/MIDI audio)

Adds Roland MT-32 synthesizer emulation for cores that support MIDI audio (primarily
**ao486** DOS core). Two variants:

**MT32-Pi Lite Add-On Board** — uses a **Raspberry Pi Zero 2 W** as a bare-metal MT-32
emulator. Connects via the **User IO port** on the IO board. Best integrated option.

**MT32-Pi Raspberry Pi HAT** — uses RPi 3A+, 3B, or 4 connected via USB Type-A 3.0
Male-to-Male cable. More flexible but less compact.

Both require a compatible IO board with User IO port (Digital IO, Analog IO, A/V Pro).

---

## 14. RTC (Real Time Clock) Add-On Board (Optional)

Plugs into the **LTC connector** on the DE10-Nano. Maintains accurate time offline via
CR1220 coin cell battery.

**Cores that explicitly support RTC (from official docs):** ao486, Minimig — and
additionally: Archie, BBC Master, CoCo3, C64, GBA, GBC, NeoGeo, MSX, SNES, TSConf, QL,
PC-98, X68000, ZXNext.

You don't need it if MiSTer is always connected to internet (syncs via NTP).

| Product | Price | Retailer |
|---------|-------|---------|
| MiSTer RTC v1.4 | ~£15 | [misterfpga.co.uk](https://misterfpga.co.uk/product/mister-real-time-clock-board-rtc/) |
| MiSTer RTC | varies | [ultimatemister.com](https://ultimatemister.com/product/mister-rtc/) |
| Generic on Amazon | ~$10 | [Amazon](https://www.amazon.com/Cuifati-CR1220-Module-Clock-Mister/dp/B08VN2SQTX) |
| DIY via JLCPCB/PCBway | ~$5 | Open-source files; M41T81 I2C RTC chip + 32.768kHz crystal |

---

## 15. ADC Analog Audio Input Board (Optional)

Adds a **3.5mm audio line-in** to MiSTer. Used for:
- Loading software from tape audio recordings into computer cores (C64, ZX Spectrum, etc.)
- Routing audio from external retro tape players

Available standalone or bundled with the official Ultimate MiSTer case.

---

## 16. Wi-Fi (Optional — Required for OTA Updates)

No built-in Wi-Fi on DE10-Nano. A USB Wi-Fi dongle enables:
- Running the MiSTer update script (keeps cores and firmware current)
- ROM transfer over the network
- NTP time sync (replaces need for RTC board if always online)

**Confirmed working models (from official docs):**
- ASUS USB-AC53 Nano rev A1
- TP-Link Archer T3U (AC1300) or Archer T2U Nano (AC600)
- CanaKit Raspberry Pi WiFi dongle
- Edimax EW-7612UAn v2, EW-7811UN, or EW-7822ULC
- Netgear A6100 or A6150
- D-Link AC1300 MU-MIMO Nano USB DWA-181
- MiSTer Addons WiFi+BT Combo Adapter ($20)

> ⚠️ Compatibility can vary by hardware revision within the same model name. Results may
> vary; use name-brand adapters.

---

## 17. Bluetooth (Optional — Wireless Controllers)

For wireless controller support. Compatible chipsets (from official docs):
- **CSR8510** and **BCM20702** — BT 4.0, confirmed working
- Most **Bluetooth 5.0** adapters also work

**Confirmed adapters:**
- ASUS USB-BT500
- Edimax BT-8500
- ZEXMTE Long Range USB Bluetooth
- CSR8510-based adapters (sold by misterfpga.co.uk, misterkits.com)
- MiSTer Addons WiFi+BT Combo Adapter ($20)

> Tip: If Bluetooth is unstable, use a short USB extension cable to move the dongle
> away from the USB hub — RF interference is a common cause.

---

## 18. Recommended Builds — Bill of Materials

### Minimal Build (HDMI only, get it working)

| Item | Recommended | Approx Cost |
|------|-------------|------------|
| microSD 64GB | Samsung EVO Plus | £10 |
| Power supply | MiSTer 5V 4A barrel-jack | £23 |
| 128MB SDRAM | XS-DS v3.0 (misterfpga.co.uk) | £47 |
| Heatsink | Aluminium 20mm, 3M 8810 | £3.29 |
| USB OTG adapter | Generic Micro-USB OTG | ~£2 |
| USB keyboard | Any standard USB | varies |
| USB gamepad | Any USB HID | varies |
| **Subtotal (excl. keyboard/gamepad)** | | **~£85** |

> HDMI output uses the DE10-Nano's built-in port — no extra purchase needed.

---

### Full Build (HDMI + CRT, proper case, active cooling, wireless)

| Item | Recommended | Approx Cost |
|------|-------------|------------|
| microSD 128GB | Samsung PRO Endurance | £18 |
| Power supply | Mean Well GST25A05-P1J 5V/5A | ~$18 |
| 128MB SDRAM | XS-DS v3.0 | £47 |
| IO Board | A/V Pro v9.2 (with Noctua fan) | £57.99 |
| USB Hub | USB Hub v2.1 | £29 |
| Case | Aluminum Passively Cooled Armor | $75 |
| VGA-to-SCART cable | MiSTer Addons (470Ω resistor) | ~$20 |
| Wi-Fi + BT | MiSTer Addons combo adapter | $20 |
| RTC | MiSTer RTC v1.4 | £15 |
| Controller | 8BitDo M30 + SNAX V2.1 | ~$60 |
| **Subtotal** | | **~$350–380 USD** |

> The Aluminum Armor contacts the FPGA chip directly — no separate heatsink needed.

---

## 19. Where to Buy

| Store | Region | URL |
|-------|--------|-----|
| **MiSTer Addons** | US (ships worldwide) | https://misteraddons.com/ |
| **Ultimate MiSTer** | EU (ships worldwide) | https://ultimatemister.com/ |
| **MiSTer FPGA UK** | UK (ships worldwide) | https://misterfpga.co.uk/ |
| iCode | US | https://www.icode.com/ |
| Terasic (official DE10-Nano) | TW (ships worldwide) | https://www.terasic.com.tw/ |
| Mouser / Digi-Key | Global | For Mean Well PSU and components |

---

*Compiled 2026-06-19. Primary source: MiSTer FPGA official documentation at
https://mister-devel.github.io/MkDocs_MiSTer/ — requirements list, add-on board
descriptions, Wi-Fi/BT adapter lists, controller support, CRT settings, and RTC
details all verified directly from the docs. Prices from misteraddons.com,
ultimatemister.com, and misterfpga.co.uk current as of this date.*
