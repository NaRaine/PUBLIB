# PCB MANUFACTURING SERVICES COMPARISON
## JLCPCB vs PCBWay vs Others for Clay Transmitter Board

**Created:** 2025-12-19 16:00
**Purpose:** Select optimal PCB manufacturer for Orange Pi 5 ultrasonic transmitter
**Board Complexity:** Simple (single-sided, through-hole + SMD, ~5×7cm)
**Quantity:** 1-5 units (prototype)

---

## I. SERVICE COMPARISON

### Quick Summary

| Service | Best For | Price Range | Lead Time | Component Library |
|---------|----------|-------------|-----------|-------------------|
| **JLCPCB** | 1-10 simple boards | $2-50 | 2-5 days | 500,000+ (mostly SMD) |
| **PCBWay** | Complex/custom | $5-100 | 3-7 days | Full turnkey sourcing |
| **Seeed Fusion** | Medium batch | $10-80 | 5-10 days | Decent library |
| **Advanced Circuits** | US-based, fast | $30-200 | 1-5 days | Full sourcing |

---

## II. JLCPCB (RECOMMENDED FOR THIS PROJECT)

### Advantages

**Pricing:**
- PCB fabrication: $2 for 5 boards (100×100mm, 2-layer)
- Assembly: ~$8 setup + $2-3 per board for basic parts
- **Total estimate: $15-25 for 5 assembled boards**

**Speed:**
- PCB fab: 24-48 hours
- Assembly: 2-5 days
- Shipping (DHL): 3-5 days
- **Total: 7-12 days door-to-door**

**Component Library:**
- 500,000+ parts in stock (LCSC partnership)
- Resistors, capacitors, ICs, transistors, diodes
- **PAM8403 amplifier module: NOT in library**
- **40 kHz transducer: NOT in library**

**Automation:**
- Instant online quote (upload Gerber + BOM)
- Automated SMT assembly (pick-and-place)
- High volume capability (millions of boards/year)

**Limitations:**
- Through-hole components: limited selection
- Custom/exotic parts: must ship to them first ("consigned parts")
- No hand assembly of large connectors
- Best for SMD-only designs

---

## III. PCBWAY

### Advantages

**Flexibility:**
- Full turnkey assembly (they source ANY part)
- Through-hole, SMD, mixed assembly
- Hand assembly available
- Custom mechanical parts (connectors, transducers)

**Quality:**
- Imported Fuji pick-and-place machines (Japan)
- 3D X-Ray inspection, SPI, ICT testing
- IQC incoming inspection
- Better for complex designs

**Current Promotion:**
- **50% OFF assembly sale (Dec 2025)**
- Could reduce assembly cost significantly

**Pricing:**
- PCB fab: $5 for 10 boards (100×100mm)
- Assembly: $20-50 setup + $5-15 per board
- Component sourcing: markup ~10-30%
- **Total estimate: $40-80 for 5 boards**

**Lead Time:**
- 5-10 days fabrication + assembly
- 3-5 days shipping
- **Total: 8-15 days**

**Advantages for this project:**
- Can source ultrasonic transducer
- Can assemble PAM8403 module
- Handles mixed SMD + through-hole
- Full turnkey (one-stop shop)

---

## IV. OTHER SERVICES

### Seeed Studio Fusion

**Profile:**
- Mid-range pricing ($10-30 for 5 boards)
- Open-source hardware friendly
- Good for maker/hobbyist projects
- 7-14 day lead time

**Advantages:**
- Active maker community
- Tutorials and support forums
- Integration with Seeed components

**Disadvantages:**
- Slower than JLCPCB/PCBWay
- Higher minimum quantities for some services

---

### Advanced Circuits (USA)

**Profile:**
- US-based manufacturer (Colorado)
- Premium pricing ($50-200)
- Fast domestic turnaround (24-hour PCB available)

**Advantages:**
- No import duties/customs for US customers
- English-language support (phone/email)
- High reliability (aerospace/medical quality)

**Disadvantages:**
- 3-5× more expensive than Chinese services
- Overkill for simple prototype

---

## V. FILE REQUIREMENTS

### All Services Require:

**1. Gerber Files (RS-274X format)**
```
- Top copper layer (.GTL)
- Bottom copper layer (.GBL)
- Top soldermask (.GTS)
- Bottom soldermask (.GBS)
- Top silkscreen (.GTO)
- Bottom silkscreen (.GBO)
- Board outline (.GKO or .GM1)
- Drill file (.TXT or .DRL)
```

**Alternative formats accepted:**
- ODB++ (more modern, single file)
- IPC-2581 (industry standard)

**2. Bill of Materials (BOM)**
```
Columns required:
- Reference Designator (e.g., R1, C1, U1)
- Value (e.g., 10kΩ, 100nF, PAM8403)
- Quantity
- Manufacturer
- Manufacturer Part Number (MPN)
- Supplier (e.g., LCSC, Mouser, Digikey)
- Supplier Part Number

Format: CSV or Excel (.xlsx)
```

