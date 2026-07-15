# BLE protocol

The complete on-air Bluetooth-LE protocol for Neewer Infinity fixtures. Everything a client
needs to discover, connect to, and control a light.

## Transport & GATT

- **Discovery.** BLE scan, filtered by advertised-name prefix. The known prefixes are
  `NW`, `NWR`, `NEEWER`, `SL`, `DL`, `NH`. A fixture advertises its own MAC in the
  manufacturer-data field.
- **Connect.** No pairing, no bonding. Links are multi-central capable at the GATT level —
  more than one host can connect at once — but see the single-central caveat in
  [hardware.md](hardware.md).
- **Control service** `69400001-b5a3-f393-e0a9-e50e24dcca99`:
  - Write characteristic `69400002-b5a3-f393-e0a9-e50e24dcca99` — write / write-without-response.
    Every `0x78` command frame goes here.
  - Notify characteristic `69400003-b5a3-f393-e0a9-e50e24dcca99` — status replies arrive here.
- **OTA rides the same `69400001` service** — there is no separate service for firmware update.
- A second vendor service `7f510004` (with write characteristics `7f510005` / `7f510006`) is
  exposed by the hardware but is **never used** by any command path. It is not the OTA channel.
  Ignore it.
- **MTU is 23** (20 usable payload bytes). Frames longer than 20 bytes — OTA blocks, large
  pixel/streamer palettes — are split into ≤20-byte ATT writes and reassembled by the device
  using the frame's own length header. **Continuation chunks do not begin with `0x78`**, so a
  parser must reassemble by header, not per write.

## Frame format

```
[0]=0x78   [1]=opcode   [2]=len   [3 .. 3+len-1]=payload   [last]=checksum
```

- `len` counts **payload bytes only** — it excludes the `78 op len` header and the trailing
  checksum byte.
- **Checksum** = the sum of all preceding bytes, `& 0xFF`, written into the last byte.
- **OTA exception:** on fixtures of device-class 6, the leading header byte is `0x85` instead
  of `0x78`.

There is no framing beyond this: no length-of-length, no CRC, no sequence number in the
general case. Integrity is the single additive checksum byte.

## Addressing modes

The same colour/effect operations exist in up to three addressing forms. The opcode selects
the form.

| Mode | Frame shape | Target |
|---|---|---|
| **Direct** | `78 op len <payload> ck` | the BLE-connected fixture itself |
| **By MAC** | `78 op len <MAC6> <inner> ck` | a specific fixture, addressed by its 6-byte MAC |
| **By channel** | `78 op len <NET4> <CH> <inner> ck` | a 2.4G group channel |

- `MAC6` — the 6 target-MAC bytes.
- `NET4` — the `networkId` as a little-endian 32-bit value. Some "New" builder variants send
  only the low 16 bits (`NET2`).
- `CH` — the group channel byte, a device-list-position index (not the physical 2.4G dial
  channel).

**Important behavioural note.** On the TL tube line the *extended* colour modes (RGBCW, CIE
xy, gel, scene) are **by-MAC only** — the direct forms of those opcodes are silently ignored,
and the by-MAC form is what renders. The **by-channel** forms are a 2.4G-relay path that a
BLE-connected fixture does not self-apply; over BLE, multi-fixture control is per-MAC
fan-out. See [hardware.md](hardware.md) and [provisioning.md](provisioning.md) for the full
picture. The by-channel opcodes are documented here for completeness.

## Core control commands

The everyday direct-to-fixture commands.

| Function | Frame | Notes |
|---|---|---|
| Power on | `78 81 01 01 fb` | |
| Power off | `78 81 01 02 fc` | |
| HSI / RGB | `78 86 04 <hueLo> <hueHi> <sat> <bri> +ck` | hue is LE16, 0–360; sat and bri 0–100 |
| CCT (+GM) | `78 87 04 <bri> <cct> <gm> <dimCurve> +ck` | bri 0–100; cct in hundreds-K, model-dependent range (the tubes do 25–100 → 2500–10000 K); gm 0–100, 50 = neutral; gm is signed on the device |
| CCT (no GM) | `78 87 03 <bri> <cct> <dimCurve> +ck` | older three-byte variant |
| Scene / built-in FX | `78 88 <len> <effect> <params> +ck` | see below; **not** handled by the TL120C firmware — use the by-MAC scene opcode `0x91` there |

