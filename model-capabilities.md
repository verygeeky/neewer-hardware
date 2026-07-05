# Model capability matrix

Capability flags for the ~108 Neewer models in the Infinity / BLE family. If you are here to
learn about a specific light and what you can do with it, start at the
[README](README.md) instead; for a detailed write-up of a fixture, see the per-model pages in
[models/](models/). This page is the reference table.

## How to read the matrix

Each row is a model with its integer light-type and latest known firmware version. The
per-model behaviour keys off the integer light-type.

Columns:

- **CCT** — colour-temperature range.
- **HSI** — full hue/saturation/intensity colour. `—(bi)` marks a bi-colour fixture (CCT
  only, no colour). `◑noRGB` marks a fixture whose HSI flag is set but which does not support
  RGB colour.
- **RGBCW** — direct red/green/blue/cold-white/warm-white control.
- **xy** — CIE xy colour-coordinate control.
- **GM** — green/magenta tint adjustment.
- **pixFX** — pixel-effect family (`—` none, or the effect class 1/2/3), as declared in the
  vendor config. Confirmed on hardware only for the TL120C; a `1` on the TL90C/TL60 is
  vendor-listed but **not yet bench-confirmed** (see the model pages).
- **Strmr** — the Streamer / FX-Flow feature.
- **DMX512-in** — a wired hardware DMX port (RJ45 or native 5-pin). See [dmx.md](dmx.md).
- **wDMX-2.4G** — Neewer's proprietary 2.4G "Wireless DMX" mode.
- **2.4G/grp** — participates in 2.4G grouping.
- **batt** — the app's battery **tier/bar indicator** (3, 4, or 5; `—` = mains or no battery).
  This is the vendor UI's tier, **not a literal cell count** — the TL90C and TL120C both show
  5 despite different pack voltages (10.8 V vs 14.4 V). The packs are **built-in
  (non-removable)**.
- **fan** — has an active cooling fan.
- **OTA** — firmware-update transport (`OTA` = custom `0x78` block protocol, `DFU` = Nordic
  DFU). See [firmware.md](firmware.md).

Legend: `✅` yes · `✅†` inherited from a hardware-identical confirmed base variant · `—` no ·
`?` not individually verified.

## Confidence

