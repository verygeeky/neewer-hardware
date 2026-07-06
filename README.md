# neewer-hardware

A technical reference for the **Neewer** "Infinity" LED lights — the RGB tube lights, panels,
and COB heads in Neewer's 2.4 GHz / Bluetooth family. It documents the hardware, the wireless
control protocol, the per-model capabilities, and the company behind the products. It is the
reference for someone who owns one of these lights, or is deciding whether to.

The fixtures covered in depth are the **TL-series tube lights** —
[TL60](models/tl60.md) (60 cm), [TL90C](models/tl90c.md) (90 cm), and
[TL120C](models/tl120c.md) (120 cm).

## What these lights are

The TL-series tubes are full-colour LED tubes with Red, Green, Blue, Cool White, and Warm White
emitters (RGBCW). They provide:

- Adjustable white, roughly 2500–10000 K, with green/magenta (GM) tint control
- Full RGB / HSI colour and CIE xy colour-point control
- Gel / colour-paper emulation (LEE and ROSCO libraries)
- Built-in lighting effects (scenes)
- Pixel effects — colour bands rendered along the tube — on the models that support them
- A high colour rendering index (CRI 97, TLCI 98, per Neewer)

Each tube runs on mains or a built-in (non-removable) lithium battery, and has a wired DMX port.
The same protocol and much of the same capability extend across Neewer's wider Infinity range —
the AP/CB/FS/HS/PL panels, the RGB COB heads, and dozens of other models — so a large part of
this reference applies beyond the tubes. The full list is in
[model-capabilities.md](model-capabilities.md).

## What you can do with them

A physical switch on the fixture selects one control mode at a time:

- **On the light itself** — a dial and screen set colour, temperature, brightness, and effects.
- **From the Neewer phone app**, over Bluetooth LE.
- **Fixture-to-fixture over 2.4 GHz** — lights on the same 2.4 GHz channel sync together; this is
  hardware-only and not driven from a phone or computer.
- **Over DMX** — a wired DMX512 console (the tubes have a DMX port), or Neewer's proprietary
  2.4 GHz "Wireless DMX" on the models that support it. See [dmx.md](dmx.md).

The Bluetooth LE interface is open and unauthenticated: a client connects, writes command
frames, and the fixture responds. The frame format and the full command set are in
[protocol.md](protocol.md). This reference is implemented by the
[neewer](https://github.com/verygeeky/neewer-python) Python library and the
[neewerd](https://github.com/verygeeky/neewerd) control daemon built on it; other community
software that controls these lights is indexed in [awesome-neewer.md](awesome-neewer.md).

## What's in this reference

| Document | What it covers |
|---|---|
| [hardware.md](hardware.md) | The radios, power/adapters, the physical BLE / 2.4G / DMX mode switch, the 2.4 GHz channel sync, and operational behaviour. |
| [protocol.md](protocol.md) | The Bluetooth wire protocol: GATT services, frame format, addressing modes, the full command-opcode reference, and reply decoding. |
| [provisioning.md](provisioning.md) | The `networkId` group id — enrolling a light into a group, using it as a stable per-unit id, and resetting it. |
| [pixel-and-streamer.md](pixel-and-streamer.md) | The pixel-effect palette system and the TL60 "Streamer" / FX-Flow feature. |
| [dmx.md](dmx.md) | The two independent DMX paths: wired DMX512 and 2.4 GHz "Wireless DMX". |
| [firmware.md](firmware.md) | Firmware version reads and the over-the-air update transports. |
| [model-capabilities.md](model-capabilities.md) | The capability matrix for ~108 models. |
| [company.md](company.md) | The manufacturer, brand, radio-module certifications, and related products. |
| [models/](models/) | Per-model pages for the tube fixtures. |
| [awesome-neewer.md](awesome-neewer.md) | An index of third-party software for these lights. |

## Scope and confidence

The wire protocol, addressing, provisioning, DMX paths, radios, and per-model behaviour
described here are documented for the TL60, TL90C, and TL120C. The capability matrix for the
wider range is drawn from Neewer's own configuration data and product documentation; details not
individually verified are marked as such. Open items are tracked in the
[issue tracker](https://github.com/verygeeky/neewer-hardware/issues).