The length byte must equal the actual payload count; a mismatched length causes the frame to be
mis-parsed. For example, CCT must be sent in the four-byte form (`len=0x04`: bri, cct, gm,
dimming-curve).

### Built-in scene / FX payloads (`0x88`)

Effect types 1–17. Each effect emits a payload after the effect id:

| # | Effect | Payload after effect id |
|---|---|---|
| 1 | Lightning | `bri, cct, rate` |
| 2 / 3 / 6 / 8 | Paparazzi / Defective Bulb / CCT Flash / CCT Pulse | `bri, cct, gm, rate` |
| 4 | Explosion | `bri, cct, gm, rate, sparks` |
| 5 / 15 | Welding / Screen (TV) | `briMin, briMax, cct, gm, rate` |
| 7 / 9 | Hue Flash / Hue Pulse | `bri, hueLo, hueHi, sat, rate` |
| 10 | Cop Car | `bri, opts, rate` |
| 11 | Candlelight | `briMin, briMax, cct, gm, rate, sparks` |
| 12 | Hue Loop | `bri, hueMinLo, hueMinHi, hueMaxLo, hueMaxHi, rate` |
| 13 | CCT Loop | `bri, cctMin, cctMax, rate` |
| 14 | INT Loop | `0, briMin, briMax, 0, 0, cct, rate` |

### DIY colour sequences

A DIY "Pic" is a user-defined sequence of colour cells the fixture cycles through. Each cell
is a 3-byte block — the same triplet used throughout the pixel/streamer payloads:

| Mode | Block | Flag byte |
|---|---|---|
| CCT | `[0x00, cct, gm]` | `0x00` |
| HSI | `[(hue>>8)&0x0F \| 0x10, hue&0xFF, sat]` | `0x10` (the high nibble flags HSI) |
| Off / black | `[0x20, 0x00, 0x00]` | `0x20` |

The transport is chosen by scope:

| Scope | Frame |
|---|---|
| Direct BLE, one fixture | `78 86 05 <hueLo> <hueHi> <sat> <bri> 00 +ck` |
| By MAC (2.4G single) | `78 8F 05 <MAC6> 86 <hueLo hueHi sat bri 00> +ck` |
| By channel (2.4G group) | `78 92 05 <NET4> <CH> 86 <…> +ck` |
| Gradient/hard-switch toggle | `78 D3 07 <MAC6> <0=smooth \| 1=hard> +ck` |

"Alternate colours" is a colour list plus `0xD3` hard-switch; "gradient" is the same list
plus `0xD3` smooth.

---

## Complete opcode reference

Header `0x78` and trailing checksum are omitted from the payload column. `MAC6` = target
MAC, `NET4`/`NET2` = networkId, `CH` = channel byte.

### Direct BLE control (no addressing)

| Opcode | Name | Payload |
|---|---|---|
| `0x81` | Power on/off | `<01 on \| 02 off>` |
| `0x82` | Brightness only | `<bri>` |
| `0x83` | CCT only | `<cct>` |
| `0x84` | Enter/request adjust mode | *(none)* |
| `0x85` | Light-state query | *(none)* → `78 85 00 FD` |
| `0x86` | HSI | `<hueLo hueHi sat bri 00>` |
| `0x87` | CCT full | `<bri cct gm curveType unit>` (2–5 bytes) |
| `0x88` | Scene / built-in FX | `<effType*3 + mode>` |
| `0x8A` | Device config request | *(none)* |
| `0xA8` | RGBCW (direct) | `<R G B C W ?? 00>` — by-MAC only on the TL tube line |
| `0xB9` | CIE-xy (direct) | `<idx xLo xHi yLo yHi i3>` — by-MAC only on the TL tube line |
| `0xAF` | Gel / colour-paper (direct) | `<paperLo paperHi i3 i4 i6 brand i5>` (ROSCO=1, LEE=2) — by-MAC only on the TL tube line |
| `0xB2` | Pixel effect (direct) | `<len> <effectData>` |
| `0xD7` | Pixel12 effect (direct) | `<len> <effectData>` |
| `0x61` | LP40s light set | `<8 bytes>` |
| `0x62` | LP40s config query | `00` |
| `0xF0` | Factory test / fan switch | `AA` |
| `0xF5` | Factory test | `CC` |

### Infinity / 2.4G — MAC-addressed

