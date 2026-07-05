# Hardware

The physical construction, radios, power, and operating behaviour of the Neewer Infinity tube
fixtures.

## Radios

The tubes carry two independent 2.4 GHz radios, each a certified module identified on the
fixture's regulatory label (as "Contains FCC ID"):

- **Bluetooth Low Energy** — a Nordic **nRF52832** BLE SoC on the **PTR5618PA** module
  (FCC ID `2AA72-PTR5618PA`), with an integrated power amplifier. This is the radio behind the
  app / BLE control interface described in [protocol.md](protocol.md).
- **Proprietary 2.4 GHz** — a **Si24R1** GFSK transceiver (an nRF24L01+-class part) on the
  **G01-SPIPX-N** module (FCC ID `2ANIV-G01`). This carries the 2.4 GHz channel mesh and
  "Wireless DMX" described in [dmx.md](dmx.md).

The two radios are separate, and the fixture uses only one at a time (see Control modes).

## Power

Every tube runs on either mains or a built-in (non-removable) lithium battery, and charges from
a **20 V DC barrel adapter**. The adapter is the same 20 V design across the line; only the
current rating scales with fixture size:

| Fixture | Adapter model | Output |
|---|---|---|
| TL60 | Neewer NT-200240D2 | 20 V / 2.4 A / 48 W |
| TL90C | Neewer NT72-200360D2 | 20 V / 3.6 A / 72 W |
| TL120C | Neewer NT136-2000500D2 | 20 V / 5.0 A / 100 W |

Battery-pack figures are on the per-model pages under [models/](models/).

## Control modes — the physical switch

Each fixture has a physical control-mode selector (the `❋/2.4G` button and mode indicators).
The modes are **mutually exclusive** — a fixture is in exactly one at a time.

| Mode | What it is | Reachable over BLE? |
|---|---|---|
| **BLE / App / Infinity** | Bluetooth LE. The fixture advertises and accepts a GATT connection. | Yes — the mode the app and any BLE controller use. |
| **2.4G** | Neewer's proprietary 2.4 GHz channel mesh. | No — not BLE; invisible to a BLE central. |
| **DMX** | Wired DMX (on models with a port) / 2.4G Wireless-DMX addressing. | See [dmx.md](dmx.md). |

**2.4G and BLE cannot be active at once.** Flipping a fixture to 2.4G drops it off Bluetooth
entirely. A BLE-connected fixture is not on the 2.4G mesh and cannot relay onto it — this holds
for any controller, including the vendor app.

## 2.4G channel mesh

In **2.4G mode**, fixtures set to the **same channel** sync fixture-to-fixture: adjust one (its
dial, or a Wireless-DMX transmitter) and the others on that channel follow.

- **Set the channel on-device:** short-press `❋/2.4G` until the **2.4G** indicator lights; press
  and hold about one second until the screen shows `CH`; rotate the dial to the channel number.
- **Keyed on channel only** — it is independent of the BLE `networkId` (two fixtures with
  different advertised nets sync on the same channel).
- **Hardware-only.** There is no way to inject into a live 2.4G channel from BLE (mode
  exclusivity, above) or from a wired DMX input on another light.
- The **2.4G dial channel is a different axis from the BLE `networkId`**; they are unrelated
  numbers.

## Multi-light control over BLE

A BLE-connected fixture is not on the 2.4G mesh, and there is no BLE-side multicast frame. Over
BLE, several fixtures are driven by holding one connection per fixture and writing each
individually; a "group" in a BLE controller is a list of fixtures to address in turn, not a
hardware broadcast target. The by-channel opcodes in [protocol.md](protocol.md) are a
2.4G-relay path that a BLE-connected fixture does not self-apply.

BLE write-without-response has no backpressure, so a controller that sends frames faster than
the adapter delivers them builds an unbounded transmit-queue backlog. The host adapter's
aggregate throughput, not the per-fixture rate, is the ceiling as fixture count grows.

## Operational notes

- **BLE is effectively single-central.** Although the GATT layer allows multiple connections,
  two centrals against one fixture at once — for example the phone app and another controller —
  produce flaky or dead notify subscriptions and colour writes that do not apply. A fixture that
  "won't connect or behaves oddly" is usually held by another central, or switched to 2.4G mode,
  rather than broken.
- **BLE MAC rotation is intermittent.** A power-cycle sometimes re-randomises a fixture's BLE
  MAC; a mode switch (BLE ↔ 2.4G) does not. MAC-keyed maps can therefore go stale on a reboot;
  the `networkId` unit id in [provisioning.md](provisioning.md) is the durable anchor.
- **The vendor app cannot colour a lone BLE member it just added.** The app drives colour via
  the group / by-channel path, which a fixture does not self-apply over BLE; the direct by-MAC
  colour opcodes always work.
- **An ungraceful host disconnect can strand the BLE link.** If a controlling process is killed
  without disconnecting, the fixture can be left connected at the OS Bluetooth layer, stop
  advertising, and become undiscoverable by a scan. Recovery is an explicit OS-level disconnect
  (e.g. `bluetoothctl disconnect <MAC>`).
