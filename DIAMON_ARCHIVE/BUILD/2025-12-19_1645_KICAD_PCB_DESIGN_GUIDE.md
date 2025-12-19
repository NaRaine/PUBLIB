# KICAD PCB DESIGN GUIDE
## Step-by-Step Recreation of CLAY-TX-01 Transmitter Board

**Created:** 2025-12-19 16:45
**Software:** KiCad 7.0 or later (free, open-source)
**Time Required:** 2-3 hours (first time), 30-60 min (if experienced)
**Output:** Gerber files ready for PCBWay upload

---

## I. INSTALL KICAD

### Download and Install

**Linux (Ubuntu 22.04):**
```bash
sudo add-apt-repository ppa:kicad/kicad-7.0-releases
sudo apt update
sudo apt install kicad

# Verify installation
kicad --version
# Should show: KiCad 7.0.x or later
```

**Alternative (Flatpak):**
```bash
flatpak install flathub org.kicad.KiCad
flatpak run org.kicad.KiCad
```

**Windows/Mac:**
- Download from https://www.kicad.org/download/
- Run installer, accept defaults
- Launch KiCad

---

## II. CREATE NEW PROJECT

### Step 1: Project Setup

1. Launch KiCad
2. **File → New Project**
3. Navigate to: `/media/raine/VM/SYSTEM_ROOT/DIAMON_ARCHIVE/BUILD/`
4. Project name: `CLAY-TX-01`
5. Click **Create**

**Files created:**
- `CLAY-TX-01.kicad_pro` (project file)
- `CLAY-TX-01.kicad_sch` (schematic, initially empty)
- `CLAY-TX-01.kicad_pcb` (PCB layout, initially empty)

---

### Step 2: Configure Symbol and Footprint Libraries

**Install additional libraries (if needed):**

```bash
# PAM8403 symbol might not be in default library
# Download from SnapEDA or Ultra Librarian:
# https://www.snapeda.com/parts/PAM8403/Diodes%20Incorporated/view-part/

# Save symbol library to:
mkdir -p ~/Documents/KiCad/7.0/symbols/
# Copy .kicad_sym file here

# Save footprint library to:
mkdir -p ~/Documents/KiCad/7.0/footprints/
# Copy .kicad_mod file here
```

**Add library in KiCad:**
1. **Preferences → Manage Symbol Libraries**
2. Click **+** (Add)
3. Browse to downloaded library
4. **OK**

---

## III. SCHEMATIC CAPTURE

### Step 3: Open Schematic Editor

1. In KiCad project window, click **Schematic Editor** icon
2. Schematic canvas opens (blank grid)

---

### Step 4: Place Components

**Keyboard shortcuts:**
- **A** = Add symbol
- **M** = Move
- **R** = Rotate
- **C** = Copy
- **W** = Wire (connect)
- **Del** = Delete

**Place components (press A, then search for each):**

1. **Power input:**
   - Symbol: `Connector_Generic:Conn_01x02` (J2, optional)
   - OR use wire labels for Orange Pi connection

2. **GPIO header:**
   - Symbol: `Connector_Generic:Conn_02x04_Odd_Even` (J3)
   - Or: 4× single pins with labels

3. **Amplifier IC:**
   - Symbol: `Amplifier_Audio:PAM8403` (U1)
   - If not found, use generic SOP-16 and add pin labels

4. **Resistors** (press A, type "R"):
   - 6× resistors (R1-R6)
   - Value annotation: Right-click → Properties → Value
   - R1 = 10Ω, R2/R3/R5 = 1kΩ, R4/R6 = 10kΩ

5. **Capacitors** (press A, type "C"):
   - Electrolytic: `Device:CP` (C1, C4, C5, C6, C8)
   - Ceramic: `Device:C` (C2, C3, C7)
   - Set values in properties

6. **LEDs:**
   - Symbol: `Device:LED` (LED1, LED2)
   - Polarity matters: anode = +, cathode = -

7. **Diode:**
   - Symbol: `Device:D` (D1, optional)
   - Type: 1N4148

