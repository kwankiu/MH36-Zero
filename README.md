# MH36-Zero V1.0

**Modular toolhead board for Waveshare RP2040-Zero and compatible modules**

MH36-Zero is a compact, open-source toolhead board designed for Voron, Rat Rig, and other Klipper-based 3D printers. Built around the Waveshare RP2040-Zero, it is minimalist yet feature-complete: 4 independent fan/heater channels, selectable voltage, StepStick driver socket, ADXL345 breakout, filament detection, probe support, Neopixel, and I2C expansion.

The board is designed to be **cheap and easy to build** — only basic SMD passives are required, and it reuses parts you likely already have (RP2040-Zero, TMC driver, ADXL breakout, NMOS modules, etc.).

### Key Features / Compatibility

- Modular and reusable parts (drawer-friendly)
- Waveshare RP2040-Zero socket
  - Supports RP2040-Zero/Tiny, RP2350-Zero, and pin-compatible modules
  - Cloned RP2040-Zero cost less than $2 (Not tested for reliability, Waveshare board is **recommended**)
- StepStick driver module socket
  - Support TMC2209 (recommended), TMC2208, TMC2240, GC6609, A4988, etc
  - UART and DIAG supported
- 4× independent FAN/HE channels with NMOS headers (GND-GATE-OUT)
  - MOS_VSEL jumper: 5V or VIN selectable
  - Removable NMOS modules (6-pin header)
  - NMOS modules: 40A NMOS module (HYG038N03) from aliexpress/taobao, Mellow Fly VS3622DE (dual-channel), etc.
  - High-current capable (using KF128-2.54-2P connectors, default XH2.54-2P is 3A max)
- ADXL345 (GY-291) breakout socket (SPI + INT)
  - Support I2C and SPI
  - INT1 is connected to GPIO 13 (shared with Probe)
- NTC/PT1000 temperature sensors (2 channels)
  - Support NTC 100K and PT1000 (4.7K pull-up resistor, PT100 not supported)
- Neopixel header
- Probe / 3D-Touch header with servo support
- Filament Detect header (configurable as endstop for homing)
- I2C expansion header (support sensors like AHT20, BME280, LDC1612, Eddy, etc)
- Power input: 12–24V screw terminal (3.81 mm)
- Optional VIN & 5V power LEDs
- M3 mounting holes (43.85 mm diagonal spacing)
  - Fits 36 mm pancake motors, compatible with EBB36 mount
  - Mount: Mini Stealthburner, Anthead, A4T, DragonBurner, G2 and similar toolheads
- Board size: 50.8 × 45.8 mm
- Firmware: Klipper (tested with RP2040-Zero USB connection)

### Pinout & Wiring

![Pinout Diagram](docs/images/pinout.png)  
*(Add your EasyEDA 3D view screenshot or drawn diagram here)*

**TO BE ADDED**

### BOM
Mandatory:
- 5× 100nF 0603 ceramic capacitor
- 1× 100µF 35V electrolytic capacitor (through-hole, 6.3 or 8mm diameter)
- 2× 2.2kΩ 0603 resistor
- 2× 4.7kΩ **0.1%** 0603 resistor (0.1% tolerance is **strongly recommended** for temperature accuracy)
- 1× 100Ω 0603 resistor
- 1× 330Ω 0603 resistor (or 470Ω, can be ommited if your neopixel already have a 470Ω resistor)
- 1x 3.81 mm 2-pin screw terminal (such as KF128-3.81-2P)
- 1x Waveshare RP2040-Zero (or compatible module)
- 1x StepStick driver module (TMC2209, GC6609, A4988, etc)
- 1x NMOS modules (4× HYG038N03 module or 2x Mellow FLY Fan MOS or similar)
- 2.54 mm **male** headers (various counts)
- 2.54 mm **female** headers (RP2040-Zero, StepStick, NMOS 6-pin and ADXL 8-pin)
- 7x 2.54 mm XH 2-pin connectors (you can swap 1-3 of them with KF128-2.54-2P for FAN/HEATER headers)
- 1x 2.54 mm XH 4-pin connectors

Optional:
- 2× 0603 LED (VIN & 5V indicators)
- 1× 10kΩ 0603 resistor (VIN LED)
- 1× 2.2kΩ 0603 resistor (5V LED)
- 1x ADXL345 GY-291 Module (for input shaper, bed leveling, probing and X/Y homing)
- 1x NTC 100K through-hole for chamber temp
- 2x 2.54mm Jumpers (for INT and DIAG enable)

### Assembly Guide

1. **Bottom side (SMD)**: Solder all 0603 passives (100nF ×5, 4.7k 0.1% ×2, 2.2k x2, 100Ω, 330Ω and optional LED resistors).
2. **Bottom side (Jumper Pads)**: Solder **all four** Voltage Select jumper pads to your desired operating voltage (Probe/3D Touch, FAN/HE1, FAN/HE2, FAN/HE3, FAN/HE4)
3. **Top side**: Solder headers (2.54 mm), 3.81 mm terminal, 100µF 35V electrolytic (observe polarity!) and all connectors (Optional: Solder LEDs + resistors (VIN LED 10k, 5V LED 2.2k)).
3. **Top side (Jumper Pins)**: Short the INT and DIAG enable jumpers if the feature is needed
4. **Plug-ins**:
   - Waveshare RP2040-Zero module
   - TMC driver module
   - NMOS modules
   - ADXL module (optional)
5. **GND enhancement for high sustained load (optional)**:
   - Solder a **18 AWG or thicker** wire between the two GND pins on the TMC/StepStick module (or the bottom of the PCB)
   - This is discovered during a LED resistor routing which generated a narrow ground plane path causing the ground to flow through the TMC/StepStick Module instead of the ground plane as return path (fixed in the final V1.0 PCB file, therefore **not necessary**), but may help reduces heat and allows better ground return under sustained high loads

### Klipper Configuration

See `firmware/klipper_config/mcu_mh36_zero.cfg` in this repo for a template.

```ini
[mcu mh36_zero]
serial: /dev/serial/by-id/usb-Klipper_rp2040_...

[board_pins mh36_zero]
aliases:
    heater1_pin = gpio5, fan1_pin = gpio6,
    heater2_pin = gpio7, fan2_pin = gpio8,
    heater3_pin = gpio9, fan3_pin = gpio10,
    heater4_pin = gpio11, fan4_pin = gpio12,
    thermistor0_pin = gpio27,
    thermistor1_pin = gpio28,
    neopixel_pin = gpio26,
    adxl345_cs_pin = gpio15,
    # ... (add more as needed)
```

### Safety & Warnings

- No overcurrent/short-circuit protection on VIN — use fused/current-limited PSU, never plug/unplug wires live.
- No reverse polarity protection — double-check +VIN– wiring before power-up (reverse may damage USB host via GND backdoor).
- Hand-assembly **required** — solder carefully, check for bridges.

### License
- GPL GNU v3 License — feel free to fork, modify, and share.

### Credits
- Designed by kwankiu (Samuel)
- Inspired by EBB36, SHT36, NH36, THR36, and other open-source toolheads