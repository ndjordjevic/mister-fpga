# MiSTer FPGA — Research Resources

Research notes and resource collection for **MiSTer FPGA**: an open-source project that
recreates classic computers, video game consoles, and arcade machines using modern FPGA
hardware. The platform is built around the **Terasic DE10-Nano** development board, which
contains an **Intel Cyclone V SoC FPGA** (110K LEs + dual-core Cortex-A9 ARM).

> **Key concept:** MiSTer uses FPGA to *re-implement* the original hardware at the logic
> level — not software emulation. The result is cycle-accurate behavior including original
> video output, audio timing, and hardware quirks, on real display hardware.

---

## 1. Official Documentation

| Resource | URL | Notes |
|----------|-----|-------|
| **MiSTer FPGA Documentation** | https://mister-devel.github.io/MkDocs_MiSTer/ | ⭐ Main official docs (MkDocs). Setup, configuration, core guides |
| MiSTer Documentation — Links page | https://mister-devel.github.io/MkDocs_MiSTer/basics/links/ | Curated list of community resources from the docs team |
| Computer Cores reference | https://mister-devel.github.io/MkDocs_MiSTer/cores/computer/ | All supported computer platforms (Amiga, C64, Atari, DOS, etc.) |
| Console Cores reference | https://mister-devel.github.io/MkDocs_MiSTer/cores/console/ | All supported consoles (SNES, Genesis, NES, PS1, N64, etc.) |
| Arcade Cores reference | https://mister-devel.github.io/MkDocs_MiSTer/cores/arcade/ | All supported arcade boards |
| **MiSTer FPGA Bible** | https://boogermann.github.io/Bible_MiSTer/getting-started/introduction/ | ⭐ Community-written supplementary guide, very thorough |
| MiSTer Bible — Setup guide | https://boogermann.github.io/Bible_MiSTer/getting-started/how-to-setup/ | Step-by-step: SD card, first boot, updating, running a core |
| MiSTer Bible — What you need | https://boogermann.github.io/Bible_MiSTer/getting-started/what-you-need/ | Hardware checklist: DE10-Nano, SDRAM, USB Hub, I/O board |
| MiSTer Wikipedia | https://en.wikipedia.org/wiki/MiSTer | Good overview: history, architecture, supported systems |
| Emulation General Wiki — MiSTer | https://emulation.gametechwiki.com/index.php/MiSTer | Comparison vs emulation, core quality notes, setup tips |
| adreeve/MiSTerManual (GitHub) | https://github.com/adreeve/MiSTerManual | Community manual: getting started with computers/consoles on MiSTer |

---

## 2. Hardware — The DE10-Nano & Peripherals

The heart of MiSTer is the **Terasic DE10-Nano** board (Intel Cyclone V SE, 110K LEs,
dual-core ARM Cortex-A9, DDR3, HDMI, Ethernet). MiSTer runs on the ARM side and
programs the FPGA fabric on demand per core.

### Essential peripherals
- **128MB SDRAM board** — required by many cores (Amiga, SNES, etc.) for accurate timing
- **USB hub** (powered, OTG) — connects keyboards, gamepads, etc.
- **I/O board** — adds VGA, analog audio, buttons, user port; optional but recommended
- **SD card** (32GB+) — stores the MiSTer OS, cores, and ROMs

### Hardware resources

| Resource | URL | Notes |
|----------|-----|-------|
| **Terasic DE10-Nano** (official) | https://www.terasic.com.tw/cgi-bin/page/archive.pl?Language=English&CategoryNo=167&No=1046&PartNo=1#contents | Official product page — Overview tab. Spec: Cyclone V 5CSEBA6U23I7, 110K LE, DDR3, HDMI, ETH |
| DE10-Nano — MiSTer FPGA Forum | https://misterfpga.org/viewtopic.php?t=3397 | Community thread on hardware specifics |
| What components do I need? (Forum) | https://misterfpga.org/viewtopic.php?t=3753 | Community answer to full setup BOM |
| Multisystem all-in-one motherboard | https://github.com/Heber-co-uk/Multisystem | Heber Ltd PCB integrating DE10-Nano + all peripherals into one board |
| MiSTer Floppy (Minimig core) | https://mister.robsmithdev.co.uk/get | Using real floppy drives with MiSTer's Amiga Minimig-AGA core |
| DE10-Nano top layout image (official) | https://www.terasic.com.tw/attachment/archive/1046/image/layout_top.jpg | Terasic's official board photo/diagram — ports, headers, chip positions |