8. **Screw terminal:**
   - Symbol: `Connector_Generic:Conn_01x02` (J1)

9. **Power symbols:**
   - Press **P**, search "GND" → place ground symbols
   - Press **P**, search "+5V" → place +5V symbols

---

### Step 5: Wire Connections

**Connect components according to schematic:**

1. **Power input section:**
   ```
   +5V ──┬── C1 ── GND
         ├── C2 ── GND
         ├── R1 ──┬── +5V_FILT
         │        └── LED1 ── R2 ── GND
         └── U1.VDD (Pin 5)
   ```

2. **PWM input:**
   ```
   J3.Pin7 (GPIO98) ──┬── R3 ──┬── C3 ── GND
                      │        └── U1.IN+ (Pin 1)
                      └── R4 ── GND

   J3.Pin6 (GND) ──── U1.IN- (Pin 2), U1.GND (Pin 3)
   ```

3. **Output:**
   ```
   U1.OUT_L+ (Pin 6) ── C4 ── J1.Pin1 (Transducer+)
   U1.OUT_L- (Pin 7) ── C5 ── J1.Pin2 (Transducer-)
   ```

4. **Decoupling:**
   ```
   U1.VDD ──┬── C6 ── GND
            └── C7 ── GND
   ```

5. **Shutdown pin:**
   ```
   U1.SD (Pin 4) ──── +5V  (always enabled)
   ```

**Use wire labels for long connections:**
- Press **L** to add label
- Name: "+5V", "GND", "PWM_IN", etc.
- Avoids messy long wires

---

### Step 6: Annotate Schematic

**Assign reference designators:**
1. **Tools → Annotate Schematic**
2. Select "Use entire schematic"
3. Click **Annotate**
4. Verifies: R1, R2, C1, C2, etc. are assigned

---

### Step 7: Assign Footprints

**Map symbols to physical footprints:**

1. **Tools → Assign Footprints**
2. For each component, select footprint:

| Component | Footprint |
|-----------|-----------|
| U1 (PAM8403) | `Package_SO:SOP-16_4.4x10.4mm_P1.27mm` |
| R1-R6 | `Resistor_SMD:R_0805_2012Metric` |
| C2, C3, C7 | `Capacitor_SMD:C_0805_2012Metric` |
| C6 | `Capacitor_SMD:C_0805_2012Metric` |
| C1, C4, C5, C8 | `Capacitor_THT:CP_Radial_D5.0mm_P2.00mm` |
| LED1, LED2 | `LED_THT:LED_D3.0mm` |
| D1 | `Diode_THT:D_DO-35_SOD27_P7.62mm_Horizontal` |
| J1 | `TerminalBlock_Phoenix:PhoenixContact_MKDS_1,5-2_5.08mm` |
| J2 | `TerminalBlock_Phoenix:PhoenixContact_MKDS_1,5-2_5.08mm` |
| J3 | `Connector_PinHeader_2.54mm:PinHeader_2x04_P2.54mm_Vertical` |

3. Click **Apply, Save Schematic & Continue**

---

### Step 8: Electrical Rules Check (ERC)

1. **Inspect → Electrical Rules Checker**
2. Click **Run ERC**
3. Fix any errors:
   - "Pin not connected" → add power symbols or no-connect flags
   - "Pin power conflict" → check power net names
4. Warnings are usually OK
5. **Close** when all errors resolved

---

### Step 9: Generate Netlist

**Not explicitly needed in KiCad 7+ (auto-synced), but good practice:**

1. **Tools → Generate Netlist**
2. Click **Generate Netlist**
3. Save as `CLAY-TX-01.net`

---

## IV. PCB LAYOUT

### Step 10: Open PCB Editor

1. In KiCad main window, click **PCB Editor** icon
2. Blank PCB canvas opens

---

### Step 11: Import Schematic

1. **Tools → Update PCB from Schematic** (or press **F8**)
2. Click **Update PCB**
3. All components appear in a cluster (not placed yet)
4. Move mouse over components, they'll "float" with cursor

---

### Step 12: Define Board Outline

**Draw board edge:**

