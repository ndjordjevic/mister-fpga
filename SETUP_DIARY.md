# MiSTer FPGA — Setup Diary

A running log of the physical setup and configuration process for this DE10-Nano MiSTer
build. Chronological — newest entries at the bottom.

---

## 2026-07-05 — Initial setup

### Tools prepared
- Upgraded **balenaEtcher** on macOS from 2.1.4 → **2.1.6** (official arm64 build from
  balena's GitHub releases, quarantine flag cleared).
- Downloaded **Mr. Fusion v2.10** (`mr-fusion.img`, packaged MiSTer release `release_20250402`)
  — the universal auto-expanding SD card image.

### SD card selection
Had 4 cards available: **TeamGroup 32GB** (shipped with the DE10-Nano), MicroDrive 32GB,
MicroDrive 128GB, HSTF 64GB. Chose the **TeamGroup 32GB** — established legitimate brand,
already proven with this board. MicroDrive and HSTF are unbranded/AliExpress-tier cards
with little reliability verification; skipped for the boot/OS drive.

### Flashing the card
- Identified the card as `/dev/disk4` via `diskutil list` (matched by size + the factory
  `de10-nano` FAT32 partition).
- Flashed via `dd` (run manually in Terminal with `sudo`, since the automated shell
  couldn't prompt for a password):
  ```
  sudo dd if=mr-fusion.img of=/dev/rdisk4 bs=4m status=progress && sync
  ```
- Verified post-flash partition table showed the new `MRFUSION` FAT32 partition — old
  factory demo partition gone. Ejected safely.

### First boot — troubleshooting
- First attempt: **black screen**, blue power LED lit. Cause turned out to be the **SD
  card not fully seated** — reseated it and the board booted correctly on retry.
  (Confirmed via official docs that the standard checklist for this symptom is: check
  HDMI cable/connection, reseat the SD card, verify the **MSEL DIP switch** bank reads
  `On, Off, On, Off, On, On`, and check for alternating green Ethernet LEDs as a boot
  sign even without video.)
- On successful boot: Mr. Fusion auto-installed the base MiSTer system (v2.10), landed
  on the main menu with **no cores yet** ("Cores no files!").

### Board port reference (established during setup)
Confirmed physical layout from the Terasic silkscreen/photo:
- **USB OTG (Micro-USB AB)** — top-right, the *only* USB host port. This is where all
  USB peripherals connect (via adapter/hub) — there is no full-size USB-A port on the
  bare board.
- **Mini-USB B** next to it — USB Blaster II / UART, **JTAG only**, not for peripherals.
- **HDMI** — bottom-left, sole built-in video output.
- **GPIO0** (top header, near the MSEL/FPGA config switch) — where the **SDRAM board**
  plugs in.
- **GPIO1** (bottom header, near USER BUTTON/HPS_RST) — for IO/A-V boards; also used to
  power the heatsink fan (pins 11 = 5V, 12 = GND) since GPIO0 was occupied by SDRAM.
- **LTC 2×7 header** (top-right) — for an RTC board (not installed in this build).

### USB / keyboard
Using a **Raspberry Pi Keyboard and Hub** (standard HID keyboard + built-in bus-powered
3-port USB 2.0 hub — not Pi-specific despite the name) connected via a **Micro-USB→USB-A
OTG adapter** into the OTG port. No dedicated MiSTer USB Hub board in this build yet —
the keyboard's own hub ports are carrying the Wi-Fi dongle for now.

### Cores & network
- Ran the **update** script (Update_All equivalent) over Ethernet — downloaded the full
  core library (Arcade / Computer / Console / Other / Utility categories) and populated
  what had been an empty core list.
- **Timezone fix:** clock was 2 hours off (MiSTer defaults to UTC; Serbia is UTC+1/+2).
  Tried the **rtc** script first — failed with `hwclock: cannot access the hardware
  clock via any known method`, which is expected since **no RTC add-on board** is
  installed (no hardware clock to query). Used the separate **timezone** script instead,
  set to **auto** — corrected the clock immediately, and NTP over the network keeps it
  accurate going forward without needing an RTC board at all.

### SDRAM board
Installed the **MiSTer SDRAM v3.0 XS-DS, 128MB** board into the **GPIO0** header
(top, vertical mount). Powered off fully before inserting, supported the DE10-Nano from
below to avoid flexing/cracking the header. Verified detection by loading the **SNES**
core — it opened cleanly with no "SDRAM not found" warning, confirming the board is
seated and recognized. (Full functional test with an actual ROM still pending.)

### Heatsink + fan
Installed a heatsink with pre-applied thermal tape directly onto the Cyclone V FPGA chip
(centered, pressed firmly ~10-15 sec to set the adhesive bond). It came with an attached
fan needing power — connected to the **GPIO1** header (since GPIO0 was taken by SDRAM),
using the pins directly under the silkscreened **"11"** label: top pin = 11 (5V), bottom
pin = 12 (GND). Powered on — fan spins, board boots normally.

### Wi-Fi / Bluetooth
Added a generic **"WiFi 5 BT + AC600 driver-free"** USB combo dongle, plugged into the
Raspberry Pi keyboard's passthrough hub port. Despite this chipset family (Realtek
RTL8811CU/RTL8821CU) typically requiring manually-compiled third-party drivers on
regular Linux and having no native kernel support — meaning it usually **would not**
work on MiSTer's fixed embedded kernel — **it worked anyway** once the `wifi` script was
run. Confirmed as a genuine working connection (not just piggybacking on Ethernet) by:
1. Unplugging the Ethernet cable entirely.
2. Confirming the main menu showed Wi-Fi + Bluetooth icons instead of the wired icon.
3. Re-running the **update** script successfully with only Wi-Fi connected.

### Oric core
The **Oric** core (MiSTer-devel/Oric_MiSTer) was already present after the core sync.
First boot defaulted to the **Pravetz 8D** ROM variant (Cyrillic BASIC) — switched the
machine type in the OSD (F12) to **Oric Atmos** and reset, landing on standard English
**Oric Extended BASIC v1.1 (1983, Tangerine)**. Confirmed via web research this is the
correct, final, and only official ROM revision for the real Atmos hardware (v1.1 was a
bugfixed v1.0 with no later official update) — nothing to upgrade.

### Not yet installed
- **USB Hub add-on board** — not owned yet; currently relying on the keyboard's built-in
  hub for the Wi-Fi dongle. Fine for now, but bus-power-limited if more peripherals are
  added.
- **IO board** (VGA/audio/CRT output) — HDMI-only for now.
- **RTC board** — not needed; NTP over network/Wi-Fi covers timekeeping.
- Oric software (TAP/DSK files) — core confirmed working, no software loaded/tested yet.

---

## Open items for next session
- Load an actual ROM/game to fully verify SDRAM under real gameplay (not just clean
  core load).
- Get Oric TAP or disk software loaded and confirm CLOAD / Microdisc behavior.
- Consider a proper USB Hub board once more peripherals are added (controllers, etc.).
