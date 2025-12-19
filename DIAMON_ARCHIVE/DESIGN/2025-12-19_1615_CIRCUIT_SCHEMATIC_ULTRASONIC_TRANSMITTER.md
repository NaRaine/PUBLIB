# ULTRASONIC TRANSMITTER CIRCUIT SCHEMATIC
## Orange Pi 5 to Clay Layer Interface Board

**Created:** 2025-12-19 16:15
**Board Name:** CLAY-TX-01
**PCB Size:** 50×70mm (2×2.8 inches)
**Layer Count:** 2-layer (Top + Bottom)
**Voltage:** 5V DC input (from Orange Pi GPIO)
**Power:** 3W continuous, 5W peak

---

## I. COMPLETE CIRCUIT DIAGRAM

### Block Diagram Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                      CLAY-TX-01 BOARD                            │
│                                                                   │
│  Orange Pi GPIO ──┬── Voltage Reg ── PAM8403 ── Transducer       │
│   Pin 1 (3.3V)    │     (5V)          Amplifier   Driver         │
│   Pin 4 (5V)  ────┘                                              │
│   Pin 7 (PWM) ────────────────────────┘                          │
│   Pin 6 (GND) ────────────────────────────────────────────────── │
└─────────────────────────────────────────────────────────────────┘
```

---

## II. DETAILED SCHEMATIC

### Power Supply Section

```
Orange Pi Pin 4 (5V)
    │
    ├───┬──── C1 (100µF) ──── GND    [Input filter]
    │   │
    │   └──── C2 (100nF) ──── GND    [High-freq bypass]
    │
    ├──── R1 (10Ω) ────┬──── +5V_FILT [Series resistor for isolation]
    │                  │
    │                  └──── LED1 (red) ──── R2 (1kΩ) ──── GND  [Power indicator]
    │
Orange Pi Pin 6 (GND) ──── GND
```

**Component values:**
- C1: 100µF electrolytic, 10V (input smoothing)
- C2: 100nF ceramic, 10V (high-frequency noise)
- R1: 10Ω, 0.25W (current limiting)
- R2: 1kΩ, 0.125W (LED current = 4mA)
- LED1: Red 3mm LED, 2V forward voltage

---

### PWM Input Section

```
Orange Pi Pin 7 (GPIO 98, PWM0)
    │
    ├──── R3 (1kΩ) ────┬──── C3 (10nF) ──── GND  [Low-pass filter, 16 kHz cutoff]
    │                  │
    │                  └──── PAM8403.IN+ (Pin 1)
    │
    ├──── R4 (10kΩ) ──── GND  [Pull-down resistor]
    │
Orange Pi Pin 6 (GND) ──── PAM8403.IN- (Pin 2)
                           PAM8403.GND (Pin 3)
```

**Component values:**
- R3: 1kΩ (input impedance matching)
- R4: 10kΩ (pull-down to prevent floating input)
- C3: 10nF ceramic (anti-aliasing filter)

**Filter calculation:**
```
Cutoff frequency fc = 1 / (2π × R3 × C3)
                    = 1 / (2π × 1000 × 10×10⁻⁹)
                    = 15.9 kHz

Passes: 40 kHz PWM signal ✓
Blocks: High-frequency switching noise ✓
```

---

### Amplifier Section (PAM8403)

**PAM8403 Pinout:**
```
         ┌─────────────┐
  IN+  1 │●            │ 16  OUT_R+
  IN-  2 │             │ 15  OUT_R-
  GND  3 │             │ 14  NC
  SD   4 │   PAM8403   │ 13  NC
  VDD  5 │             │ 12  NC
  OUT_L+ 6│             │ 11  NC
  OUT_L- 7│             │ 10  NC
  NC   8 │             │ 9   NC
         └─────────────┘
```

**Connections:**
```
PAM8403.VDD (Pin 5) ──── +5V_FILT
PAM8403.GND (Pin 3) ──── GND
PAM8403.SD  (Pin 4) ──── +5V_FILT    [Shutdown pin - HIGH = active]

PAM8403.IN+  (Pin 1) ──── R3 ──── Orange Pi Pin 7 (PWM)
PAM8403.IN-  (Pin 2) ──── GND

PAM8403.OUT_L+ (Pin 6) ──── C4 (220µF) ──── Transducer (+)
PAM8403.OUT_L- (Pin 7) ──── C5 (220µF) ──── Transducer (-)

PAM8403.OUT_R+ (Pin 16) ──── [Not connected]
PAM8403.OUT_R- (Pin 15) ──── [Not connected]
```

**Decoupling capacitors:**
```
PAM8403.VDD (Pin 5) ──┬──── C6 (10µF) ──── GND  [Power decoupling]
                      └──── C7 (100nF) ── GND  [High-freq bypass]
```

**Component values:**
- C4, C5: 220µF electrolytic, 10V (output DC blocking)
- C6: 10µF tantalum or ceramic, 10V
- C7: 100nF ceramic, 10V

**Why LEFT channel only?**
- PAM8403 is stereo (2× 3W channels)
- We only need mono output
- Left channel feeds transducer
- Right channel unused (saves components)

---

### Transducer Output Section

```
                      ┌─────────────────────┐
                      │  Ultrasonic Trans   │