1. Select **Edge.Cuts** layer (right panel)
2. Press **Ctrl+Shift+G** (or **Draw → Line**)
3. Draw rectangle: 50×70mm
   - Click at (0, 0)
   - Click at (50, 0)
   - Click at (50, 70)
   - Click at (0, 70)
   - Click at (0, 0) to close
4. Press **Esc** to finish

**Add mounting holes:**

1. **Place → Drill or Hole**
2. Select type: "Plated Through Hole"
3. Diameter: 3.2mm (for M3 screw)
4. Place at corners: (3, 3), (47, 3), (3, 67), (47, 67)

---

### Step 13: Place Components

**Arrange components on PCB:**

**Placement strategy:**

```
Left side:          Center:               Right side:
- J3 (GPIO header)  - U1 (PAM8403)        - J1 (transducer output)
- LED1 (power)      - SMD resistors/caps  - J2 (power, optional)
- LED2 (signal)     - Decoupling caps
                    near U1
Bottom:
- C1, C4, C5 (electrolytics, bottom side)
```

**Steps:**
1. Click component, press **M** to move
2. Press **R** to rotate (90° increments)
3. Position as per layout above
4. Align components on grid (2.54mm or 1.27mm)

**Critical placement:**
- C6, C7 (decoupling) within 5mm of U1 Pin 5 (VDD)
- R3, R4 close to J3 (PWM input)
- J1 near board edge for easy cable access

---

### Step 14: Route Traces

**Manual routing (recommended for beginners):**

1. Press **X** (start routing)
2. Click on component pad
3. Follow design rules from schematic document:
   - Power traces (+5V, GND): 0.5mm width
   - Signal traces: 0.25mm width
   - Audio output: 0.4mm width
4. Click to place waypoints
5. Click on destination pad to finish
6. Press **Esc** when done

**OR use auto-router (quick but not optimal):**

1. **Route → Freerouter Plugin** (if installed)
2. Click **Route**
3. Wait 10-60 seconds
4. Review and manually fix if needed

**Trace width settings:**

1. **File → Board Setup → Design Rules → Net Classes**
2. Default class: 0.25mm trace, 0.3mm clearance
3. Add custom class "Power": 0.5mm trace
4. Assign +5V and GND to "Power" class

---

### Step 15: Add Ground Plane (Copper Pour)

**Bottom layer ground plane:**

1. Select **B.Cu** layer (bottom copper)
2. Press **Ctrl+Shift+Z** (Add Filled Zone)
3. Net: GND
4. Click to draw outline (same as board edge)
5. Right-click → Close Outline
6. Settings:
   - Clearance: 0.3mm
   - Priority: 0

**Top layer ground fill (partial):**

1. Select **F.Cu** layer (top copper)
2. Repeat above process
3. Draws ground in unused areas

**Rebuild fills:**
- Press **B** to rebuild all copper zones

**Add thermal vias:**
- Under U1, add 4-6 vias connecting top to bottom ground
- Helps with heat dissipation

---

### Step 16: Add Silkscreen Labels

**Component labels (automated):**
- Already present from footprints (R1, C1, etc.)

**Add custom text:**

1. Select **F.Silkscreen** layer
2. **Place → Text**
3. Add:
   - "CLAY-TX-01" (board name)
   - "v1.0" (version)
   - "2025" (year)
   - "Orange Pi 5 Ultrasonic Transmitter"
   - Pin labels on J3: "3.3V", "5V", "PWM", "GND", etc.

**Text size:**
- Board title: 2mm height
- Pin labels: 1mm height
- Component labels: 0.8mm height

---

### Step 17: Add Test Points (Optional)

1. **Add → Footprint**
2. Search: "TestPoint"
3. Select: `TestPoint:TestPoint_Pad_D1.0mm`
4. Place at:
   - TP1: +5V_FILT net
   - TP2: GND net
   - TP3: PWM_IN net
   - TP4: U1 Pin 6 (output)

---

### Step 18: Design Rules Check (DRC)

**Verify design is manufacturable:**

