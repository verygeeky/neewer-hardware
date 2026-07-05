# Pixel effects & Streamer

Two related effect subsystems: the **pixel** palette system (on the TL120C / TL90C and other
`pixelEffectClassify` fixtures) and the **Streamer** / FX-Flow system (TL60 only).

## Pixel effects

**There is no per-LED framebuffer command.** "Pixel" here means a palette colour index, not a
physical LED index. An effect is a named effect id plus scalar parameters plus up to eight
colour triplets; the firmware renders that effect across its LEDs. The result is
spatial — distinct colours appear as bands along the tube — but you choose an *effect plus
palette*, not raw per-pixel bytes.

### Frame wrappers

There are two effect families, `Pixel` and `Pixel12`, distinguished by opcode range and
effect-id set, each with three addressing forms:

| Transport | Pixel | Pixel12 |
|---|---|---|
| Direct BLE | `78 B2 <len> <effectData> ck` | `78 D7 …` |
| By MAC | `78 B0 <len> <MAC6> <effectData> ck` | `78 D5 …` |
| By channel | `78 B1 <len> <NET4> <CH> <effectData> ck` | `78 D6 …` |

On the TL tube line the by-MAC form (`0xB0`) is the one that renders.

### effectData

`effectData` is a list of 2–3 sub-frames, each `[effectId, subIndex, payload…]`:

- **subIndex 0** — scalar parameters: brightness, speed, direction, colour count, running
  status. For example, effect 1's parameters are `32 02 2e 01 01`.
- **subIndex 1** — palette colours 0–5, as 3-byte triplets (see the DIY colour-cell encoding
  in [protocol.md](protocol.md)).
- **subIndex 2** — palette colours 6–7, present only when the palette is large.

Each sub-frame is wrapped and written as its own GATT write, roughly 80 ms apart. Long
palettes are additionally chunked to ≤20 bytes to fit the MTU and reassembled by header.

`Pixel` versus `Pixel12` is the opcode family and effect-id range, not a pixel count in the
frame. Base `Pixel` uses wire effect ids `1–7, 10, 11, 12`; `Pixel12` uses a contiguous
`1–12`.

### Example frames

Pixel effect 1, two-colour palette (the fixture's own checksums):

```
78 b0 0d cc 8d be bb 25 b0 01 00 32 02 2e 01 01 41      parameters
78 b0 0e cc 8d be bb 25 b0 01 01 10 73 64 10 5a 64 94   palette: hue 115 + hue 90, sat 100
```

A crafted red + blue palette (renders as two bands, animated):

```
78 b0 0e cc 8d be bb 25 b0 01 01 10 00 64 10 f0 64 b7   hue 0 (red) + hue 240 (blue)
```

Observed effect ids include 1, 2, 4, 5, 6, 7. Palettes can hold three or more colour blocks,
and the off/black block `20 00 00` can appear in a palette, so an effect can leave segments
dark.

## Streamer (FX-Flow)

The feature the vendor UI calls "FX Flow" is named **Streamer** in the protocol. It is
**TL60-only** — supported on the TL60 RGB-2 (type 59) and TL60 RGB-3 (type 115), and not on
the TL120C or TL90C. A fixture self-reports support: a `0xC4` streamer-support query to a TL60
returns a `0x17` reply with value `01`; a TL120C stays silent.

On a *single* fixture the vendor app does not use Streamer at all — it streams per-MAC
`0x8F` / `0x90` colour frames instead. Streamer is a multi-fixture, group feature.

### Realtime group play — `0xC0`

```
78 C0 0A <NET4> <CH> <EE BR SP DR OS> ck        (EE=4 inserts a bgBri byte → len 0x0B)
```

`EE` = effect enum + 1, `BR` = brightness, `SP` = speed, `DR` = direction, `OS` = open state.
Sent only to the group master.

### Full multi-fixture download — `0xBF`

```
78 BF <LL HH>                      header, length little-endian
EM                                 effect mode = (effectEnum + 1) & 0xFF
[BG3]                              3-byte background packet, only if effectEnum == 3
DN MCh MCl TCh TCl                 deviceCount, modColorNum (BE16), totalColorNum (BE16)
P0 O0 S0h S0l MAC0(6)              per-fixture: position, blockCount, startOffset (BE16), MAC6
…                                  one 10-byte record per fixture
C0(3) C1(3) …                      colour blocks, 3 bytes each
KK                                 checksum = sum(all) & 0xFF
```

Each fixture's **position** is its index in the device list, and it is allocated a contiguous
run of colour blocks `[startOffset, startOffset + blockCount)`. Blocks per fixture: TL60 → 8,
TL120 → 16.

The exact effect-mode → visual-pattern mapping and the per-byte units of several effect
parameters (`sectionType`, `runStyle`, `movement`, `direction`) are data-driven from server
JSON rather than enumerated as constants, so they are not fully documented here.