| Opcode | Name | Payload |
|---|---|---|
| `0x8D` | Power by MAC | `MAC6 81 <01\|02>` |
| `0x8E` | State / power-status read | `MAC6 85` → reply `0x04` |
| `0x8F` | DIY colour (HSI) by MAC | `MAC6 86 <hueLo hueHi sat bri 00>` |
| `0x90` | CCT/HSI effect by MAC | `MAC6 <sub> <data>` |
| `0x95` | Battery query by MAC | `MAC6` → reply `0x05` |
| `0x99` | Identify / find-light | `MAC6` |
| `0x9A` | Control slave by netid | `NET4 <01\|02>` |
| `0x9D` | Online-status query | `MAC6 NET4` → reply `0x7F` |
| `0x9E` | Version read by MAC | `MAC6` → reply `0x08` |
| `0xA4` | Dimming-curve set | `MAC6 87 <curve0 curve1 curve2> <i2>` |
| `0xA9` | RGBCW by MAC | `MAC6 A8 <bri R G B C W decBri>` |
| `0xAB` | Booster data by MAC | `MAC6 <01\|02>` |
| `0xAC` | Booster state by MAC | `MAC6` |
| `0xAD` | Gel / colour-paper by MAC | `MAC6 <hueLo hueHi> sat bri decBri brand gelNo` |
| `0xB0` | Pixel effect by MAC | `MAC6 <effectData>` |
| `0xB3` | Temperature + fan-mode query | `MAC6` → reply `0x12` (the TL120C does not answer; other fixtures do) |
| `0xB4` | Fan-mode set | `MAC6 <mode>` |
| `0xB7` | CIE-xy by MAC | `MAC6 <bri xLo xHi yLo yHi 00>` |
| `0xBD` | Double-light state query | `MAC6` → reply `0x13` |
| `0xBE` | Double-light state set | `MAC6 <i2>` |
| `0xC4` | Streamer-support query | `MAC6` → reply `0x17` |
| `0xD1` | Scheduled-tasks query | `MAC6` → reply `0x1B` |
| `0xD2` | Scheduled-tasks set | `MAC6 <en weekdays cdEn ts4 countdown4 i3>` |
| `0xD3` | DIY-gradient toggle (single) | `MAC6 <flag>` |
| `0xD5` | Pixel12 effect by MAC | `MAC6 <effectData>` |
| `0xD8` | Factory test | `MAC6 <mode> <i3 i4 i5 i6>` |
| `0xD9` | RGBW by MAC | `MAC6 <bri colorIdx>` |
| `0xA6` | Lighting-effect status (single) | `MAC6 <i2>` |

### Infinity / 2.4G — group / channel-addressed

Every by-MAC colour or effect opcode has a `<NET4><CH>` by-channel twin. These are relayed to
a 2.4G group channel and are not self-applied by a BLE-connected fixture.

| Opcode | Name | Payload |
|---|---|---|
| `0x92` | DIY colour (HSI) by channel | `NET4 CH 86 <hueLo hueHi sat bri 00>` |
| `0x93` | HSI by channel | `NET4 CH 87 <data>` |
| `0x94` | Built-in FX by channel | `NET4 CH <effect> <data>` |
| `0x98` | Power by channel (group) | `NET4 CH 81 <01\|02>` |
| `0xA7` | Lighting-effect status by channel | `NET4 CH <status>` |
| `0xAA` | RGBCW by channel | `NET4 CH A8 <bri R G B C W decBri>` |
| `0xAE` | Gel / colour-paper by channel | `NET4 CH <hueLo hueHi> sat bri decBri brand gelNo` |
| `0xB1` | Pixel effect by channel | `NET4 CH <effectData>` |
| `0xB8` | CIE-xy by channel | `NET4 CH <bri xLo xHi yLo yHi 00>` |
| `0xC0` | Streamer realtime group play | `NET4 CH <eff bri [bgBri] spd dir openState>` |
| `0xD4` | Group-activate / select | `NET4 CH 00` |
| `0xD6` | Pixel12 effect by channel | `NET4 CH <effectData>` |

### Group / network management

| Opcode | Name | Payload |
|---|---|---|
| `0x8C` | Assign channel + networkId | `MAC6 CH NET4` (New: `MAC6 CH NET2`) → reply `0x7F` |
| `0x9B` | Sync / register light | `MAC6 01 <idxLo idxHi>` → reply `0x7F` |
| `0x9F` | Add light + assign channel | `MAC6 01 CH NET4` → reply `0x7F` |
| `0xA3` | Identity check (broadcast networkId) | `NET4 <idx>` (New: `NET2 <idx>`) |

