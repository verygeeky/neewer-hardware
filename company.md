# Company & brand

## Manufacturer

Neewer Infinity fixtures are made by **Shenzhen Neewer Technology Co., Ltd**, based in Shenzhen,
China. This is the FCC grantee of record for the wireless modules in these products, under
grantee code **`2ANIV`** (a legacy code `2ADUJ` also exists).

## Brand / trademark

The **NEEWER** trademark (USPTO registrations 4462769 and 5477858) is registered to **Shenzhen
Xing Ying Da Industry Co., Ltd**.

## Radio module certifications

The tubes' two radios are certified modules. The BLE module is certified by its module maker;
the 2.4 GHz modules are certified under the Neewer grantee code:

| FCC ID | Grantee | Module | Radio |
|---|---|---|---|
| `2AA72-PTR5618PA` | XunTong | PTR5618PA | Nordic nRF52832 (Bluetooth LE) |
| `2ANIV-G01` | Neewer | G01-SPIPX-N (Gisemi) | Si24R1 (2.4 GHz) |
| `2ANIV-NW-01` | Neewer | NW-01 | Si24R1 (2.4 GHz) |
| `2ANIV-PC24G` | Neewer | PC24G | Si24R1-class USB 2.4 GHz dongle |

Both tube-radio FCC IDs (`2AA72-PTR5618PA` and `2ANIV-G01`) appear on the fixture's regulatory
label. See [hardware.md](hardware.md).

## Fixture identity

A fixture advertises as `NW-<serial>&<networkId>`. The serial is a batch/model code shared
across every unit of a model — not unique per light — and the model is derived from it. The
serial → model table is in [protocol.md](protocol.md).

## Related products

Godox sells a physically similar RGB tube line under matching names — **TL60 / TL120 / TL180** —
with comparable, brand-tuned specifications:

| | Neewer TL120C | Godox TL120 |
|---|---|---|
| Length | 120 cm | 117 cm |
| Power | 42 W | 30 W |
| CCT range | 2500–10000 K | 2700–6500 K |
| Battery | 10.8 V / 6000 mAh | 14.4 V / 5200 mAh / 74.88 Wh |
| Adapter | 20 V / 5.0 A | 20 V / 4.8 A |
| Illuminance @1 m, 5600 K | 900 lux | 685 lux |

The two lines use different, incompatible Bluetooth control protocols. Any further relationship
between them is not established.
