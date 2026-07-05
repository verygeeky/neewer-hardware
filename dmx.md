# DMX

**DMX isn't always DMX here.** Two completely different things share the name on these
fixtures, and they get conflated constantly:

- **Wired DMX512** — a real, standard DMX port. On the tubes it's an RJ45 jack, driven through
  an **NC005 / NC006 RJ45-to-XLR cable** ("the dongle") into an ordinary DMX console.
- **"Wireless DMX" (2.4G)** — Neewer's own 2.4 GHz group-and-channel scheme. It is *marketed*
  as "Wireless DMX" but it is not the DMX512 standard, and it is not CRMX or any W-DMX
  standard. Only two models have it.

A given model can have neither, either, or both, and the two are unrelated — neither one drives
the other. The rest of this page covers each in turn.

## Path 1 — wired DMX512

A native **hardware DMX port**. The TL tubes (TL120C, TL90C, TL60) expose an **RJ45 DMX
port**, connected to a standard DMX512 console through an **NC005 (3-pin) or NC006 (5-pin)
RJ45-to-XLR cable**. The pro panels and COB fixtures use a native **5-pin DMX port**.

This is pure hardware — **no BLE frames are involved** — and it is **not** gated by the
vendor app's `supportDmxMode` flag. Any fixture with the port does standard 512-channel DMX
directly from a console.

Product-page-confirmed wired-DMX models include: TL120C, TL90C, TL60 RGB (all via the
NC005/NC006 cable), PL60C, FS600C, AP150B, HS200C, and their `-2` / `-3` variants. The
`DMX512-in` column in [model-capabilities.md](model-capabilities.md) marks which models are
verified.

### Wired DMX512 personalities

The tubes offer two DMX personalities, selected on-device with the `MOD` button; the DMX start
address is set with `ADD` (1–512). The channel maps below are documented in the TL-series
tube-light manual and are consistent across the Neewer and Godox TL tubes. Ready-made console
profiles are a planned addition.

**Personality 1 — CCT-HSI 8-bit (5 channels)**

| Channel | Function | Value | Result |
|---|---|---|---|
| 1 | Intensity | 0–255 | 0–100% |
| 2 | Colour temperature | 0–255 | 2700–6500 K |
| 3 | Crossfade | 0–255 | 0 = CCT output, 255 = HSI output (blends CCT ↔ HSI) |
| 4 | Hue | 0–255 | 0–360° |
| 5 | Saturation | 0–255 | 0–100 |

**Personality 2 — ULTIMATE 8-bit (mode-select)**

Channel 1 selects a sub-mode, channel 2 is intensity, and channels 3+ carry that sub-mode's
parameters.

| Channel 1 (mode select) | Sub-mode |
|---|---|
| 0–51 | CCT |
| 52–103 | HSI |
| 104–156 | RGBW |
| 157–208 | FX (effects) |
| 209–255 | Gel (colour-paper) |

Sub-mode parameters (channels 3 onward):

- **CCT** — ch 3: colour temperature (0–255 → 2700–6500 K).
- **HSI** — ch 3: hue (0–255 → 0–360°); ch 4: saturation (0–255 → 0–100).
- **RGBW** — ch 3: red; ch 4: green; ch 5: blue; ch 6: white (each 0–255).
- **FX** — ch 3: effect type (table below); ch 4: speed (0–85 = level I, 86–171 = level II,
  172–255 = level III).
- **Gel** — ch 3: brand (0–127 = ROSCO, 128–255 = LEE); ch 4: gel selection within the brand
  (20 gels per brand, 40 total).

FX effect type (channel 3), across evenly divided DMX ranges:

| Value | Effect | Value | Effect |
|---|---|---|---|
| 0–18 | RGB cycle | 134–151 | Fireworks |
| 19–36 | Flash | 152–170 | Fireworks (variant) |
| 37–56 | Double flash | 171–189 | Police car |
| 57–75 | Lightning | 190–208 | Fire truck |
| 76–95 | Broken bulb | 209–228 | Ambulance |
| 96–113 | TV | 229–247 | Music |
| 114–133 | Candle | 248–255 | SOS |

**8-bit value conversions.** Intensity: percent = DMX × 0.3921. Colour temperature:
kelvin = DMX × 149 + 2700.

### Daisy-chain (master / slave)

The RJ45 DMX in/out ports daisy-chain fixtures. One fixture set to **LEAD** (`MOD` → `L/F` →
`LEAD`) drives the rest, each set to **FOLLOW** (with no DMX address); the followers mirror the
lead in real time. A chain runs up to **18 fixtures**.

## Path 2 — Neewer "Wireless DMX" (2.4G)

Marketed on the packaging as "WIRELESS DMX CONTROL". The fixture is assigned a **DMX group
(A–G)** and a **channel / address (1–512)** — on-device or via the app — and receives DMX over
Neewer's proprietary **2.4 GHz** link from a chosen host/master.

This is **not** LumenRadio CRMX or any W-DMX standard. It is Neewer's proprietary 2.4G group
and channel addressing; the "Wireless DMX" name is Neewer's own marketing for it. No Neewer
product in this catalogue uses CRMX.

Path 2 is gated by `supportDmxMode = true` in the app's configuration, which is set for
exactly two models: **TL98C** (type 96) and **HS60C PRO** (type 208). The TL98C has no
physical DMX port — its DMX is entirely this wireless mode.

The `0x78` DMX opcodes (`0xCA` / `0xC9` / `0xCB` / `0xCC`, listed in
[protocol.md](protocol.md)) configure **path 2** over BLE: enable the mode, set the group and
channel, download DMX effects, and query state. The app surfaces this UI only for
`supportDmxMode` models.

## The two are orthogonal

| | Wired DMX512 port | Wireless DMX (2.4G) |
|---|---|---|
| The tubes (TL120C, TL90C, TL60) | Yes | No |
| TL98C | No | Yes |
| HS60C PRO | Unverified | Yes |

A fixture can have the wired port without the wireless flag (the tubes), the wireless flag
without a wired port (TL98C), or in principle both. Neither one drives the other: a wired-DMX
input to one light does not appear on the 2.4G channel, and vice versa.