### Battery / power station / booster

| Opcode | Name | Payload |
|---|---|---|
| `0xCD` | Battery info query | `00`=all, `01`=base, `09`=power-supply → reply `0x18` |
| `0xCE` | Battery strategy / limit / port | several sub-forms → reply `0x19` |

### Firmware / OTA

See [firmware.md](firmware.md) for the full update flow, including the fragment-spacing constraint
(keep GATT fragments around 20 ms apart to avoid overrunning the two-chip UART) that a working OTA
implementation depends on.

| Opcode | Name | Payload |
|---|---|---|
| `0x80` | Version read (direct) | *(none)* → `78 80 00 F8`, reply `0x00` |
| `0xD0` | OTA-type probe | *(none)* → reply `0x1A` |
| `0x96` | OTA update-info header | `<v1 v2 v3> <size BE32> <checkCode BE32> <nameASCII>` |
| `0x97` | OTA image block, 128-byte | `<len8> <≤128 data>` |
| `0xCF` | OTA image block, 4096-byte | `<len LE16 = data+1> <≤4096 data>` |

### DMX over BLE

See [dmx.md](dmx.md). These configure Neewer's proprietary 2.4G "Wireless DMX" mode.

| Frame | Meaning |
|---|---|
| `78 CA 02 01 <EN> ck` | Enable/disable wireless DMX mode |
| `78 CA 04 02 <GG> <addrHi addrLo> 00 ck` | Set DMX group (A–G) + channel 1–512 (BE) |
| `78 CA 04 04 <mode> <play> <speed> ck` | Local DMX play |
| `78 C9 01 <i2> ck` | Query DMX mode |
| `78 CC <len> <blockIdx> <blockData> 00 ck` | DMX effect download, per-block colour |
| `78 CB <len> 00 <sizeLo sizeHi> <size4> … 07 80 …` | DMX effect download header |

---

## Reply / notification reference

Replies arrive on notify characteristic `69400003`. `b1` is the reply opcode (`bytes[1]`).

| Opcode | Meaning | Key fields |
|---|---|---|
| `0x00` | Version reply (direct) | version = bytes[5,6,7] |
| `0x02` | Power status (direct) | bytes[3]: 1 = on |
| `0x03` | WiFi state | len 5 |
| `0x04` | Device power / state-read reply | MAC at [3..8], mode/on at [9..10] |
| `0x05` | Battery reply / colour-accept ACK | MAC at [3..8], pct = `bytes[9]`; also pushed on colour-state change on some fixtures |
| `0x06` | OTA block ACK | bytes[3]: 0=next, 1=retry, 2=restart, 3=done, 4=fail |
| `0x08` | Version reply (by MAC) | version = bytes[11,12,13], ASCII model tail follows |
| `0x09` / `0x0A` | Light-effect status | `len ≥ bytes[2]+3` |
| `0x11` | Booster (CB200B) | len 11 |
| `0x12` | Temperature / fan-mode | temp = `bytes[9] - 50` |
| `0x13` | Double-light state | len 11 |
| `0x14` | Streamer download message | MAC in payload |
| `0x15` | DMX switch state | bytes[4]: 1 = on |
| `0x17` | Streamer device message / streamer-support reply | a TL60 answers the `0xC4` support query with `0x17 01` |
| `0x18` | Battery info | base if bytes[3]==1, power-supply if ==9 |
| `0x19` | Battery port-connect / strategy | bytes[3] |
| `0x1A` | OTA-type reply | len 5; bytes[3]==1 → 4096-byte OTA, else 128-byte |
| `0x1B` | Scheduled-tasks reply | len ≥ 10 |
| `0x1D` | Factory reply | — |
| `0x7F` | Generic Infinity ACK | 12-byte (`[2]==8`) or 17-byte (`[2]==13`); the executed opcode is echoed at `bytes[9]`, status at `bytes[10]` |

The `0x7F` ACK is what confirms a provisioning write landed — a fixture echoes the `0x9F` or
`0x8C` it just executed. On the TL120C-2 a `0x05` reply pair also fires whenever the fixture
accepts and applies a colour frame, and is silent when it ignores one, which makes it a
headless accept/ignore signal. That `0x05` colour-accept behaviour is TL120C-2-specific; it
is absent on the TL90C.

---

## Fixture identity

