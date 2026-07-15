# Firmware & OTA

Firmware version reads and the over-the-air update transports.

## Version read

- **Direct:** `78 80 00 F8` → reply `0x00`, version at bytes[5,6,7] (e.g. `1.1.9` =
  `… 01 01 09 …`). Not answered by every fixture — the TL120C, for instance, does not reply to
  the direct version read.
- **By MAC:** `78 9E 06 <MAC6> ck` → reply `0x08`, version at bytes[11,12,13], followed by an
  ASCII model tail.

The ASCII tail (`RGB-2`, `RGB-3`) is a **firmware-generation code, not a product model**: a
TL120C reports `RGB-2` too (2.x → `RGB-2`, 3.x → `RGB-3`). The advertised serial is the
reliable model identifier — see [protocol.md](protocol.md).

## OTA — two transports

The update transport is selected per fixture by a firmware-update mode: 1 = Nordic DFU, 2 = a
custom `0x78` block protocol. Both run over the normal `69400001` control service — there is no
separate DFU characteristic in the update flow.

### Nordic Secure DFU (mode 1)

A standard Nordic DFU: a signed `.zip` package delivered through the Nordic Secure DFU service
(`0000fe59-0000-1000-8000-00805f9b34fb`, control `8ec90001…`, packet `8ec90002…`). Legacy DFU
uses `00001530-1212-efde-1523-785feabcd123`.

### Custom `0x78` block protocol (mode 2)

1. **Probe** — `0xD0` → reply `0x1A`. If `bytes[3] == 1` the fixture takes 4096-byte blocks
   ("OTA_PRO"), otherwise 128-byte blocks.
2. **Header** — `0x96`: `<v1 v2 v3> <size BE32> <checkCode BE32> <name>`. The `checkCode` is an
   additive sum over the image.
3. **Blocks** — `0x97` (≤128 B) or `0xCF` (≤4096 B), each re-fragmented into 20-byte GATT
   writes. A block's data continuation writes carry no `0x78` prefix.
4. **Flow control** — reply `0x06` after each block, with the operation at bytes[3]: 0=next,
   1=resend, 2=restart, 3=done, 4=fail. The block index is the only sequence number; ordering is
   driven by the fixture's ACK. There is no CRC32.

On commit the fixture powers fully off instead of relaunching on the new image. It has to be
switched back on by hand, so a reconnect immediately after the final ACK will fail.

#### Fragment spacing

These are two-chip fixtures: a BLE-UART radio module forwards frames over an internal UART to a
separate LED MCU (see [hardware.md](hardware.md)). During OTA that UART reassembler is the
bottleneck, not the BLE link. Push the 20-byte GATT fragments too fast and it silently overruns, so
the fixture drops fragments and asks for resends via `0x06` op=1.

This was confirmed on a TL60 RGB-3 (firmware confirmation 2026-07-14 by fohdeesha, whose
`neewer-bridge` drove the first full end-to-end flash). At about 6 ms between fragments, roughly
75% of blocks were retransmitted. Raising the gap to about 20 ms took resends to zero and ran the
transfer roughly 100x faster: a 142 KB, 1113-block image in about 500 s with no resends.

A few practical points:

- Keep inter-fragment spacing around 20 ms or more, and raise it further on a marginal link.
- The write characteristic accepts write-with-response, so each fragment can be ATT-acked at the
  link layer on top of the per-block `0x06` ACK.
- Site the BLE adapter next to the fixture and give it exclusive access, with no second central
  contending for the connection, for the duration of the flash.

## Firmware versions

The latest published firmware version for each fixture is listed on the per-model pages under
[models/](models/).