PAM8403.OUT_L+ ───────┤  (+) Red wire       │
  (via C4)            │                     │
                      │  40 kHz piezo       │
PAM8403.OUT_L- ───────┤  (-) Black wire     │
  (via C5)            │                     │
                      │  60W rated          │
                      └─────────────────────┘

Screw terminal J1:
  Pin 1: Transducer (+) ──── C4 ──── PAM8403.OUT_L+
  Pin 2: Transducer (-) ──── C5 ──── PAM8403.OUT_L-

Cable: Shielded 2-conductor, 18 AWG, up to 10 meters
```

**Why DC-blocking capacitors (C4, C5)?**
- PAM8403 output has DC bias (~2.5V)
- Transducer is AC-only device
- Capacitors pass 40 kHz AC, block DC
- Prevents transducer damage/heating

---

### Optional: Activity Indicator LED

```
PAM8403.OUT_L+ ────┬──── D1 (1N4148 diode) ──── C8 (10µF) ──┬──── LED2 (green) ──── R5 (1kΩ) ──── GND
                   │                                         │
                   │                                         └──── R6 (10kΩ) ──── GND
                   └─────────────────────────────────────────┘
```

**Function:**
- Diode rectifies 40 kHz AC signal
- C8 smooths to DC
- LED2 brightness indicates output level
- R6 bleeds charge when signal stops

**Component values:**
- D1: 1N4148 fast switching diode
- C8: 10µF electrolytic, 10V
- LED2: Green 3mm LED
- R5: 1kΩ (LED current limiting)
- R6: 10kΩ (discharge resistor)

**Optional component** - useful for debugging but not essential.

---

## III. COMPLETE PARTS LIST

### Active Components

| Ref | Description | Value | Package | Part Number |
|-----|-------------|-------|---------|-------------|
| U1 | Audio amplifier IC | PAM8403 | SOP-16 | PAM8403DR |
| LED1 | Power indicator | Red 3mm | THT | WP3A8ID |
| LED2 | Signal indicator (opt) | Green 3mm | THT | WP3A8GD |
| D1 | Fast switching diode (opt) | 1N4148 | DO-35 | 1N4148 |

### Passive Components - Resistors

| Ref | Value | Power | Tolerance | Package | Part Number |
|-----|-------|-------|-----------|---------|-------------|
| R1 | 10Ω | 0.25W | 5% | 0805 SMD | RC0805FR-0710RL |
| R2 | 1kΩ | 0.125W | 5% | 0805 SMD | RC0805FR-071KL |
| R3 | 1kΩ | 0.125W | 5% | 0805 SMD | RC0805FR-071KL |
| R4 | 10kΩ | 0.125W | 5% | 0805 SMD | RC0805FR-0710KL |
| R5 | 1kΩ (opt) | 0.125W | 5% | 0805 SMD | RC0805FR-071KL |
| R6 | 10kΩ (opt) | 0.125W | 5% | 0805 SMD | RC0805FR-0710KL |

### Passive Components - Capacitors

| Ref | Value | Voltage | Type | Package | Part Number |
|-----|-------|---------|------|---------|-------------|
| C1 | 100µF | 10V | Electrolytic | 5mm dia | EEE-1AA101P |
| C2 | 100nF | 10V | Ceramic X7R | 0805 SMD | CL21B104KBCNNNC |
| C3 | 10nF | 50V | Ceramic X7R | 0805 SMD | CL21B103KBANNNC |
| C4 | 220µF | 10V | Electrolytic | 6.3mm dia | EEE-1AA221P |
| C5 | 220µF | 10V | Electrolytic | 6.3mm dia | EEE-1AA221P |
| C6 | 10µF | 10V | Ceramic X5R | 0805 SMD | CL21A106KOQNNNE |
| C7 | 100nF | 10V | Ceramic X7R | 0805 SMD | CL21B104KBCNNNC |
| C8 | 10µF (opt) | 10V | Electrolytic | 5mm dia | EEE-1AA100P |

### Connectors

| Ref | Description | Pins | Pitch | Part Number |
|-----|-------------|------|-------|-------------|
| J1 | Screw terminal (transducer) | 2-pin | 5.08mm | 691214110002 |
| J2 | Screw terminal (power) | 2-pin | 5.08mm | 691214110002 |
| J3 | Female pin header (to Orange Pi) | 2×4 pin | 2.54mm | PZ254V-11-04P |

### Mechanical

| Ref | Description | Quantity |
|-----|-------------|----------|
| - | PCB (50×70mm, 2-layer) | 1 |
| - | M3 mounting holes | 4 |
| - | M3×10mm standoffs | 4 |
| - | M3 screws | 8 |

---

## IV. PCB LAYOUT GUIDELINES

### Board Dimensions

```
┌─────────────────────────────────────────────┐  ↑
│ ●                                         ● │  │
│                                             │  │
│   [J3]           [U1]            [J1]       │  70mm
│  GPIO          PAM8403         Transducer   │  │
│  Header                         Output      │  │
│                                             │  │
│   [LED1]  [LED2]                 [J2]       │  │
│   Power   Signal                 Power      │  ↓
│                                             │
│ ●                                         ● │
└─────────────────────────────────────────────┘
  ←─────────────── 50mm ─────────────────────→
