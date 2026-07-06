# Awesome Neewer

An index of third-party software that controls, documents, or integrates with Neewer LED
lights — with an emphasis on the Bluetooth-LE "Infinity" family (TL120C / TL90C / TL60 tubes,
RGB / CB / SL panels, COB heads).

Activity figures (stars, last-commit dates), licenses, and model lists are stated only where
visible on the project's page.

## From this project

The software written alongside this reference — built on, and feeding back into, the research
documented here:

- **[neewer-python](https://github.com/verygeeky/neewer-python)** — Python (PyPI:
  [`neewer`](https://pypi.org/project/neewer/)). The control **library**: a typed `Fleet` BLE
  client (persistent connections, auto-reconnect supervisor, groups/aliases/positions), the
  verified frame layer (power / HSI / CCT / scenes / pixel / RGBCW / CIE-xy / gel, by-MAC and
  by-channel forms), reply decoding, fixture identity from the advertised serial, per-model
  capability gating, DMX personalities, and animation effects. The protocol core is pure
  standard library — `bleak` enters only at the transport seam. MIT.
- **[neewerd](https://github.com/verygeeky/neewerd)** — Python (PyPI:
  [`neewerd`](https://pypi.org/project/neewerd/)). The control **daemon** over that library:
  holds the BLE links and exposes the lights over socket / MQTT with Home Assistant discovery /
  OSC / HTTP REST + web UI / Art-Net / sACN, plus a `neewerctl` CLI client and a stdio MCP
  server (`neewer-mcp`). MIT.

(The [verygeeky/NeewerLite-Python](#fork-lineage) fork under Fork lineage is from the same
author — a GUI client re-aimed at the daemon.)

## A note on transports

Neewer lights do not all speak one protocol. Four physically distinct control paths appear
across the projects below, and it matters which one a project targets:

- **BLE GATT** — the `6940…` service (`69400001` service, `69400002` write, `69400003`
  notify) with `[0x78, tag, len, payload…, checksum]` frames. This is the family this
  repository documents. A separate `0x7A` frame prefix is used by the newer "NEEWER Home"
  (`NH-*`) smart-home line (see [matthobby/neewer-lib](#protocol-documentation-elsewhere)).
- **WiFi / UDP** — the GL1 / GL25 key-light class, controlled over UDP (ports 5052 or 5052x)
  with an IP-registration handshake, heartbeats, and a sum-mod-256 checksum. Not BLE.
- **2.4 GHz proprietary RF** — the wireless remote and the USB dongle (VID `0x0581`,
  PID `0x011D`), using a Nordic-nRF24-class radio. Not BLE.
- **USB serial** — CH340-based USB-C control on some newer fixtures (PL81-Pro), with a
  `0x3A`-prefixed / 16-bit-checksum framing distinct from the BLE `0x78` family.

Projects are grouped by function; the transport is called out per entry.

---

## Closed source / commercial

Neewer's own apps and the paid control tools. Closed source; listed for completeness — the
community projects elsewhere in this list are the open alternatives.

- **NEEWER Studio** — the primary first-party control app (Bluetooth LE).
  [Android](https://play.google.com/store/apps/details?id=neewer.nginx.annularlight) ·
  [iOS](https://apps.apple.com/us/app/neewer-studio/id1455948340). The Android package id is
  `neewer.nginx.annularlight` — an unusual identifier whose `annularlight` ("ring light") points
  to the app's origin as a ring-light controller.
- **NEEWER Control Center** — first-party device-management app for desktop.
  [Mac App Store](https://apps.apple.com/us/app/neewer-control-center/id1664344174?mt=12).
- **NEEWER Live** — first-party app for the WiFi / streaming lights.
- **NEEWER Control** — the official closed-source **Elgato Stream Deck** plugin (v3.1.10) for
  Neewer streaming lights, connecting through NEEWER Control Center; the integration the
  community OBS / Stream Deck projects reimplement.
  [Elgato Marketplace](https://marketplace.elgato.com/product/neewer-control-953dc64a-234b-49a4-9702-bdbdbadd7f49).
- **NEEWER Live 2.4G** — first-party app paired with the 2.4 GHz USB dongle.
- **NEEWER Home** — first-party home-lighting app for the `NH-*` line (Bluetooth / WiFi), which
  uses the distinct `0x7A` protocol noted above.

---

## Control libraries

### Python

- **[matthobby/neewer-lib](https://github.com/matthobby/neewer-lib)** — Python (PyPI:
  [`neewerlite`](https://pypi.org/project/neewerlite/)). BLE control library that documents
  and implements two opcode families: the standard `0x78` "Studio" protocol and a separate
  `0x7A` "Neewer Home" protocol for strip lights. Same `6940…` GATT triad. MIT. ~1 star.
  Release 0.3.3 (2026-04-13). *Protocol overlap: confirms the `0x78` prefix and adds a
  documented `0x7A` variant this repo has not characterised.*
- **[amattas/neewer-sacn](https://github.com/amattas/neewer-sacn)** — Python 3.10+. A BLE
  control library + CLI/TUI whose headline feature is an sACN/E1.31 bridge (also listed under
  [DMX](#dmx--art-net--sacn--osc--midi-bridges)). Ships a full written spec at
  [`docs/protocol.md`](https://raw.githubusercontent.com/amattas/neewer-sacn/main/docs/protocol.md).
  MIT (README states MIT; no SPDX detected by the GitHub API — verify the LICENSE file).
  Last push 2026-06-25. Models: documents Infinity (TL60 RGB / TL90C / TL120C / PL60C /
  RL45C / SL90), Extended Legacy (GL1C, RGB62), and Legacy variants; live-tested TL120C and
  PL60C. *Protocol overlap: matches `0x86` HSI / `0x87` CCT / `0x88`-class scene and the
  additive checksum; documents an Infinity envelope that inserts a 6-byte MAC after the
  length byte, and reports that Infinity devices do not enforce MAC validation.*
- **[Electronics/NeewerLitePython](https://github.com/Electronics/NeewerLitePython)** —
  Python. A control library / Home Assistant custom integration that borrows from
  `sysofwan/ha-triones` and the original macOS `NeewerLite`. Distinct from
  taburineagle's fork of the same name. 6 stars. Last push 2023-10-28.

### Go

- **[anna-oake/go-neewer](https://github.com/anna-oake/go-neewer)** — Go. Early-stage BLE
  library (`protocol` / `network` / `receive` source files); README describes the
  architecture as unfinished. 2 stars.
- **[lcyvin/neewer-go](https://github.com/lcyvin/neewer-go)** — Go. WIP library + CLI
  targeting the Neewer SL90; currently only device scanning is implemented. MIT. Last push
  2023-02-27.

### Rust

- **[iKadmium/sacn-neewer-lite](https://github.com/iKadmium/sacn-neewer-lite)** — Rust.
  Bridges sACN / E1.31 DMX-over-IP to Neewer BLE lights (also under
  [DMX](#dmx--art-net--sacn--osc--midi-bridges)). MIT. Last push 2025-01-16.
- **[izzyk-coder/neewer-ambilight](https://github.com/izzyk-coder/neewer-ambilight)** —
  Rust. Screen-colour / ambilight sync driving a Neewer BLE light. MIT. Last push 2026-05-24.

### JavaScript / TypeScript

- **[coachjamesgunaca/homebridge-neewer-lights](https://github.com/coachjamesgunaca/homebridge-neewer-lights)**
  — TypeScript. Homebridge/HomeKit plugin reimplementing the BLE protocol directly (no app).
  Confirmed on RGB660 PRO; claims classic-protocol support for RGB530/480, RGB176, CB60 RGB.
  MIT. Last push 2026-06-27. *Protocol overlap: documents `[0x78][tag][length][payload][checksum]`
  with tag `0x87` for CCT and `0x86` for HSI — matching this repo's frame layer exactly.*
- **[level451/neewerjs](https://github.com/level451/neewerjs)** — JavaScript. Confirmed to
  exist; no usable README content (likely an early stub).
- **[brennancheung/bt-neewer](https://github.com/brennancheung/bt-neewer)** — JavaScript.
  Confirmed to exist; no usable README content. Last push 2021-01-24 (oldest artifact found).

---

## CLI tools

- **[SergeDubovsky/neewer-cli](https://github.com/SergeDubovsky/neewer-cli)** — Python
  (`bleak`, optional `textual` TUI; PyPI: [`neewer-cli`](https://pypi.org/project/neewer-cli/)).
  Standalone BLE CLI focused on fast, reliable, GUI-less control; status queries, extended
  scene args (hue/sat/speed), CCT/HSI/scene modes, presets. Explicitly derived from
  NeewerLite-Python (credits Zach Glenwright). MIT. 0 stars. v0.1.4, last push 2026-06-19.
- **[dsjodin/neweer_studio](https://github.com/dsjodin/neweer_studio)** — Python 3.10+. CLI +
  REPL for BLE control (power, CCT, RGB, HSI, scenes); tested against TL21C and SL90; ships
  its own from-scratch `PROTOCOL.md`. Very early (single commit on a non-main branch).
- **[Rokkit-exe/neewerctl](https://github.com/Rokkit-exe/neewerctl)** — Go. CLI for the
  Neewer PL81 over a **serial (CH340)** link, not BLE — 8-byte frame
  `0x3A, 0x02, 0x03, power, brightness, temp, 0x00, checksum`, resent every 60–80 ms.
  GPL-3.0. Last push 2026-01-13.
- **[Antti/neewer-control-cli](https://github.com/Antti/neewer-control-cli)** — Rust.
  Confirmed to exist; no further protocol detail surfaced. Last push 2024-09-04.
- **[Chippi/neewer-gl1-pro-cli](https://github.com/Chippi/neewer-gl1-pro-cli)** — TypeScript
  / Node. CLI for the **WiFi** GL1 Pro (IP-addressed, brightness 1–100, CCT 2900–7000 K,
  auto IP detection). 2 stars.
- **[filiptepper/neectl](https://github.com/filiptepper/neectl)** — TypeScript. "Control
  Neewer LED over USB adapter" — **USB** transport, not BLE. Last push 2024-01-02.

---

## Desktop / mobile apps

- **[keefo/NeewerLite](https://github.com/keefo/NeewerLite)** — macOS / Swift. The original
  community app the whole "NeewerLite" family descends from (author Xu Lian, @keefo).
  Brightness / CCT (3200–8500 K) / RGB / scenes / gels, a microphone sound-to-light mode,
  URL-scheme automation, a bundled Stream Deck plugin, and an MCP server for AI control.
  MIT. **158 stars** — by far the most-starred project in this index. Last push 2026-05-13.
  Broad model list (660/480/RGB530 PRO, SL90 Pro, RGB176/RGB1-A/RGB62/TL21C, GL1/GL1 Pro/GL1C,
  BH-30S/TL60 RGB/TL40, CB60 RGB/CB60B/CB100C/CB120B, NS02). *Protocol overlap: uses the
  `0x78` frame prefix (`[0x78,0x01,0x01]`) and distinguishes "new 78,8F" vs "old 78,87" CCT
  forms; detailed opcodes/UUIDs are loaded from a bundled database. Its wiki also documents a
  separate `0x7A` "NEEWER Home" protocol for the `NH-*` line.*
- **[taburineagle/NeewerLite-Python](https://github.com/taburineagle/NeewerLite-Python)** —
  Python / PySide. Cross-platform port of keefo's macOS app; GUI, CLI, and HTTP-server modes.
  MIT. 89 stars. Last push 2025-02-18. The hub of the Python-side fork tree (see
  [Fork lineage](#fork-lineage)).
- **[PoizenJam/NeewerLux](https://github.com/PoizenJam/NeewerLux)** — Python. The standout
  divergent fork of NeewerLite-Python (~21 commits ahead): adds a keyframe/chained-animation
  engine, multi-light presets, a visual editor, and a GET-based animation HTTP API. 1 star.
  Last push 2026-06-12.
- **[verygeeky/NeewerLite-Python](https://github.com/verygeeky/NeewerLite-Python)** — Python /
  PySide. A fork of taburineagle's NeewerLite-Python that adds a client mode driving the
  [`neewerd`](../neewerd) daemon over its socket / HTTP API (rather than owning the BLE link
  itself), and extends the UI toward exposing every BLE opcode type. (See
  [Fork lineage](#fork-lineage).)
- **[TheASDM/Beacon](https://github.com/TheASDM/Beacon)** — Swift. A substantial fork/rewrite
  of keefo's NeewerLite: full SwiftUI redesign, expanded device DB (74 types), overhauled
  Stream Deck+ plugin, and CB100C command fixes. MIT. Last push 2026-04-07.
- **[ratmice/neewerctl](https://github.com/ratmice/neewerctl)** — Rust (`druid` GUI +
  `btleplug`). Cross-platform BLE controller (Linux/Windows tested); targets the RGB-480.
  Credits keefo's and taburineagle's projects for the protocol. Dual Apache-2.0 / MIT.
  2 stars. Last push 2022-02-01.
- **[Teravus/LightPanelControlTool](https://github.com/Teravus/LightPanelControlTool)** —
  C# / Windows WPF. Exposes an HTTP control surface for Neewer BLE panels aimed at
  StreamDeck / Streamer.bot / SAMMI. Built for RGB660 Pro. Apache-2.0. Last push 2026-05-28.
  README notes the protocol is community-reverse-engineered "from observed behavior, magic
  bytes, and magic numbers."
- **[AugusDogus/neweer-tray](https://github.com/AugusDogus/neweer-tray)** — Zig (Windows) /
  Rust (Linux). Minimal on/off tray toggle targeting the **2.4 GHz USB dongle** (VID `0x0581`,
  PID `0x011D`) over Windows HID — not BLE. Protocol found by hooking `Send_RF_DATA` in
  Neewer's own DLL with Frida (see [RE writeups](#reverse-engineering-writeups)). MIT.
  Last push 2026-05-28.
- **[m-rk/neewer-usb-control](https://github.com/m-rk/neewer-usb-control)** — Tauri (Rust /
  Svelte) + Python CLI. macOS menubar app + CLI talking to a **CH340 USB-serial** chip over
  USB-C; tested on the PL81-Pro. Frame `[0x3A][tag][len][payload][checksum_hi][checksum_lo]`
  (16-bit big-endian checksum), CCT tag `0x02`. Claims the first public USB control
  implementation for a Neewer light. MIT. v1.0.0. Last push 2026-05-12.

---

## Home Assistant integrations

BLE, unless noted. [neewerd](#from-this-project) (above) reaches Home Assistant a different
way: MQTT Discovery — `light` entities per tube/group appear automatically against a broker.

- **[darinlarimore/neewer-ble-homeassistant](https://github.com/darinlarimore/neewer-ble-homeassistant)**
  — Python. Custom integration for BLE control (brightness, CCT, RGB, auto-discovery); tested
  on MS150B, lists MS60C / RGB660(PRO) / RGB480/530 / SL-80 / SNL-660 / GL1 / CB100C / CB300B
  / RGB1 / TL60 RGB as expected. Derived from NeewerLite / NeewerLite-Python. MIT. 5 stars.
  v0.2.1, last push 2026-01-23. Custom-repo install (not in the HACS default store).
- **[shyndman/hass-neewer](https://github.com/shyndman/hass-neewer)** — Python. Custom
  component (on/off, brightness, CCT, RGB, scenes); reads capabilities from a remote
  specifications database rather than hardcoded per-model logic. MIT. 5 stars. Last push
  2026-05-30.
- **[ARautio/neewer_bt](https://github.com/ARautio/neewer_bt)** — Python. Proof-of-concept
  integration for the **TL40** tube (power + brightness + CCT only); reconnects before each
  state change. Credits NeewerLite-Python. Apache-2.0. 1 star. v0.2.0, last push 2026-02-13.
- **[ondrejvysek/HomeAssistant-Neewer-TL_TubeLight-BLE](https://github.com/ondrejvysek/HomeAssistant-Neewer-TL_TubeLight-BLE)**
  — ESPHome-YAML. Exposes Neewer tube lights as native HA light entities via an ESPHome BLE
  client (on/off, brightness, RGB, multi-tube grouping, keepalive). Tested against **TL120C
  and TL60 RGB** — directly this repo's target hardware. Experimental. Last push 2025-12-30.
  *Protocol overlap: states service `69400001-…` and command char `69400002-…`, matching this
  repo's write characteristic.*
- **[kgleeson/NeewerGL25BHASS](https://github.com/kgleeson/NeewerGL25BHASS)** — Python. HA
  integration for the **GL25B over its USB HID dongle** (not BLE), local-only; derives packet
  handling from AugusDogus/neweer-tray's HID writeup.
- **[MrCurlsTTV/hacs-neweer-gl1](https://github.com/MrCurlsTTV/hacs-neweer-gl1)** — Python.
  HACS custom-repository integration for the **WiFi** GL1 Pro (UDP/5052, subnet auto-discovery via
  a checksummed handshake; identifies a light by a `80 03 00 83` response). On/off,
  brightness, CCT 2900–7000 K. MIT. v1.0.4, last push 2026-06-26.
- **[doneverhart/neewer-gl1-pro-ha](https://github.com/doneverhart/neewer-gl1-pro-ha)** —
  Python. HA custom component for the WiFi GL1 Pro. No README/description content found.

### Homebridge / HomeKit (adjacent)

- **[coachjamesgunaca/homebridge-neewer-lights](https://github.com/coachjamesgunaca/homebridge-neewer-lights)**
  — TypeScript, **BLE**. See [Control libraries → JS/TS](#javascript--typescript). MIT.
- **[jamesbretz/homebridge-neewer](https://github.com/jamesbretz/homebridge-neewer)** —
  JavaScript, **WiFi/UDP-5052**. HomeKit plugin for GL25C / GL1-class lights. Documents
  registration `80 02 10 00 00 [len] [ip-ascii] 2e`, heartbeat `80 04 84`, power-on
  `80 05 02 01 01 89` / off `80 05 02 01 00 88`, RGBCW `80 05 07 07 [bri][R][G][B][C][W][cs]`,
  checksum = sum mod 256. MIT. Last push 2026-03-19.

---

## ESPHome / microcontroller bridges

- **[mplogas/bleewerlite-esphome](https://github.com/mplogas/bleewerlite-esphome)** — C++ /
  Python (ESPHome external component). ESP32 → HA BLE bridge with brightness / CCT / RGB and
  17 built-in hardware effects; ships a specs DB of 48 models with a generic RGB+CCT fallback.
  Confirmed on GL1-Pro, HS60C, and HB80C (an "Infinity protocol variant," 2500–7500 K).
  50 ms write rate-limit; ~3 concurrent BLE clients per ESP32; no state readback. MIT.
  13 stars. Last push 2026-03-23. *Protocol overlap: documents start byte `0x78`, HSI `0x86`,
  CCT `0x87`/`0x8F`, pixel `0xB0`, and GATT UUID `69400002-b5a3-f393-e0a9-e50e24dcca99` —
  the strongest independent third-party corroboration of this repo's opcode table.*
- **[litui/esphome-components](https://github.com/litui/esphome-components)** — C++ / Python.
  ESPHome external component exposing an RGB660 as a `neewerlight` platform light via a
  persistent ESP32 BLE link. MIT. 15 stars. Last push 2025-03-15.
- **[DanielBaulig/neewer-controller](https://github.com/DanielBaulig/neewer-controller)** —
  ESP32 firmware (ESPHome-based). A board that mounts on and is powered by an RGB660-family
  light, holds a persistent BLE link, and re-exposes it over WiFi/REST for Home Assistant and
  Bitfocus Companion. Targets RGB660 / 660 Pro / Pro II. Credits Aria Barrel's and Xu Lian's
  ESPHome BLE work. 10 stars, 6 releases (v0.2.3). Last push 2025-03-15. Also sold as an
  assembled module on Tindie (by Rarely Unplugged) — the same open-source project, pre-built.
- **[cjweidman/esphome_neewer](https://github.com/cjweidman/esphome_neewer)** — ESP32 /
  ESPHome bridge for the RGB660 (same lineage as neewer-controller) with a web pairing UI and
  REST exposure. Last push 2026-03-24.
- **[Xieo/ESP32-C3-Home-Assistant-Neewer-SNL660](https://github.com/Xieo/ESP32-C3-Home-Assistant-Neewer-SNL660)**
  — C++ / ESPHome config targeting the SNL660, exposed to HA. Last push 2026-02-11.
- **[Xieo/ESP32-C3-Home-Assistant-Neewer-RGB-1200](https://github.com/Xieo/ESP32-C3-Home-Assistant-Neewer-RGB-1200)**
  — C++ / ESPHome config for the **RGB-1200** over an ESP32-C3 BLE bridge, exposed to HA (light
  MAC + WiFi credentials in YAML). Same author as the SNL660 config. MIT.
- **[dsjodin/studio_lights](https://github.com/dsjodin/studio_lights)** — C++ (ESP32-C3). A web
  portal that drives **Neewer 288ARC** lights (over a 2.4 GHz **A7105** SPI radio) and Weeylite
  RB9 lights (BLE iBeacon) from one board and UI. Same author as `neewer_light_288ARC`.
- **[dsjodin/neewer_light_288ARC](https://github.com/dsjodin/neewer_light_288ARC)** — C++
  (ESP32 / PlatformIO). Controls the **288ARC** studio light over an **A7105 2.4 GHz** module
  (not BLE — the 288ARC is a proprietary-RF fixture); ships its own
  `studio_light_remote_protocol.md`. Last push 2026-05-09.
- **[jurihock/NeewerLedMod](https://github.com/jurihock/NeewerLedMod)** — Arduino Nano. A
  hardware mod driving PWM/MOSFETs to control a **BP300** panel's LEDs directly, bypassing the
  BLE control path entirely. GPL-3.0. Last push 2026-04-28.

---

## DMX / Art-Net / sACN / OSC / MIDI bridges

[neewerd](#from-this-project) (above) receives **Art-Net** and **sACN / E1.31** and drives the
tubes over BLE with rate-limited writes; it also takes OSC.

- **[amattas/neewer-sacn](https://github.com/amattas/neewer-sacn)** — Python. sACN / E1.31 →
  BLE bridge (a lighting console drives Neewer lights with no physical DMX adapter). Ships the
  three-variant protocol spec noted under [Protocol documentation](#protocol-documentation-elsewhere).
  MIT (see caveat above). Last push 2026-06-25. Live-tested TL120C and PL60C.
- **[iKadmium/sacn-neewer-lite](https://github.com/iKadmium/sacn-neewer-lite)** — Rust.
  Operates Neewer BLE lights from sACN. MIT. Last push 2025-01-16.
- **[iKadmium/sacn-neewer-lite-go](https://github.com/iKadmium/sacn-neewer-lite-go)** — Go.
  Listens for sACN / E1.31 multicast and forwards to Neewer BLE lights via a simple 3-channel
  R/G/B mapping; a Go companion to the same author's Rust project. v0.1.2, last push
  2024-10-15.
- **[null-part/neewerserver](https://github.com/null-part/neewerserver)** — Python. A UDP →
  BLE bridge server (port 1664) for driving generic "Neewer RGB Light" panels from
  TouchDesigner / MIDI. GPL-3.0. Experimental.
- **[dumast/midi-light-controller](https://github.com/dumast/midi-light-controller)** —
  Python. Routes MIDI from Logic Pro (via the macOS IAC Driver and `mido`) into a BLE app
  (`bleak`) with HSI / CCT / scene / per-note mapping modes. Targets the **CB100C**. Last push
  2026-04-03. Uses name prefixes `("NEEWER", "NW-", "SL", "NWR")` and has an `--infinity` flag.

No dedicated TouchOSC-layout or QLC+ fixture project for Neewer was found (see [Gaps](#gaps));
for Art-Net the only receiver found is [neewerd](#from-this-project)'s module.

---

## OBS / Stream Deck / streaming-tool integrations

- **[josesj/neewer-obs-bridge](https://github.com/josesj/neewer-obs-bridge)** — Python 3.11+.
  Listens to OBS Studio's WebSocket v5 API and cross-fades one or more **Neewer 660 Pro**
  panels between per-scene lighting profiles (stepped RGB at 30+ steps/sec with easing); also
  runs an HTTP server for Stream Deck. Last push 2026-06-22. README references a "verified
  RGB660 PRO packet format."
- **[coachjamesgunaca/neewer-streamdeck](https://github.com/coachjamesgunaca/neewer-streamdeck)**
  — TypeScript. Stream Deck plugin that talks to a Homebridge instance's local HTTP API rather
  than BLE directly (Stream Deck → HTTP → Homebridge → BLE); depends on
  `homebridge-neewer-lights`. MIT. Last push 2026-06-27.
- **[multiplaie/NeewerStreamDeckPlugin](https://github.com/multiplaie/NeewerStreamDeckPlugin)**
  — C# (Elgato SDK). Stream Deck plugin exposing temperature and HSL control, including
  dual-light configs. 1 star. Last push 2022-02-01.
- **[DanielBaulig/neewer-controller](https://github.com/DanielBaulig/neewer-controller)** —
  ESP32 firmware whose README documents control from **Bitfocus Companion** via its REST API
  (also under [ESPHome bridges](#esphome--microcontroller-bridges)).

The macOS app [keefo/NeewerLite](https://github.com/keefo/NeewerLite) and its fork
[TheASDM/Beacon](https://github.com/TheASDM/Beacon) both bundle their own Elgato Stream Deck
plugins.

---

## Reverse-engineering writeups

- **[JDogHerman — RGB660 PRO BLE RE notes](https://gist.github.com/JDogHerman/483b4e56537892cb2089a63cc12e5631)**
  (GitHub gist, updated 2025-06-21). BLE frame notes for the RGB660 PRO. *Protocol overlap:
  service `69400001-…`, write char `69400002-…`, preamble `0x78`, additive low-byte checksum;
  HSV `78 86 04 [hue][hue_hi][sat][lum][cs]`, CCT `78 87 02 [bri][temp][cs]`, power
  `78 81 01 01 FB` / `78 81 01 02 FC`, and named scenes. Independent corroboration of this
  repo's `0x86`/`0x87` opcodes and checksum rule from a separate researcher.*
- **[AugusDogus/neweer-tray](https://github.com/AugusDogus/neweer-tray)** — the repo doubles
  as a writeup of reverse-engineering the **2.4 GHz USB dongle** (VID `0x0581` / PID `0x011D`)
  by hooking `Send_RF_DATA` inside Neewer's `Set_Dongle_RF_API_x64.dll` with Frida. A third,
  distinct control path (USB HID, not BLE, not the RF remote).
- **[Tom Clement — Reverse Engineering the Neewer 660 Keylight's Remote](https://web.archive.org/web/20241213122350/https://www.inthenameofscience.nl/reverse-engineering-neewer-660-keylight-remote-control/)**
  (inthenameofscience.nl, via archive.org; also syndicated on
  [Hackster.io](https://www.hackster.io/news/tom-clement-reverse-engineers-neweer-s-wireless-light-controls-to-ease-rapid-setup-a9ff5c445ab8)).
  Reverse-engineers the **NL660-2.4 panel's 2.4 GHz RF remote (RT-100)**: identifies an
  **STM8L152C6T6** MCU driving an **SI24R1** (nRF24L01 clone), and — the STM8 flash being
  read-protected — sniffs the **STM8↔SI24R1 SPI bus** with a logic analyzer to recover both the
  radio setup and the packet protocol. Radio: 2410 MHz (channel 10), GFSK 250 kb/s, 3-byte
  address `0xD35C6E`, CRC16, 32-byte payload, no auto-ack. *Protocol overlap with the BLE line:
  the 2.4 GHz payload is `77 <panel> 01 <cmd> <value> …` — a `0x77` preamble (vs the BLE `0x78`),
  `0x82` brightness / `0x83` temperature commands (matching the BLE opcodes), and a panel-target
  byte (0–10, or 88 for all). No security; commands are replayable.* Reproduced with an Arduino
  Nano + NRF24L01. A third independent confirmation of the SI24R1 for the 2.4 GHz radio.

---

## Related hardware

Neewer non-light devices that share the BLE ecosystem.

- **Neewer DL200 camera dolly** — a motorised camera dolly (not a light) that speaks over the
  **same `6940…` GATT service** as the tubes, confirming the service spans Neewer's whole BLE
  line. Documented modes: Manual / Live / Timelapse, with a 5-second status/heartbeat packet.
  - **[Tida-Support/Neewer-DL200](https://github.com/Tida-Support/Neewer-DL200)** — JavaScript.
    BLE protocol documentation for the DL200. GPL-3.0. 1 star.
  - **[Everlast Engineering — a web controller for the Neewer DL series](https://everlastengineering.com/neewer-dl200/)**
    — a web-based Bluetooth controller for the DL-200 dolly with features beyond the official
    app. Code at [github.com/everlastEngineering](https://github.com/everlastEngineering/).

---

## Protocol documentation elsewhere

- **[amattas/neewer-sacn — docs/protocol.md](https://raw.githubusercontent.com/amattas/neewer-sacn/main/docs/protocol.md)**
  — a structured three-variant spec: **Infinity** (MAC-addressed 12+ byte envelope
  `78 [tag][size][mac×6][subtag][params][cs]`; TAGs Power `0x8D`, CCT `0x90`, HSI `0x8F`,
  Scene `0x91`, Device-Info `0x9E`, Gel `0xAD`, RGBCW `0xA9`, XY `0xB7`, plus channel/group
  broadcast frames keyed on a 4-byte LE `NetworkId`), **Extended Legacy** (adds a
  green-magenta byte: `78 87 03 [bri][cct][gm][cs]`), and **Legacy** (`78 81 01 01 FB` power
  ON etc.). Documents 18 scene/effect IDs and the OFF→ON power-cycle required before a scene
  change on Infinity devices. Claims MAC validation is not enforced — a checkable claim that
  refines this repo's "TL120C by-MAC-only" finding.
- **[matthobby/neewer-lib](https://github.com/matthobby/neewer-lib)** — documents both the
  `0x78` "Studio" protocol and the `0x7A` "Neewer Home" protocol (NH-PD, NS02, music-reactive
  strip modes) over the same `6940…` GATT triad. MIT.
- **[coachjamesgunaca/homebridge-neewer-lights](https://github.com/coachjamesgunaca/homebridge-neewer-lights)**
  — README documents `[0x78][tag][length][payload][checksum]` with `0x87` CCT and `0x86` HSI.
- **[keefo/NeewerLite — Docs/](https://github.com/keefo/NeewerLite)** — the app's wiki
  (`Neewer-Light-Protocol.md`, `Neewer-Home-Protocol.md`) documents the `0x78` protocol
  (CB60 RGB, GL1C, RGB62) and the separate `0x7A` "NEEWER Home" protocol for the `NH-*` line.

---

## Fork lineage

The "NeewerLite" family has a clear root and two lineages:

- **Root — [keefo/NeewerLite](https://github.com/keefo/NeewerLite)** (macOS / Swift, Xu Lian,
  MIT, 158 stars). The original community app; carries the `0x78` protocol knowledge that
  everything else builds on. 22 GitHub forks (most stale).
  - **[TheASDM/Beacon](https://github.com/TheASDM/Beacon)** — a SwiftUI rewrite/fork with an
    expanded 74-type device DB and Stream Deck+ plugin.
- **Python port — [taburineagle/NeewerLite-Python](https://github.com/taburineagle/NeewerLite-Python)**
  (Python / PySide, MIT, 89 stars) — "originally based off the NeewerLite macOS Swift project
  by @keefo." 21 forks; the meaningfully divergent ones:
    - **[PoizenJam/NeewerLux](https://github.com/PoizenJam/NeewerLux)** — renamed, ~21 commits
      ahead; keyframe animation engine, presets, visual editor, animation HTTP API. The
      standout derivative.
    - **[astaroph/NeewerLite-Python-MCZ](https://github.com/astaroph/NeewerLite-Python-MCZ)** —
      1 commit ahead ("Add new light models").
    - **[verygeeky/NeewerLite-Python](https://github.com/verygeeky/NeewerLite-Python)** —
      re-aimed as a client of the `neewerd` daemon (socket / HTTP API) instead of a standalone
      BLE controller, with the UI extended toward full opcode coverage.
    - Several near-mirrors (Cyb3rDuke, zansilu-byte, zoltak) are only 1–2 trivial commits
      ahead and not independent projects.
- **Independent Python lib of the same name** —
  [Electronics/NeewerLitePython](https://github.com/Electronics/NeewerLitePython) is a
  *separate* project (borrows from `ha-triones` + the macOS `NeewerLite`), not a fork of
  taburineagle's repo — do not conflate the two.
- **Downstream RE credit chains, not forks:** ratmice/neewerctl (Rust), several HA
  integrations (darinlarimore, ARautio), and the ESPHome components (mplogas, DanielBaulig,
  litui) all credit keefo and/or taburineagle for the protocol without sharing code — a
  citation lineage rather than a fork lineage.

---

## Gaps

Searched for and not found — integrations that do not appear to exist yet:

- **Bitfocus Companion** — no `companion-module-neewer-*` module, and no open request in
  `bitfocus/companion-module-requests`.
- **QLC+ / TouchOSC** — no Neewer QLC+ fixture profile or `.tosc` layout; searches returned
  only generic tooling. (Art-Net itself is covered by [neewerd](#from-this-project).)
- **@4noxx GL25B USB-HID** — cited secondhand in `kgleeson/NeewerGL25BHASS`'s credits, but the
  repository could not be located.