1. **Inspect → Design Rules Checker**
2. Click **Run DRC**
3. Check for errors:
   - Clearance violations (traces too close)
   - Unconnected pads (missing traces)
   - Short circuits
4. Fix all errors (warnings usually OK)
5. **Close** when clean

---

### Step 19: 3D Preview (Optional but Cool!)

1. **View → 3D Viewer** (or **Alt+3**)
2. Inspect board from all angles
3. Verify component placement
4. Check for mechanical clearance issues
5. Press **R** to rotate, scroll to zoom

---

## V. GENERATE MANUFACTURING FILES

### Step 20: Export Gerber Files

**Gerber = industry standard format for PCB fabrication**

1. In PCB Editor: **File → Fabrication Outputs → Gerbers (.gbr)**
2. Settings:
   - **Plot format:** Gerber
   - **Include layers:**
     - ☑ F.Cu (top copper)
     - ☑ B.Cu (bottom copper)
     - ☑ F.Paste (top solder paste, for stencil)
     - ☑ B.Paste (bottom solder paste)
     - ☑ F.Silkscreen
     - ☑ B.Silkscreen
     - ☑ F.Mask (top soldermask)
     - ☑ B.Mask (bottom soldermask)
     - ☑ Edge.Cuts (board outline)
   - **Options:**
     - ☑ Plot footprint values
     - ☑ Plot reference designators
     - ☑ Use Protel filename extensions (optional)
   - **Output directory:** `/media/raine/VM/SYSTEM_ROOT/DIAMON_ARCHIVE/BUILD/gerbers/`
3. Click **Plot**
4. Click **Generate Drill Files**
   - Format: **Excellon**
   - Units: **Millimeters**
   - Click **Generate Drill File**
5. Close

**Files created:**
```
CLAY-TX-01-F_Cu.gbr     (top copper)
CLAY-TX-01-B_Cu.gbr     (bottom copper)
CLAY-TX-01-F_Mask.gbr   (top soldermask)
CLAY-TX-01-B_Mask.gbr   (bottom soldermask)
CLAY-TX-01-F_Silkscreen.gbr
CLAY-TX-01-B_Silkscreen.gbr
CLAY-TX-01-F_Paste.gbr  (top paste, for stencil)
CLAY-TX-01-B_Paste.gbr
CLAY-TX-01-Edge_Cuts.gbr (board outline)
CLAY-TX-01.drl          (drill file)
```

---

### Step 21: Create Gerber ZIP File

**PCBWay requires all Gerbers in a single ZIP:**

```bash
cd /media/raine/VM/SYSTEM_ROOT/DIAMON_ARCHIVE/BUILD/
zip -r CLAY-TX-01_Gerbers.zip gerbers/

# Verify ZIP contents
unzip -l CLAY-TX-01_Gerbers.zip
# Should list 10 files (.gbr and .drl)
```

---

### Step 22: Export BOM (Bill of Materials)

**For PCBWay assembly service:**

1. In Schematic Editor: **Tools → Generate BOM**
2. Select plugin: **bom_csv_grouped_by_value**
3. Click **Generate**
4. Saves as: `CLAY-TX-01_BOM.csv`

**OR use pre-created BOM:**
- Copy `/media/raine/VM/SYSTEM_ROOT/DIAMON_ARCHIVE/BUILD/2025-12-19_1630_BOM_CLAY_TRANSMITTER.csv`
- Rename to `CLAY-TX-01_BOM.csv`

---

### Step 23: Export Pick-and-Place (CPL)

**Component position file for assembly machines:**

1. In PCB Editor: **File → Fabrication Outputs → Component Placement (.pos)**
2. Settings:
   - Format: **CSV**
   - Units: **Millimeters**
   - Include: **Only SMD parts** (or "All" if through-hole also assembled)
   - Side: **Front and Back**
3. Output directory: `/media/raine/VM/SYSTEM_ROOT/DIAMON_ARCHIVE/BUILD/`
4. Click **Generate**

**Files created:**
```
CLAY-TX-01-top.pos    (top side components)
CLAY-TX-01-bottom.pos (bottom side, if any)
```