```

**Mounting holes:** 4× M3, positioned 3mm from corners

---

### Layer Stack-Up

```
Layer 1 (Top):    Copper (35µm / 1 oz)
                  Soldermask (green)
                  Silkscreen (white)

Core:             FR4 (1.6mm standard thickness)

Layer 2 (Bottom): Copper (35µm / 1 oz)
                  Soldermask (green)
                  Silkscreen (white)
```

---

### Component Placement

**Top side:**
- U1 (PAM8403) - center of board
- All SMD resistors and capacitors - near U1
- J1 (transducer terminal) - right edge
- J2 (power terminal) - bottom right
- J3 (GPIO header) - left edge
- LED1, LED2 - bottom left (visible)

**Bottom side:**
- Through-hole electrolytic capacitors (C1, C4, C5)
- Minimizes board height

---

### Trace Width Guidelines

| Net | Current | Width | Spacing |
|-----|---------|-------|---------|
| +5V_FILT | 1A max | 0.5mm (20 mil) | 0.3mm |
| GND | 1A max | 0.5mm (20 mil) | 0.3mm |
| PWM input | 1mA | 0.25mm (10 mil) | 0.3mm |
| Audio output | 0.5A | 0.4mm (16 mil) | 0.3mm |

**Ground plane:**
- Bottom layer: solid GND pour (maximizes noise immunity)
- Top layer: GND pour in unused areas
- Multiple vias connect top/bottom ground

---

### Critical Layout Notes

1. **Keep PWM trace SHORT** (<30mm from J3 to R3)
   - Minimizes EMI pickup
   - Reduces ringing

2. **Decoupling capacitors CLOSE to U1**
   - C6, C7 within 5mm of VDD pin
   - Vias to ground plane immediately adjacent

3. **Output traces (to J1) WIDE**
   - Handles 0.5A transducer current
   - 0.4mm width minimum

4. **Separate analog/digital ground returns**
   - PWM input ground separate from output ground
   - Star ground topology at power input

5. **Thermal relief on power pads**
   - PAM8403 has thermal pad (Pin 9 equivalent)
   - Large copper area on bottom for heat dissipation
   - Multiple thermal vias

---

## V. DESIGN RULES FOR MANUFACTURING

### PCBWay Standard Specs

```
Min trace width:         0.15mm (6 mil)
Min trace spacing:       0.15mm (6 mil)
Min hole size:           0.3mm (12 mil)
Min annular ring:        0.15mm (6 mil)
Copper weight:           35µm (1 oz)
Soldermask:              Green LPI
Silkscreen:              White
Surface finish:          HASL (lead-free) or ENIG
Board thickness:         1.6mm
```

**Our design uses:**
- Trace width: 0.25-0.5mm (well above minimum)
- Spacing: 0.3mm (2× minimum, safe)
- Vias: 0.6mm hole, 1.0mm pad (standard)
- Easy to manufacture, low cost

---

## VI. TESTING POINTS

### Add Test Points for Debugging

| TP | Net | Purpose |
|----|-----|---------|
| TP1 | +5V_FILT | Verify power supply |
| TP2 | GND | Ground reference |
| TP3 | PWM input | Monitor 40 kHz signal |
| TP4 | PAM8403.OUT_L+ | Check amplifier output |

**Test point specification:**
- 1.0mm diameter pad
- No soldermask (bare copper)
- Silkscreen label "TP1", etc.

---

## VII. BILL OF MATERIALS (BOM) FOR PCBWAY

**See separate file:** `2025-12-19_1630_BOM_CLAY_TRANSMITTER.csv`

BOM includes:
- Reference designators
- Values
- Manufacturer part numbers (for LCSC/Mouser/Digikey)
- Supplier part numbers
- Quantities

---

## VIII. NEXT STEPS

1. **Recreate schematic in KiCad:**
   - File → New Project → "CLAY-TX-01"
   - Draw schematic exactly as shown above
   - Assign footprints to all components

2. **Generate PCB layout:**
   - Place components as per layout guidelines
   - Route traces (autorouter or manual)
   - Add ground pour
   - Run Design Rule Check (DRC)

3. **Export Gerber files:**
   - File → Plot
   - Select Gerber format
   - Include all layers (copper, mask, silk, drill)
   - Generate ZIP file

4. **Export BOM and CPL:**
   - Use KiCad BOM plugin
   - Export pick-and-place (CPL) file

5. **Upload to PCBWay:**
   - Quote and order

**Estimated time to recreate in KiCad: 2-3 hours**

**Ready to create the BOM file with part numbers?**