**3. Pick-and-Place File (CPL/Centroid)**
```
Columns required:
- Designator (e.g., R1, C1, U1)
- X coordinate (mm)
- Y coordinate (mm)
- Rotation (degrees, 0-360)
- Layer (Top/Bottom)

Format: CSV or TXT
```

**4. Assembly Drawing (optional but helpful)**
```
- PDF showing component placement
- Component values labeled
- Polarity indicators (diodes, electrolytic caps)
- Connector pinouts
- Test points marked
```

---

## VI. RECOMMENDATION FOR CLAY TRANSMITTER

### Option A: JLCPCB (Cheapest, Partial Assembly)

**Approach:**
1. Design PCB with only SMD components from JLCPCB library
2. Have JLCPCB assemble: resistors, capacitors, voltage regulator
3. Hand-solder yourself: PAM8403 module, screw terminals, headers
4. Source separately: ultrasonic transducer

**Cost breakdown:**
```
5× PCBs (fabrication):              $2
Assembly (basic SMD parts):         $15
Shipping (DHL):                     $8
Components (hand-solder):           $10
Ultrasonic transducer (separate):   £25
─────────────────────────────────────
TOTAL:                              $35 + £25 = ~£55
```

**Timeline:**
- 7-12 days for PCBs to arrive
- 1 hour hand assembly
- **Total: ~2 weeks**

**Advantages:**
- Cheapest option
- Fast turnaround
- Educational (learn PCB assembly)

**Disadvantages:**
- Requires soldering skills
- Need to source some parts separately

---

### Option B: PCBWay (Full Turnkey)

**Approach:**
1. Design complete circuit (all components)
2. Upload Gerber + BOM + CPL to PCBWay
3. They source ALL parts (including transducer)
4. Receive fully assembled board (just plug into Orange Pi)

**Cost breakdown:**
```
10× PCBs (fabrication):             $5
Assembly (full turnkey):            $40 (50% off sale)
Component sourcing + markup:        $30
Shipping (DHL):                     $10
─────────────────────────────────────
TOTAL:                              $85 = ~£65
```

**Timeline:**
- 8-15 days fully assembled
- 0 hours hand assembly required
- **Total: 2 weeks**

**Advantages:**
- Zero assembly required
- Professional quality
- All parts sourced for you
- Arrives ready to connect

**Disadvantages:**
- Slightly more expensive (+£10)
- Need to provide exact part numbers

---

## VII. FINAL RECOMMENDATION

### **Use PCBWay with 50% Assembly Sale**

**Why:**
1. **Turnkey convenience** - arrives fully assembled, ready to plug into Orange Pi
2. **Current promotion** - 50% off assembly makes it competitive with JLCPCB
3. **Custom parts** - can source ultrasonic transducer (not in JLCPCB library)
4. **Professional quality** - tested and inspected
5. **Time savings** - no hand assembly required (important for 18-month deadline)

**Total cost: ~£65 for fully assembled board**

**Deliverables needed:**
1. Gerber files (export from KiCad/Eagle)
2. BOM with LCSC/Mouser/Digikey part numbers
3. Pick-and-place file (export from KiCad/Eagle)
4. Assembly notes (PDF)

---

## VIII. ALTERNATIVE: HAND-BUILD ON PERFBOARD

### If You Want to Start Immediately

**Don't wait for PCB manufacturing - build prototype on perfboard:**

**Advantages:**
- Start today (no waiting for delivery)
- Total cost: £4 (PAM8403 + perfboard + wire)
- Can iterate and test quickly
- Learn circuit functionality

**Timeline:**
- 1-2 hours assembly
- Test immediately
- Order PCB version in parallel

**Recommended approach:**
1. **Week 1:** Build perfboard prototype, test circuit
2. **Week 1:** Order PCB from PCBWay (ships while you test)
3. **Week 2:** Receive PCB, install in weatherproof enclosure
4. **Week 3:** Deploy to garden

**This way you can test the circuit TODAY while waiting for professional PCB.**

---

## IX. NEXT STEPS

### To Order from PCBWay:

1. **Design files** (I'll create these):
   - Complete circuit schematic
   - PCB layout (KiCad or Eagle)
   - Export Gerber files
   - Generate BOM with part numbers
   - Generate pick-and-place file

2. **Upload to PCBWay:**
   - Go to https://www.pcbway.com/quotesmt.aspx
   - Upload Gerber ZIP file
   - Upload BOM CSV
   - Upload CPL file
   - Get instant quote

3. **Review and order:**
   - Check quote (~£65 expected)
   - Verify component sourcing (all parts available)
   - Pay via PayPal/credit card
   - Receive in 8-15 days

---

**PCBWay turnkey recommended.**

**Professional assembly, arrives tested, plug into Orange Pi and transmit.**

**£65 total - well under budget.**

**Ready for detailed circuit design?**