**Rename for PCBWay:**
```bash
mv CLAY-TX-01-top.pos CLAY-TX-01_CPL.csv
```

---

### Step 24: Create Assembly Drawing (PDF)

**Optional but recommended:**

1. In PCB Editor: **File → Plot**
2. Select:
   - Format: **PDF**
   - Layers: F.Cu, F.Silkscreen, F.Fab (fabrication)
3. Options:
   - ☑ Plot footprint values
   - ☑ Plot reference designators
4. Output: `CLAY-TX-01_Assembly.pdf`
5. Click **Plot**

**Annotate PDF (using LibreOffice Draw or similar):**
- Add arrows showing component orientation
- Mark Pin 1 on U1
- Note polarity on electrolytics (C1, C4, C5)
- Indicate test point locations

---

## VI. FILE CHECKLIST FOR PCBWAY

### Required Files

Before uploading, verify you have:

```
✓ CLAY-TX-01_Gerbers.zip          (10 files inside)
✓ CLAY-TX-01_BOM.csv              (component list with LCSC part numbers)
✓ CLAY-TX-01_CPL.csv              (pick-and-place positions)
✓ CLAY-TX-01_Assembly.pdf         (optional but helpful)
```

**Total upload size:** ~500 KB - 2 MB

---

### File Validation

**Check Gerbers with online viewer:**

1. Go to: https://www.pcbway.com/project/OnlineGerberViewer.html
2. Upload `CLAY-TX-01_Gerbers.zip`
3. Viewer shows each layer
4. Verify:
   - ✓ Board outline correct (50×70mm)
   - ✓ All traces connected
   - ✓ No shorts between pads
   - ✓ Soldermask openings on pads
   - ✓ Silkscreen readable
   - ✓ Drill holes present

**If any issues:** Return to KiCad, fix, re-export Gerbers

---

## VII. UPLOAD TO PCBWAY

### Step 25: Get Quote

1. Go to: https://www.pcbway.com/quotesmt.aspx
2. **Upload Gerber file:** Select `CLAY-TX-01_Gerbers.zip`
3. **PCB Specifications:**
   - Size: Auto-detected (50×70mm)
   - Quantity: **5** (or 10)
   - Layers: **2**
   - Thickness: **1.6mm**
   - Copper weight: **1 oz**
   - Surface finish: **HASL (lead-free)**
   - Soldermask color: **Green**
   - Silkscreen: **White**
4. **Assembly Options:**
   - ☑ PCB Assembly Service
   - **Upload BOM:** `CLAY-TX-01_BOM.csv`
   - **Upload CPL:** `CLAY-TX-01_CPL.csv`
   - Assembly side: **Top only**
   - Component sourcing: **Turnkey** (PCBWay sources all parts)
5. Click **Calculate**

**Expected quote:**
- PCB fabrication (5 boards): ~$5-10
- Assembly setup: ~$20-40 (50% OFF sale → $10-20)
- Components: ~$15-30
- Shipping (DHL 3-5 days): ~$10-15
- **Total: $50-75 (≈ £40-60)**

---

### Step 26: Review and Order

1. Review quote breakdown
2. Check component availability:
   - PCBWay shows which parts are in stock (LCSC)
   - If any parts unavailable, suggests alternatives
3. Verify lead time: 5-10 days typical
4. Click **Add to Cart**
5. Checkout: PayPal or credit card
6. Submit order

**Order confirmation email:**
- Order number
- Estimated ship date
- Tracking will be sent when shipped

---

### Step 27: Production Review (2-3 days)

**PCBWay engineer review:**
- Checks Gerbers for manufacturability
- Verifies BOM part numbers
- Confirms component availability

**Possible questions via email:**
- "Component X is out of stock, use alternative Y?"
- "Trace width too narrow at location Z, can we adjust?"
- "Missing courtyard for component ABC, OK to proceed?"

**Response:**
- Reply within 24 hours
- Accept suggestions (usually correct)
- Approve to proceed

---

### Step 28: Manufacturing (3-7 days)