The capability flags come from the vendor app's own per-model configuration table joined with
its firmware catalogue, cross-checked against product documentation for the physical features
(the wired-DMX port in particular). Flags for the tube fixtures are additionally confirmed on
hardware, except **pixel on the TL90C/TL60** — vendor-listed but not yet bench-confirmed (the
TL120C's pixel is confirmed). A `?` in the `DMX512-in` column means "not individually verified against a product
page", not "confirmed absent" — the wired port is common across the pro line but was not
checked for every model.

Two capabilities are narrower than one might expect:

- **Wireless DMX (2.4G)** is exactly two models: **TL98C** (type 96) and **HS60C PRO** (type
  208).
- **Streamer / FX-Flow** is exactly two models: **TL60 RGB-2** (type 59) and **TL60 RGB-3**
  (type 115). The base TL60 RGB (type 32) does not have it.

---

## The matrix

### AP

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| AP100B | 117 | 0.2.5 | 2500–8500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | ✅ | OTA |
| AP100C | 102 | 0.3.5 | 2500–10000K | ✅ | ✅ | ✅ | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| AP150B | 118 | 1.0.3 | 2500–8500K | —(bi) | — | — | — | — | — | ✅ | — | ✅ | — | ✅ | OTA |
| AP150C-2 | 83 | 3.0.2 | 2500–10000K | ✅ | ✅ | ✅ | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| AP150C-3 | 204 | 3.3.4 | 2500–10000K | ✅ | ✅ | ✅ | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |

### AS

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| AS1200B | 232 | 1.5.1 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | ✅ | OTA |
| AS600B | 58 | 1.21.26 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | ✅ | OTA |
| AS600B-2 | 110 | 1.28.33 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | ✅ | OTA |
| AS600C | 214 | 1.6.29 | 2500–7500K | ✅ | ✅ | ✅ | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |

### BH

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| BH-30S RGB | 26 | 1.2.1 | 2500–10000K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| BH-30S RGB-2 | 42 | 2.2.9 | 2500–10000K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| BH20C | 90 | 1.0.7 | 2500–10000K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| BH20C-2 | 112 | 2.0.3 | 2500–10000K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| BH40C | 61 | 2.0.3 | 2500–10000K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | — | — | OTA |

### CB

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| CB100C | 49 | 1.7.5 | 2700–6500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | — | — | OTA |
| CB120B | 84 | 1.2.5 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| CB120B-2 | 201 | 2.2.5 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| CB200B | 28 | 1.1.5 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| CB200B PRO | 103 | 1.1.15 | 2700–6500K | —(bi) | — | — | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| CB200B Pro-3 | 207 | 3.0.5 | 2700–6500K | —(bi) | — | — | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| CB200C-2 | 211 | 1.2.28 | 2500–7500K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| CB300B | 47 | 1.0.24 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| CB300C | 82 | 1.2.23 | 2500–7500K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| CB300C-2 | 225 | 2.0.15 | 2500–7500K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| CB60 RGB | 22 | 1.2.3 | 2700–6500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | — | — | OTA |
| CB60B | 31 | 1.2.6 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |

### CL

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| CL124 RGB(II) | 56 | 1.2.1 | 2500–10000K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| CL124-RGB | 19 | 1.2.4 | 2500–10000K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |

### DL

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| DL200 | 35 | 1.4.3 | n/a | —(bi) | — | — | — | — | — | — | — | — | — | — | OTA |
| DL300 | 41 | 1.1.1 | n/a | —(bi) | — | — | — | — | — | — | — | — | — | — | OTA |
| DL400 | 93 | 1.1.8 | n/a | —(bi) | — | — | — | — | — | — | — | — | — | — | OTA |

### ER

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| ER1 | 23 | 1.3.1 | n/a | —(bi) | — | — | — | — | — | — | — | — | — | — | OTA |

### FL

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| FL100C | 108 | 1.0.43 | 2500–10000K | ✅ | ✅ | ✅ | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |

### FS

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| FS100B | 231 | 1.1.10 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | ✅ | OTA |
| FS150 5600K | 54 | 1.35.9 | 5600K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| FS150B | 37 | 1.33.32 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| FS150C | 75 | 1.1.11 | 2500–7500K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| FS230 5600K | 53 | 1.41.7 | 5600K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| FS230B | 55 | 1.41.15 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| FS300C-2 | 210 | 1.3.4 | 2500–7500K | ✅ | ✅ | ✅ | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| FS600B | 221 | 1.1.4 | 2700–6500K | —(bi) | — | — | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| FS600B-2 | 227 | 2.0.6 | 2700–6500K | —(bi) | — | — | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| FS600C | 209 | 1.2.17 | 2500–7500K | ✅ | ✅ | ✅ | ✅ | — | — | ✅ | — | ✅ | — | ✅ | OTA |
| FS600C-2 | 226 | 2.0.15 | 2500–7500K | ✅ | ✅ | ✅ | ✅ | — | — | ✅† | — | ✅ | — | ✅ | OTA |
| FS60B | 230 | 1.1.20 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | ✅ | OTA |

### GL

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| GL1 PRO | 33 | 1.0.9 | 2900–7000K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| GL1C | 39 | 1.1.10 | 2900–7000K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | — | — | OTA |
| GL25C | 97 | 1.1.6 | 2900–7000K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | — | — | OTA |
| GL25C-2 | 111 | 2.0.2 | 2900–7000K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | — | — | OTA |

### GR

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| GR18C | 62 | 0.4.9 | 2500–8500K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |

### HB

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| HB60B | 228 | 1.0.16 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | 4 | — | OTA |
| HB80B | 206 | 1.2.10 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | 4 | — | OTA |
| HB80C | 106 | 1.3.35 | 2500–7500K | ✅ | ✅ | ✅ | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| HB80C-2 | 223 | 2.0.15 | 2500–7500K | ✅ | ✅ | ✅ | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |

### HS

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| HS200B-2 | 222 | 2.2.9 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | ✅ | OTA |
| HS200C | 105 | 1.4.3 | 2500–7500K | ✅ | ✅ | ✅ | ✅ | — | — | ✅ | — | ✅ | — | ✅ | OTA |
| HS200C-2 | 220 | 2.7.4 | 2500–7500K | ✅ | ✅ | ✅ | ✅ | — | — | ✅† | — | ✅ | — | ✅ | OTA |
| HS60B | 66 | 1.4.6 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| HS60C PRO | 208 | 1.0.17 | 2700–6500K | ✅ | ✅ | ✅ | ✅ | — | — | ? | ✅ | ✅ | — | — | OTA |

### MS

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| MS150 | 79 | 1.0.9 | 5600K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | ✅ | OTA |
| MS150B | 38 | 1.0.22 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | ✅ | OTA |
| MS150C | 73 | 1.2.4 | 2700–6500K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| MS150C-2 | 98 | 2.1.0 | 2700–6500K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | — | ✅ | OTA |
| MS60B | 30 | 1.2.38 | 2700–6500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| MS60C | 25 | 1.1.59 | 2700–6500K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | — | — | OTA |
| MS60C-2 | 70 | 1.0.7 | 2700–6500K | ✅ | ✅ | — | ✅ | — | — | ? | — | ✅ | — | — | OTA |

### PL

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| PL60B | 116 | 0.1.7 | 2500–8500K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | ✅ | OTA |
| PL60C | 60 | 1.3.3 | 2500–10000K | ✅ | ✅ | ✅ | ✅ | — | — | ✅ | — | ✅ | — | ✅ | OTA |
| PL60C-2 | 119 | 2.1.1 | 2500–10000K | ✅ | ✅ | ✅ | ✅ | — | — | ✅† | — | ✅ | — | ✅ | OTA |

### PS

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| PS099S | 113 | 1.3.6 | n/a | —(bi) | — | — | — | — | — | — | — | — | — | — | OTA |

### Q

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Q120 | 235 | 0.1.6 | n/a | —(bi) | — | — | — | — | — | — | — | — | — | — | OTA |
| Q200 | 68 | 0.1.9 | n/a | —(bi) | — | — | — | — | — | — | — | — | — | — | OTA |
| Q4Pro | 240 | 1.0.4 | n/a | —(bi) | — | — | — | — | — | — | — | — | — | — | OTA |
| Q6 | 114 | 1.0.95 | n/a | —(bi) | — | — | — | — | — | — | — | — | — | — | OTA |

### R

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| R360 | 48 | 1.1.1 | n/a | —(bi) | — | — | — | — | — | — | — | — | — | — | OTA |

### RGB

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| RGB C80 | 21 | 1.3.5 | 2500–8500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| RGB1 | 8 | 1.1.56 | 3200–5600K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | DFU |
| RGB1200 | 18 | 1.2.23 | 2500–8500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| RGB1200-2 | 43 | 2.1.54 | 2500–8500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| RGB1200-3 | 71 | 3.1.8 | 2500–8500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| RGB1200-4 | 88 | 4.0.7 | 2500–8500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| RGB168 | 16 | 1.1.5 | 2500–8500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| RGB176 A1 | 20 | 1.1.16 | 2500–10000K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| RGB2 | 85 | 0.4.9 | 2500–10000K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | DFU |
| RGB62 | 40 | 0.2.7 | 2500–8500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 3 | — | OTA |

### RH

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| RH100B | 205 | 1.2.12 | 2900–7000K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | ✅ | OTA |

### RL

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| RL45B | 52 | 1.21.27 | 2900–7000K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| RL45B-2 | 77 | 1.21.29 | 2900–7000K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| RL45C | 94 | 1.30.41 | 2500–10000K | ◑noRGB | ✅ | — | ✅ | — | — | ? | — | ✅ | — | — | OTA |

### RP

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| RP18B PRO-2 | 72 | 2.0.2 | 2900–7000K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |

### SL

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| SL90 | 14 | 1.2.7 | 2500–10000K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | — | — | OTA |
| SL90 Pro | 34 | 1.1.6 | 2500–10000K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |
| SL90 Pro-2 | 202 | 1.2.1 | 2500–10000K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 4 | — | OTA |

### SRP

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| SRP18C | 76 | 1.1.9 | 2500–10000K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | — | — | OTA |

### T

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| T100C | 44 | 1.2.1 | 2700–6500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | — | — | OTA |

### TL

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| TL120C | 50 | 1.2.8 | 2500–10000K | ✅ | ✅ | ✅ | ✅ | 1 | — | ✅ | — | ✅ | 5 | — | OTA |
| TL120C-2 | 101 | 2.0.5 | 2500–10000K | ✅ | ✅ | ✅ | ✅ | 1 | — | ✅† | — | ✅ | 5 | — | OTA |
| TL21C | 69 | 0.1.3 | 2500–8500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 3 | — | OTA |
| TL40 | 67 | 1.7.0 | 2900–7000K | —(bi) | — | — | — | — | — | ? | — | ✅ | — | — | OTA |
| TL60 RGB | 32 | 1.3.10 | 2500–10000K | ✅ | ✅ | — | ✅ | 1 | — | ✅ | — | ✅ | — | — | OTA |
| TL60 RGB-2 | 59 | 2.4.8 | 2500–10000K | ✅ | ✅ | — | ✅ | 1 | ✅ | ✅† | — | ✅ | 5 | — | OTA |
| TL60 RGB-3 | 115 | 3.0.5 | 2500–10000K | ✅ | ✅ | — | ✅ | 1 | ✅ | ✅† | — | ✅ | 5 | — | OTA |
| TL90C | 95 | 1.1.11 | 2500–10000K | ✅ | ✅ | ✅ | ✅ | 1 | — | ✅ | — | ✅ | 5 | — | OTA |
| TL97C | 64 | 0.3.1 | 2500–8500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 3 | — | OTA |
| TL97C-2 | 218 | 0.2.1 | 2500–8500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 3 | — | OTA |
| TL98C | 96 | 1.0.1 | 2500–10000K | ✅ | ✅ | — | ✅ | — | — | — | ✅ | ✅ | 4 | — | OTA |

### VL

| Model | type | fw | CCT | HSI | RGBCW | xy | GM | pixFX | Strmr | DMX512-in | wDMX-2.4G | 2.4G/grp | batt | fan | OTA |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| VL67B | 100 | 0.1.5 | 2500–8500K | —(bi) | — | — | — | — | — | ? | — | ✅ | 3 | — | OTA |
| VL67C | 65 | 0.3.0 | 2500–8500K | ✅ | — | — | ✅ | — | — | ? | — | ✅ | 3 | — | OTA |