---

## 2b. MiSTer Pi — DE10-Nano Clone Alternative

The **MiSTer Pi** is a third-party clone of the DE10-Nano, designed from the ground up for
MiSTer FPGA. Made by **Taki Udon / Retro Remake** (retroremake.co). Despite the name, it
has no connection to Raspberry Pi — the name references its USB-C power delivery.

**Same Altera Cyclone V FPGA chip as the DE10-Nano.** Fully compatible with all MiSTer
cores and SD cards prepared for the DE10-Nano. No significant setup differences.

### Key differences vs DE10-Nano

| | DE10-Nano | MiSTer Pi |
|-|-----------|-----------|
| FPGA | Cyclone V 5CSEBA6U23I7 (110K LEs) | Same Cyclone V chip |
| DDR3 RAM | 1GB onboard | 1GB onboard |
| Power input | 5.5/2.1mm barrel jack (5V) | **USB-C PD** (5V/2A min; up to 12V/3A regulated) |
| UART debug | USB Mini-B | **USB-C** |
| HDMI | ✅ up to 1536p | ✅ up to 1536p |
| Price | ~$220–250 (board only) | **$99** base; $125 with 128MB SDRAM |
| Availability | Terasic direct (always) | Batch pre-orders; sells out fast |
| Caveats | None — original hardware | USB-C only; some HDMI quirks reported early on |

### Bundle options (retroremake.co)

| Bundle | Price | Contents |
|--------|-------|----------|
| Base board | $99 | MiSTer Pi board only |
| RAM Bundle | $125 | Board + 128MB SDRAM module |
| **Mega Pack** | $160 | Board + 128MB SDRAM + A/V Pro v9.2 + USB Hub + Wi-Fi/BT adapter + PD cable + standoffs + acrylic topper |
| **Turbo Pack** | $180 | Everything in Mega Pack + 64GB SD card pre-loaded with MiSTer firmware, fully assembled |

### Accessories sold by Retro Remake

| Product | Price | Status (as of 2026-06-19) |
|---------|-------|--------------------------|
| MiSTer A/V Pro v9.2 | $40 | Sold out |
| 128MB SDRAM | $20 | Available |
| MiSTer USB Hub | $20 | Available |
| MiSTer Pi Acrylic Case | $10 (sale from $15) | Available |
| Wi-Fi 5 / BT 4.2 USB Adapter | $5 | Available |
| SNES SNAC Adapter Bundle | $5 | Available |
| NFC Reader Pack | $10 | Available |
| N64 SNAC Adapter Bundle | $10 | Sold out |
| MiSTer Pi RTC | $5 | Sold out |

### MiSTer Pi resources

| Resource | URL | Notes |
|----------|-----|-------|
| **Retro Remake store** | https://retroremake.co/pages/store | ⭐ Official MiSTer Pi shop; all bundles and accessories |
| Retro Remake — MiSTer Pi product | https://retroremake.co/products/mister-pi-retro-gaming-fpga-board-1 | Single board listing |
| Retro Remake — Turbo Pack | https://retroremake.co/products/mister-pi-retro-gaming-fpga-board-turbo-pack-1 | Complete ready-to-go bundle |
| **Firmware / offline install** | https://github.com/Takiiiiiiii/MiSTER-Pi-Offline-Stock | GitHub; offline SD card image for MiSTer Pi |
| Setup video | https://www.youtube.com/watch?v=cbppuWmukw0 | Official setup walkthrough (YouTube) |
| 3D case files (Thingiverse) | https://www.thingiverse.com/thing:6744156 | Printable case designs for MiSTer Pi |
| Retro Remake Discord | https://discord.gg/rnfrEF6mXr | Community + support server |
| Forum thread (misterfpga.org) | https://misterfpga.org/viewtopic.php?t=8340 | Main MiSTer Pi discussion thread |
| Irken Labs mini review | https://irkenlabs.com/mister-pi-de10-clone-mini-review/ | Technical comparison vs DE10-Nano; notes USB-C caveats |
| dpad.org review | https://dpad.org/metaposts/hardware-review-mister-pi/ | "FPGA Gaming on a Budget with MiSTer Pi" |
| TinkerDifferent — first impressions | https://tinkerdifferent.com/threads/mister-pi-first-impressions.3996/ | Community first-look thread |
| Lon.TV blog post (Dec 2024) | https://blog.lon.tv/2024/12/29/the-mister-pi-is-an-affordable-new-option-for-fpga-retro-gaming-and-computing/ | Lon Seidman's overview |
| **Time Extension review** | https://www.timeextension.com/reviews/mister-pi-a-usd99-gateway-to-fpga-retro-gaming | ⭐ "A $99 Gateway to FPGA Retro Gaming" — recommended read |
| RetroRGB — Digging into MiSTer Pi | https://retrorgb.com/digging-into-the-mister-pi.html | RetroRGB hands-on coverage |