A fixture advertises as **`NW-<8-digit-serial>&<8-hex-networkId>`**, e.g.
`NW-20240012&00000000`.

- **`NW-<serial>`** is a batch/model code, shared across every physical unit of that model —
  it is **not** unique per light. The model is a pure function of this serial; the MAC is not
  consulted (the MAC can rotate on power-cycle). A fixture therefore self-identifies from its
  advertisement alone, with no connection or query.
- **`&<networkId>`** is a mutable 32-bit group id — see [provisioning.md](provisioning.md).

Serial → model, for the reference tube fixtures:

| Advertised serial | Model |
|---|---|
| `NW-20240012` | TL90C |
| `NW-20240047` | TL120C-2 |
| `NW-20240061` | TL60 RGB-3 |
| `NW-20210036` | TL60 RGB |
| `NW-20230064` | TL60 RGB-2 |
| `NW-20230031` | TL120C |

The firmware-version reply carries an ASCII tail (`RGB-2`, `RGB-3`). **This tail is a
firmware-generation code, not a product model** — a TL120C reports `RGB-2` as well (2.x →
`RGB-2`, 3.x → `RGB-3`). It cannot identify the fixture on its own; the advertised serial is
the durable identifier.

---

## Authentication

**There is none at the BLE layer.** Any client that connects to `69400001` and writes `0x78`
frames to characteristic `69400002` controls the light. No nonce, challenge, key, or
signature gates any command. The `networkId` identity check transmits the plaintext 32-bit id
plus an index — it is addressing, not access control. Frame integrity is a single additive
checksum byte: error detection, not authentication. The "NEEWER Infinity Authenticated
device" label in the vendor app means "claimed under an account's server-assigned
`networkId`", not that any cryptographic access control exists.

---

## Partially understood opcodes

A few opcodes are handled by the firmware but not fully mapped. They are listed so an
implementer knows they exist — and, in two cases, knows to avoid them.

| Opcode | What is known |
|---|---|
| `0xA0` | Light-effect status **read**. Returns reply `0x09`, a fixed-length status array. A read, not a setter; it does not report audio or live colour. |
| `0xA1` | Undocumented **setter**. Sent with no payload it **blanks the fixture output** (goes dark). Arguments and purpose are unmapped. |
| `0xA2` | Undocumented **setter** in the same cluster as `0xA1`; also drives the output dark. Arguments and purpose are unmapped. |

**Fuzzing caution:** `0xA1` and `0xA2` turn the light off. They are recoverable with a normal
power/colour frame or a power-cycle, but if you sweep the opcode space, expect these two to
black out the fixture.

## Music / audio-reactive mode

The audio-reactive ("music") feature is **entirely host-side**. The fixtures have **no
microphone** and do not react to sound on their own. In the vendor app, the phone samples its
own microphone, runs the frequency/beat analysis, and streams ordinary `0x86` HSI / `0x87` CCT
frames to the light in real time. There is no dedicated audio opcode — to reproduce the
feature, do the analysis on your host and stream colour frames yourself.

## Known unknowns

Listed so nobody hunts for something that is not resolvable from the outside:

- **WiFi provisioning** — a `0x03` reply relates to WiFi state on the WiFi-capable models, but
  the provisioning payload is only partially read. Not relevant to the BLE tubes.
- **Streamer render sequence** — the exact `0xBF` / `0xC0` provisioning-and-render order needs
  a Streamer-capable fixture (TL60 RGB-2/-3) plus a group to pin down; see
  [pixel-and-streamer.md](pixel-and-streamer.md).
- **Some effect parameter units** — a few scene/streamer parameters (`sectionType`, `runStyle`,
  `movement`, `direction`) are passed through from UI state with no in-protocol annotation, so
  their exact ranges are not fully known.

---

## Notes for an independent implementation

- The header byte is `0x85` (not `0x78`) for OTA frames on device-class-6 fixtures.
- `networkId` is little-endian in the advertised name suffix and in most payloads, but the
  "New" builder variants write only the low 16 bits.
- Pixel and streamer multi-frame sends rely on inter-frame timing (roughly 80 ms between the
  sub-frames of a pixel effect), not on ACKs.
- The MTU is fixed at 23. Reassemble both notifications and long writes by the `78 op len`
  header, never per ATT packet.
- Extended colour on the TL tube line is by-MAC only. Send RGBCW as `0xA9`, xy as `0xB7`, gel
  as `0xAD`, scene as `0x91` — the direct forms are ignored.
