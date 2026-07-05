# TL90C

A 90 cm RGB tube in Neewer's Infinity tube line (light-type 95). Behaviourally close to the
[TL120C](tl120c.md), with a few differences noted below.

| | |
|---|---|
| Light-type | 95 |
| Advertised serial | `NW-20240012` |
| Firmware seen | 1.1.9, 1.1.11 |
| CCT range | 2500–10000 K |
| Battery pack | 10.8 V / 6000 mAh / 64.8 Wh (confirmed) |
| Charger | 20 V / 3.6 A / 72 W barrel PSU, Neewer model **NT72-200360D2** ([label](../assets/neewer-psu-nt72-200360d2-20v-3.6a-72w.jpg)) — distinct from the pack voltage |
| Wired DMX512 port | Yes (via NC005/NC006 RJ45-to-XLR cable) |
| Wireless DMX (2.4G) | No |

## Capabilities

- **HSI, CCT (with GM), RGBCW, CIE xy, gel** — full colour.
- **Pixel effects** (`pixelEffectClassify` 1) — vendor-listed but **unverified** on the TL90C.
  Neewer's configuration marks the TL90C pixel-capable; pixel rendering is confirmed only on the
  TL120C.
- **No Streamer / FX-Flow.**

## Protocol notes

- **Extended colour is by-MAC only**, the same as the rest of the TL tube line: direct RGBCW
  (`0xA8`) and direct xy (`0xB9`) are ignored; by-MAC RGBCW (`0xA9`), xy (`0xB7`), and gel
  (`0xAD`) render. Basic power/HSI/CCT work as direct frames.
- **No colour-accept ACK.** Unlike the TL120C-2, the TL90C emits no `0x05` reply on a colour
  change — not for by-MAC frames, and not even for a known-good direct HSI. The
  accept/ignore signal available on the TL120C-2 does not exist here.
- **Temperature/fan query (`0xB3`).** The TL120C ignores it; some other fixtures reply `0x12`
  with temperature. Whether the TL90C answers is not verified.
- **Firmware generations.** Both 1.1.9 (older) and 1.1.11 map to the TL90C; the ASCII tail in
  the version reply is a firmware generation, not a model, so it cannot distinguish these — the
  advertised serial `NW-20240012` is the identifier.

## Grouping and provisioning

The TL90C follows the `networkId` provisioning behaviour described in
[../provisioning.md](../provisioning.md): one-shot enrollment from an unassigned state, the
`0x7F` ACK confirmation, the reset-via-`❋/2.4G`-hold procedure, and using the id as a
persistent `0x009000NN` unit id. Two TL90Cs on the same 2.4G dial channel sync
fixture-to-fixture in 2.4G mode, independent of their BLE `networkId`.

## Firmware

Delivered by the custom `0x78` block OTA protocol. Latest published version 1.1.11. See
[../firmware.md](../firmware.md).
