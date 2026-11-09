# ESP32 Edge AI — Hardware Design

ESP32-S3 camera + TFT board (project name `ESP32_SML`), designed in **EasyEDA Pro**.

## Files

| Path | Description |
|------|-------------|
| `hardware/ESP32_SML.epro2` | Full EasyEDA Pro project (schematic + PCB). Open via *File → Import → EasyEDA Pro* or *Open Project*. |
| `hardware/ESP32_SML_netlist.tel` | Netlist exported from Schematic1 (Allegro format) for quick review. |

## Overview

- **MCU:** ESP32-S3-WROOM-1-N16R16V (U1)
- **Camera:** OV2640 via 24-pin 0.5 mm FPC (FPC1), DVDD 1.2 V (XC6206-1.2, U3), AVDD 2.8 V (XC6206-2.8, U2), DOVDD 3.3 V
- **Display:** 2.2" ILI9341 SPI TFT (H3, 9-pin) + touch header (H2, 5-pin), powered from **3.3 V**
- **Power:** LM2596S-3.3 buck (U4) → +3V3; input from DC jack (DC1) or USB-C 5 V selected by jumper H5
- **USB:** USB-C (USB1) native USB on IO19/IO20, 5.1 kΩ CC pull-downs
- **Headers:** UART (H1), I2C (H4)
- **Buttons:** EN (SW1), BOOT/IO0 (SW2)

### Pin map (ESP32-S3)

| Function | GPIO |
|----------|------|
| CSI D0–D7 | 4, 5, 6, 7, 15, 16, 17, 18 |
| CSI PCLK / XCLK / HREF / VSYNC | 8 / 38 / 39 / 40 |
| Camera SDA / SCL | 41 / 42 |
| TFT RESET / DC / MOSI / SCK / MISO / CS | 9 / 10 / 11 / 12 / 13 / 14 |
| TFT backlight | 1 |
| Touch CS / IRQ | 21 / 2 |
| Status LED | 3 |
| USB D− / D+ | 19 / 20 |
| UART TX / RX | 43 / 44 |

## Design review notes (2026-11-09)

Changes made:
- TFT connector VCC (H3 pin 9) changed from +5V to **+3V3**.
- R11 (series on TFT MISO) part fixed: value said 0 Ω but part was 5.1 kΩ → now `0805W8F0000T5E` (LCSC C17477).
- PCB1 synced with schematic (C9/H3 nets, C22 and R11 added, stale C19 removed). New parts still need placement; PCB is not routed yet.

Open items:
- Verify H3 (reversed order) and H2 (normal order) match the physical TFT module pinout.
- C22 input cap is 16 V rated → keep DC input ≤ 12 V or use a 35 V part.
- LM2596 from USB 5 V has little headroom at high load.
- H5: never fit both jumper positions at once (shorts DC input to USB 5 V).
- TFT_LED driven directly by IO1 — confirm the module has a backlight transistor.
- No reverse-polarity, USB ESD, or fuse protection.