---

## 3. GitHub — Official MiSTer-devel Organization

**Org:** [MiSTer-devel](https://github.com/MiSTer-devel) — official GitHub org hosting all cores
and the main framework.

| Repository | URL | Description |
|------------|-----|-------------|
| **Main_MiSTer** | https://github.com/MiSTer-devel/Main_MiSTer | ⭐ Core MiSTer framework: Linux binary, FPGA loader, menu system |
| Main_MiSTer Wiki | https://github.com/MiSTer-devel/Main_MiSTer/wiki | ⭐ Official wiki: setup guides, MiSTer.ini config reference, advanced topics |
| **Minimig-AGA_MiSTer** | https://github.com/MiSTer-devel/Minimig-AGA_MiSTer | Amiga OCS/ECS/AGA core (main Amiga emulation on MiSTer) |
| **mr-fusion** | https://github.com/MiSTer-devel/mr-fusion | "MiSTer Fusion" — universal SD card image for easy first-time setup |
| Distribution_MiSTer | https://github.com/MiSTer-devel/Distribution_MiSTer | Base SD card files (scripts, menus, system config) |
| SD-Installer-Win64_MiSTer | https://github.com/MiSTer-devel/SD-Installer-Win64_MiSTer | Windows tool for writing MiSTer SD card images |
| **Downloader_MiSTer** | https://github.com/MiSTer-devel/Downloader_MiSTer | Official tool for installing/updating cores and system files |
| MRA-Alternatives_MiSTer | https://github.com/MiSTer-devel/MRA-Alternatives_MiSTer | Alternative .mra files: different game versions, bootlegs, hacks |
| MiSTer-devel (all repos) | https://github.com/MiSTer-devel?q=&type=all&language=&sort= | Browse all 300+ cores and tools in the org |

### Community tools

| Repository | URL | Notes |
|------------|-----|-------|
| **Update_All_MiSTer** | https://github.com/theypsilon/Update_All_MiSTer | ⭐ Community all-in-one updater: runs Downloader + extra databases |
| MiSTer_Batch_Control | https://github.com/pocomane/MiSTer_Batch_Control | CLI utility to control MiSTer remotely (load ROMs, send key sequences) |
| sy2002/MiSTer2MEGA65 | https://github.com/sy2002/MiSTer2MEGA65/wiki/The-Ultimate-MiSTer2MEGA65-Porting-Guide | Detailed guide for porting MiSTer cores to the MEGA65 FPGA computer |
| adreeve/MiSTerManual | https://github.com/adreeve/MiSTerManual | Community reference manual for all platforms on MiSTer |
| Heber-co-uk/Multisystem | https://github.com/Heber-co-uk/Multisystem | All-in-one MiSTer motherboard design |

---

## 4. Community — Forums, Discord, Wikis

| Resource | URL | Notes |
|----------|-----|-------|
| **MiSTer FPGA Forum** | https://misterfpga.org/ | ⭐ Main community forum (phpBB). Hardware, cores, dev, support |
| Forum — Arcade Cores subforum | https://misterfpga.org/viewforum.php?f=25 | Dedicated arcade cores discussion |
| Forum — Oric-1/Atmos core | https://misterfpga.org/viewtopic.php?t=4599 | Specific platform core discussion thread |
| Forum — Verilog/HDL books & tutorials | https://misterfpga.org/viewtopic.php?t=136 | ⭐ Recommended learning resources for FPGA/Verilog (pinned) |
| Forum — Learning to dev a MiSTer core | https://misterfpga.org/viewtopic.php?t=78 | Core development starting point: what to learn, how to start |
| Forum — What systems are supported? | https://misterfpga.org/viewtopic.php?t=7550 | Comprehensive answer to supported systems question |
| Forum — Arcade core list | https://misterfpga.org/viewtopic.php?t=306 | Community-maintained arcade titles list |
| **MiSTer FPGA Discord** | https://discord.com/servers/mister-fpga-647909397477195803 | ⭐ Official Discord server, 19K+ members; devs and core authors active here |
| INI Settings Reference | https://mister-devel.github.io/MkDocs_MiSTer/advanced/ini/ | Full MiSTer.ini reference: vsync, vscale, video modes, analog output |
| Updater Guide (official docs) | https://mister-devel.github.io/MkDocs_MiSTer/setup/updater/ | How to run the Downloader/updater from the OSD |
| RetroRGB — New MiSTer Updater | https://retrorgb.com/new-mister-updater.html | Writeup on the transition from update.sh to Downloader_MiSTer |

---

## 5. Tutorials & Guides

| Resource | URL | Notes |
|----------|-----|-------|
| **RetroRGB — MiSTer FPGA Hardware** | https://retrorgb.com/mister.html | ⭐ The go-to hardware guide: what to buy, how to set up, display tips |
| RetroRGB — Getting Started (YouTube) | https://www.youtube.com/watch?v=F5__shDTYMQ | Video walkthrough of first-time MiSTer setup |
| RetroRGB — Retro Castle MiSTer Kits | https://www.youtube.com/watch?v=WTeJ1eJJx-4 | Kit options overview (video) |
| RetroRGB — YouTube channel | https://www.youtube.com/@RetroRGB/videos | MiSTer news, tutorials, display comparisons |
| **My Life in Gaming** (YouTube) | https://www.youtube.com/watch?v=rhT6YYRH1EI | ⭐ Hosts Coury & Try; produced the definitive 2.5-hour MiSTer Primer Guide |
| **Pezz82** (YouTube) | https://www.youtube.com/c/Pezz82/videos | Dedicated FPGA gaming channel; core setups, reviews |
| MiSTer FPGA Tutorials Playlist | https://www.youtube.com/playlist?list=PLjnN8ZeZ9orupD5lsSbnusDEzHE0Twynd | Community playlist: tutorials, how-tos, core reviews |
| RetroGameCoders — MiSTer tutorial | https://retrogamecoders.com/mister-fpga-tutorial-getting-started-with-mister/ | Written + video getting-started guide |
| MiSTerReplay — Beginner's Complete Guide | https://misterreplay.com/mister-fpga-guide.html | Community guide covering full setup + core configuration |

---

## 6. Core Development (Verilog / SystemVerilog / VHDL)

MiSTer cores are written in **Verilog** or **SystemVerilog** (primary language for new cores).
The FPGA fabric is the Intel Cyclone V; synthesis uses **Quartus Prime Lite** (free).

| Resource | URL | Notes |
|----------|-----|-------|
| Forum — Learning to develop a core | https://misterfpga.org/viewtopic.php?t=78 | Community thread: prerequisites, workflow, how to read existing cores |
| Forum — Verilog/HDL books & tutorials | https://misterfpga.org/viewtopic.php?t=136 | ⭐ Curated reading/learning list (pinned thread) |
| Retro Reversing — Verilog | https://www.retroreversing.com/verilog | Retro-focused Verilog reference |
| Doulos — SystemVerilog for FPGA | https://www.doulos.com/knowhow/systemverilog/using-systemverilog-for-fpga-design/ | Practical SV introduction for FPGA designers |
| EDA Playground | https://www.edaplayground.com | Online Verilog/SV simulator — good for learning without hardware |
| MiSTer2MEGA65 porting guide | https://github.com/sy2002/MiSTer2MEGA65/wiki/The-Ultimate-MiSTer2MEGA65-Porting-Guide | Detailed guide to adapting MiSTer cores — reveals core internals |

### Books referenced by the MiSTer community

| Title | Notes |
|-------|-------|
| *Designing Video Game Hardware in Verilog* — S. Hugg (2018) | ⭐ Recommended in core dev thread; directly relevant (8bitworkshop) |
| *FPGA Prototyping by SystemVerilog Examples* | Referenced for SV FPGA work |

---

## 7. Reviews & Articles

| Resource | URL | Notes |
|----------|-----|-------|
| **HotHardware — MiSTer DIY review** | https://hothardware.com/reviews/mister-diy-console-fpga | In-depth technical review with FPGA accuracy comparisons |
| Time Extension — MiSTer review (2024) | https://www.timeextension.com/reviews/mister-fpga-still-the-best-option-for-hardcore-retro-gamers-in-2024 | "Still the best option for hardcore retro gamers?" (2024) |
| RetroRGB — MiSTer news (ongoing) | https://retrorgb.com/mister-fpga-news-retroachievements-polygame-master-darius-2-more.html | Running news coverage of new cores, features, developments |
| RetroRGB — MiSTer Pi | https://retrorgb.com/digging-into-the-mister-pi.html | Covering the Raspberry Pi-based MiSTer variant |

---

## 8. Where to Buy — Hardware, Kits & Accessories

### MiSTer-specific shops

| Store | URL | Notes |
|-------|-----|-------|
| **MiSTer Addons** | https://misteraddons.com/ | ⭐ US-based. Complete kits, SDRAM boards, cases, accessories |
| MiSTer Addons — DE10-Nano | https://misteraddons.com/collections/parts/de10-nano | DE10-Nano and accessories listings |
| MiSTer Addons — Pre-built bundle | https://misteraddons.com/products/mister-pre-configured-bundle-with-aluminum-case | Pre-configured kit with aluminum case |
| **Ultimate MiSTer** | https://ultimatemister.com/ | ⭐ EU-based, ships worldwide. Kits + fully built units |
| Ultimate MiSTer — Complete Kit | https://ultimatemister.com/product/ultimate-mister-kit/ | Full peripheral kit (DE10-Nano sold separately) |
| **MiSTer FPGA UK** | https://misterfpga.co.uk/ | UK-based; official MiSTer Addons reseller, free UK shipping over £20 |
| iCode — DE10-Nano for MiSTer | https://www.icode.com/product/terasic-de10-nano-fpga-board-for-mister-project/ | DE10-Nano targeted at MiSTer builders |
| **Terasic** (official source) | https://www.terasic.com.tw/cgi-bin/page/archive.pl?Language=English&CategoryNo=167&No=1046 | Official Terasic shop for DE10-Nano board |
| **Retro Remake** (MiSTer Pi) | https://retroremake.co/pages/store | ⭐ MiSTer Pi clone board + accessories; ~$99 base, $180 Turbo Pack (fully built) |

---

## 9. Key Technical Facts

- **Platform:** Terasic DE10-Nano, Intel Cyclone V SE 5CSEBA6U23I7 (110K LEs)
- **SoC:** Dual-core ARM Cortex-A9 @ 800MHz runs Linux; FPGA fabric programmed per-core
- **SDRAM:** Separate SDRAM expansion board (32MB or 128MB) required by many cores
- **Framework language:** Cores in Verilog / SystemVerilog; synthesis via Quartus Prime Lite
- **Core deployment:** Cores are `.rbf` bitstream files; Linux loads them into the FPGA
- **Update mechanism:** `update_all` script fetches latest cores + system files from GitHub
- **Display output:** HDMI native; I/O board adds VGA, SCART-compatible analog RGB, S-Video
- **Supported systems:** ~200+ cores including: NES, SNES, Genesis, PS1, N64, GBA, Neo Geo,
  Amiga (AGA), C64, Atari ST, DOS (ao486), X68000, PC-88/98, ZX Spectrum, 100s of arcade boards

---

## Open Questions

- Which cores require SDRAM vs can run on on-chip memory only (no consolidated public list found)
- Best workflow for building/testing a custom core on DE10-Nano without full Quartus install

---

*Compiled 2026-06-19. Bookmarks sourced from Brave (Retro/FPGA subfolder + adjacent Retro folders). Web research verified key URLs.*
