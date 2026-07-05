# Provisioning & the networkId

A fixture advertises as `NW-<serial>&<networkId>`, e.g. `NW-20240012&00900002`. The
`&<networkId>` suffix is a mutable 32-bit **group id**, stored on the fixture and broadcast in
its advertisement. Both `&FFFFFFFF` (−1) and `&00000000` mean **unassigned**.

The `networkId` is worth understanding for two reasons: it is how the vendor app groups
fixtures, and — because you can write it to any value and it survives a power-cycle — it
doubles as a durable per-unit identifier that solves the MAC-rotation problem.

## Enrollment is one-shot

Writing the `networkId` (opcode `0x9F` provision or `0x8C` assign, both MAC-addressed and
acknowledged by a `0x7F` reply) **commits to the fixture's flash only when both** of these
hold:

1. the fixture is currently **unassigned** (`FFFFFFFF` or `00000000`), and
2. the **`0x7F` ACK handshake completes** (the fixture must be able to reply).

When it commits, it commits **immediately** — there is no settle window (a fixture
power-cycled the instant after provisioning keeps the new id). **Once assigned,
re-provisioning is a no-op**: it returns a success ACK but reverts on the next power-cycle. To
change an assigned fixture's id you must un-enroll it first.

This means you cannot "reset to `00000000`" by provisioning `00000000` onto an enrolled
fixture — the write is acknowledged but ignored. Un-enroll first.

## Un-enroll and recover — the reset procedure

- **Un-enroll:** hold the wireless (`❋/2.4G`) button about **5 seconds while in BLE mode**.
  This returns the fixture to unassigned (`&00000000`); the BLE MAC is unchanged. The fixture
  is then re-enrollable.
- **Full recovery**, when a fixture gets wedged (dark telemetry, won't ACK, notify stuck):
  un-enroll as above, re-add it in the vendor app with **no other BLE central active** (stop
  any daemon; use the phone as the sole central), then **power-cycle the fixture**. The
  power-cycle is the step that actually clears the wedge — after an app re-add a wedged unit
  can stay uncontrollable until it is power-cycled, then returns fully.

The un-enroll clears the fixture's network state (→ `&00000000`) and re-initialises its
BLE/GATT layer — enough that a host's cached GATT handles go stale and notify subscriptions
break. It does **not** randomise the MAC, and it leaves stored colour and configuration intact.
It resets the Infinity connection state, not a factory wipe.

## Using networkId as a persistent unit id

Because it can be written to any value and survives a power-cycle, the `networkId` is a
self-documenting, reboot-stable unit id. A convenient scheme encodes the model and unit
number in hex:

- `0x009000NN` for 90C units — `&00900002` reads as "90C #2"
- `0x012000NN` for 120C units — `&01200001` reads as "120C #1"
- `0x006000NN` for 60 units

Caveats:

1. It is **advertisement-only** — no GATT query reads it back, so reading a fixture's current
   id requires it to be advertising (a connected fixture is silent; do a fresh scan).
2. The **vendor app will overwrite it** if you use the app to group the fixture — the app
   stamps the account's own `networkId`. This scheme is for controller-driven rigs kept away
   from app grouping.

## Identity is not order

The unit id gives durable **identity** — which fixture this is. It does not give **order** —
where the fixture sits in a physical row, which effects that sweep across fixtures (chases,
travelling waves) and DMX auto-addressing need. Order is a separate mapping layered over the
stable identity; a fixture moving in the row is an edit to that mapping, never a hardware
re-provision.

## How the vendor app uses networkId (for reference)

The provisioning opcodes and their sequence, as the app drives them:

| Step | Opcode | Frame | Meaning |
|---|---|---|---|
| Provision | `0x9F` | `78 9f 0c <MAC6> 01 <CH> <NET4> ck` | register / add a device |
| Assign | `0x8C` | `78 8c 0b <MAC6> <CH> <NET4> ck` | put the fixture on channel `CH` |
| Activate | `0xD4` | `78 d4 06 <NET4> <CH> 00 ck` | select `CH` as the active group |
| ACK (reply) | `0x7F` | `78 7f 08 <MAC6> <op> 00 ck` | per-device ACK of the `0x9F` / `0x8C` |

The app separates groups by **channel**, keeping the `NET4` broadcast (`ffffffff`) in that
flow. The `NET4` stamped into every by-channel colour frame is the **account** networkId (one
value for the whole logged-in fleet), which is a different thing from the per-fixture
advertised `&<networkId>` suffix; grouping under an account makes the two coincide. `CH` is a
device-list-position index, not the physical 2.4G dial channel.