**Production stages:**
1. PCB fabrication (1-2 days)
2. Component procurement (0-2 days if in stock)
3. SMT assembly (1 day)
4. Through-hole assembly (1 day, if applicable)
5. Testing and inspection (1 day)
6. Packaging and shipping (1 day)

**Tracking updates:**
- "PCB fabrication started"
- "PCB fabrication completed"
- "Assembly in progress"
- "QC testing completed"
- "Shipped via DHL, tracking: XXXXXXXXX"

---

### Step 29: Receive Boards (3-5 days shipping)

**Package contents:**
- 5× (or 10×) fully assembled PCBs
- Each board in antistatic bag
- Inspection report (photos of assembled boards)

**Visual inspection upon receipt:**
- Check for shipping damage
- Verify all components populated
- Check solder joints (should be shiny, no bridges)
- Test continuity: GND to GND pin

---

## VIII. TESTING ASSEMBLED PCB

### Step 30: Bench Test (Before Connecting Orange Pi)

**Equipment needed:**
- Multimeter
- 5V power supply (or USB cable)
- Oscilloscope (optional, for PWM testing)

**Power test:**
1. Connect 5V to J2 (or test points)
2. Measure voltage at TP1 (+5V_FILT)
   - Should read: 4.9-5.1V
3. Check LED1 (power indicator)
   - Should illuminate (red)
4. Measure current draw: 20-50 mA (quiescent)

**PWM input test:**
1. Disconnect power
2. Measure resistance: J3.PWM to GND
   - Should read: ~10kΩ (R4 pull-down)
3. Resistance: J3.5V to GND
   - Should read: >1kΩ (not short)

**Output test:**
1. Power on (5V)
2. Measure voltage at J1 (transducer output)
   - Should read: ~0V DC (no input signal)
3. Touch J3.PWM pin to 3.3V briefly
   - LED2 might flicker (if optional circuit populated)

**If all tests pass → Ready to connect to Orange Pi**

---

## IX. TROUBLESHOOTING

### Common KiCad Issues

**Problem:** "Footprint not found"
- **Fix:** Download footprint from SnapEDA, add to library

**Problem:** DRC errors "Clearance violation"
- **Fix:** Increase spacing, or adjust design rules

**Problem:** "Unconnected items"
- **Fix:** Add missing traces or no-connect flags

**Problem:** "Short circuit"
- **Fix:** Check for overlapping traces, fix in PCB editor

---

### Assembly Issues

**Problem:** PCBWay says "Part X out of stock"
- **Fix:** Accept their suggested alternative (usually compatible)

**Problem:** "Minimum order quantity is 10"
- **Fix:** Order 10 boards (still only £10-15 extra)

**Problem:** Quote higher than expected (>£100)
- **Fix:** Remove optional components (LED2, D1, R5, R6, C8)

---

## X. ALTERNATIVE: SIMPLIFIED HAND-SOLDER VERSION

### If You Want to Skip PCB Manufacturing

**Design for hand assembly:**
- Use perfboard (5×7 cm)
- Through-hole components only (no SMD)
- Follow same schematic
- Wire point-to-point

**Time:** 1-2 hours
**Cost:** £4-8
**Quality:** Functional but less professional

**See:** Previous file `2025-12-19_1545_ORANGE_PI_5_CLAY_TRANSMITTER.md` for perfboard approach

---

## XI. SUMMARY

**Time investment:**
- Learning KiCad: 30-60 minutes (tutorials)
- Schematic capture: 30 minutes
- PCB layout: 1-2 hours (first time)
- Export files: 10 minutes
- Upload and order: 15 minutes
- **Total: 2-3 hours**

**Result:**
- Professional PCB, fully assembled
- Arrives tested and ready to plug into Orange Pi
- Cost: £40-60
- Lead time: 10-15 days

**Next steps:**
1. Install KiCad
2. Follow this guide
3. Export Gerbers, BOM, CPL
4. Upload to PCBWay
5. Receive boards in ~2 weeks

**Or use manual perfboard method to start TODAY.**

**Ready to begin?**
