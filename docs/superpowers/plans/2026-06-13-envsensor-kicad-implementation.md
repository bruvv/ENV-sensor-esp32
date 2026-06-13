# ENV Sensor KiCad Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Deliver a clean KiCad 10 schematic and fully placed, routed, two-layer 52 x 70 mm PCB for the ESPHome environmental sensor.

**Architecture:** Restore the useful KiCad recovery files into the active project, then rebuild connectivity around one 3.3 V I2C bus, one SSD1327 SPI bus, and one LD2450 hardware UART. Reuse the existing module footprints where they match the hardware, add only the missing display and SEN55 connector definitions, and validate the result with KiCad ERC, DRC, schematic parity, and visual exports.

**Tech Stack:** KiCad 10.0.3, `kicad-cli`, KiCad schematic editor, KiCad PCB editor, two-layer PCB.

---

## File Map

- Modify: `envsensor/envsensor.kicad_sch` - complete schematic and net definitions.
- Create: `envsensor/envsensor.kicad_pcb` - active routed PCB restored from the best recovery board.
- Modify: `envsensor/envsensor.kicad_pro` - project rules and library references where required.
- Modify: `envsensor/sym-lib-table` - project-local symbol library registration.
- Modify: `envsensor/fp-lib-table` - project-local footprint library registration.
- Create: `envsensor/lib/envsensor.kicad_sym` - missing module symbols only.
- Create: `envsensor/lib/envsensor.pretty/SSD1327_Waveshare_1.5in.kicad_mod` - display module footprint and mechanical outline.
- Create: `envsensor/reports/erc.json` - final ERC report.
- Create: `envsensor/reports/drc.json` - final DRC and schematic parity report.
- Create: `envsensor/reports/front.png` - top-side 3D visual review export.
- Create: `envsensor/reports/back.png` - bottom-side 3D visual review export.

## Fixed Net Map

| Net | ESP32-S3 pin | Destinations |
| --- | --- | --- |
| `I2C_SDA` | GPIO8 | SCD30 SDA, SEN55 SDA, MS5611 SDA, BH1750 SDA |
| `I2C_SCL` | GPIO9 | SCD30 SCL, SEN55 SCL, MS5611 SCL, BH1750 SCL |
| `OLED_CS` | GPIO10 | SSD1327 CS |
| `OLED_MOSI` | GPIO11 | SSD1327 DIN |
| `OLED_CLK` | GPIO12 | SSD1327 CLK |
| `OLED_DC` | GPIO13 | SSD1327 DC |
| `OLED_RST` | GPIO14 | SSD1327 RST |
| `RADAR_TX` | GPIO17 | LD2450 RX |
| `RADAR_RX` | GPIO18 | LD2450 TX |
| `+5V` | 5VIN | SCD30 VDD, SEN55 VDD, LD2450 5V |
| `+3V3` | 3V3 | SSD1327 VCC, MS5611 VCC, BH1750 VCC, I2C pull-ups |
| `GND` | GND | All modules and decoupling |

### Task 1: Restore Active KiCad Board

**Files:**
- Replace: `envsensor/envsensor.kicad_sch`
- Create: `envsensor/envsensor.kicad_pcb`

- [ ] **Step 1: Preserve the active schematic outside the implementation diff**

Copy `envsensor/envsensor.kicad_sch` to a temporary file under `/tmp`; do not add another repository backup.

Run:

```bash
cp envsensor/envsensor.kicad_sch /tmp/envsensor-before-restore.kicad_sch
```

Expected: `/tmp/envsensor-before-restore.kicad_sch` exists.

- [ ] **Step 2: Restore the richer schematic and PCB**

Use:

```text
envsensor/_restore_backup_2026-05-30T16-42-54-646/envsensor.kicad_sch
envsensor/_restore_backup_2026-05-30T16-42-54-646/envsensor.kicad_pcb
```

Copy them to:

```text
envsensor/envsensor.kicad_sch
envsensor/envsensor.kicad_pcb
```

Expected: active schematic contains ESP32-S3, LD2450, SCD30, MS5611, and BH1750; active board has a 52 x 70 mm `Edge.Cuts` rectangle.

- [ ] **Step 3: Verify both files parse in KiCad 10**

Run:

```bash
/Applications/KiCad/KiCad.app/Contents/MacOS/kicad-cli sch export netlist \
  --output /tmp/envsensor-restored.net \
  envsensor/envsensor.kicad_sch

/Applications/KiCad/KiCad.app/Contents/MacOS/kicad-cli pcb render \
  --output /tmp/envsensor-restored.png \
  envsensor/envsensor.kicad_pcb
```

Expected: both commands exit 0.

- [ ] **Step 4: Commit only active recovery**

```bash
git add envsensor/envsensor.kicad_sch envsensor/envsensor.kicad_pcb
git commit -m "chore(kicad): restore active design"
```

### Task 2: Add Missing Local Library Definitions

**Files:**
- Create: `envsensor/lib/envsensor.kicad_sym`
- Create: `envsensor/lib/envsensor.pretty/SSD1327_Waveshare_1.5in.kicad_mod`
- Modify: `envsensor/sym-lib-table`
- Modify: `envsensor/fp-lib-table`

- [ ] **Step 1: Register one project-local library**

Add:

```scheme
(lib (name "envsensor")(type "KiCad")(uri "${KIPRJMOD}/lib/envsensor.kicad_sym")(options "")(descr "ENV sensor project symbols"))
```

to `envsensor/sym-lib-table`, and:

```scheme
(lib (name "envsensor")(type "KiCad")(uri "${KIPRJMOD}/lib/envsensor.pretty")(options "")(descr "ENV sensor project footprints"))
```

to `envsensor/fp-lib-table`.

- [ ] **Step 2: Create SSD1327 module symbol**

Create symbol `SSD1327_Waveshare_1.5in` with these pins:

| Number | Name | Type |
| --- | --- | --- |
| 1 | VCC | power input |
| 2 | GND | power input |
| 3 | DIN | input |
| 4 | CLK | input |
| 5 | CS | input |
| 6 | DC | input |
| 7 | RST | input |

Assign footprint:

```text
envsensor:SSD1327_Waveshare_1.5in
```

- [ ] **Step 3: Create SSD1327 footprint**

Use the official Waveshare 2D drawing from:

```text
https://files.waveshare.com/wiki/1.5inch%20OLED%20Module/1_5inch_oled_module_size.pdf
```

The footprint must contain:

- Seven plated through holes matching the module header.
- Pin 1 marker.
- 44.5 x 37 mm module body on `F.Fab`.
- Display/body outline on `F.SilkS` without crossing pads.
- Courtyard enclosing the full module.
- Clear connector-side and display-side labels.

- [ ] **Step 4: Use standard SEN55 board connector**

Use symbol `Connector_Generic:Conn_01x06` and footprint:

```text
Connector_JST:JST_GH_SM06B-GHS-TB_1x06-1MP_P1.25mm_Horizontal
```

Pin names in schematic:

| Pin | Net |
| --- | --- |
| 1 | `+5V` |
| 2 | `GND` |
| 3 | `I2C_SDA` |
| 4 | `I2C_SCL` |
| 5 | `GND` (`SEL`) |
| 6 | no-connect |

- [ ] **Step 5: Validate library loading**

Open the project in KiCad 10 and confirm both `envsensor` libraries load without rescue or missing-library dialogs.

- [ ] **Step 6: Commit library definitions**

```bash
git add envsensor/lib/envsensor.kicad_sym \
  envsensor/lib/envsensor.pretty/SSD1327_Waveshare_1.5in.kicad_mod \
  envsensor/sym-lib-table envsensor/fp-lib-table
git commit -m "feat(kicad): add display and sensor connector"
```

### Task 3: Rebuild And Clean Schematic

**Files:**
- Modify: `envsensor/envsensor.kicad_sch`

- [ ] **Step 1: Normalize references and values**

Set:

```text
U1 ESP32-S3 DevKit
U2 SCD30
U3 MS5611
U4 BH1750
U5 SSD1327
J1 SEN55
U6 LD2450
R1 4.7k I2C SDA pull-up
R2 4.7k I2C SCL pull-up
C1 10u +5V bulk
C2 100n +5V decoupling
C3 10u +3V3 bulk
C4 100n +3V3 decoupling
```

- [ ] **Step 2: Arrange schematic blocks**

Place five readable blocks from left to right:

```text
Power -> ESP32-S3 -> I2C sensors -> SSD1327 SPI -> LD2450 UART
```

Use global power symbols and named net labels instead of long crossing wires.

- [ ] **Step 3: Wire power**

Connect:

```text
ESP32 5VIN -> +5V
ESP32 3V3 -> +3V3
all module grounds -> GND
C1/C2 between +5V and GND
C3/C4 between +3V3 and GND
```

Add power flags only where ERC needs proof that the DevKit rails are driven.

- [ ] **Step 4: Wire the I2C bus**

Connect `I2C_SDA` and `I2C_SCL` according to the fixed net map.

Configure module mode pins:

```text
BH1750 ADDR -> GND for address 0x23
MS5611 PS -> +3V3 for I2C
MS5611 CSB -> +3V3 for address 0x77
MS5611 SDO -> no-connect
SCD30 SEL -> GND for I2C
SCD30 RDY and PWM -> no-connect
J1 pin 5 SEL -> GND
J1 pin 6 -> no-connect
```

Connect `R1` from `I2C_SDA` to `+3V3` and `R2` from `I2C_SCL` to `+3V3`.

- [ ] **Step 5: Wire display and radar**

Connect the SSD1327 and LD2450 exactly according to the fixed net map. Mark all unused LD2450 pads and unused ESP32-S3 GPIO pins as no-connect.

- [ ] **Step 6: Annotate and assign footprints**

Run annotation with existing references reset, then verify the final references match the approved reference list. Assign the restored project footprints for ESP32-S3, SCD30, MS5611, BH1750, and LD2450.

- [ ] **Step 7: Commit schematic**

```bash
git add envsensor/envsensor.kicad_sch envsensor/envsensor.kicad_pro
git commit -m "feat(kicad): complete sensor schematic"
```

### Task 4: Make ERC Clean

**Files:**
- Modify: `envsensor/envsensor.kicad_sch`
- Create: `envsensor/reports/erc.json`

- [ ] **Step 1: Run ERC**

```bash
mkdir -p envsensor/reports
/Applications/KiCad/KiCad.app/Contents/MacOS/kicad-cli sch erc \
  --format json \
  --severity-all \
  --exit-code-violations \
  --output envsensor/reports/erc.json \
  envsensor/envsensor.kicad_sch
```

Expected initially: possible power-driver or unconnected-pin violations.

- [ ] **Step 2: Resolve actionable findings**

Fix incorrect pin types, missing power flags, missing no-connect markers, duplicate references, and missing footprints. Do not suppress a warning merely to make the report empty.

- [ ] **Step 3: Re-run ERC**

Run the same command.

Expected: exit 0 with no error or warning violations.

- [ ] **Step 4: Commit ERC-clean schematic**

```bash
git add envsensor/envsensor.kicad_sch envsensor/reports/erc.json
git commit -m "fix(kicad): resolve schematic checks"
```

### Task 5: Place Components In Layout A

**Files:**
- Modify: `envsensor/envsensor.kicad_pcb`

- [ ] **Step 1: Update PCB from schematic**

Import all schematic changes into the restored PCB. Remove obsolete footprints only when they are absent from the approved schematic.

- [ ] **Step 2: Preserve board outline**

Verify `Edge.Cuts` remains one closed rectangle:

```text
52 mm wide
70 mm high
no mounting holes
```

- [ ] **Step 3: Place front-side modules**

Place:

```text
SSD1327 at top
BH1750 below display with clear light path
LD2450 at bottom with radar face toward bottom board edge/room
```

Keep the LD2450 existing header and module geometry. Add copper keep-out under its antenna/radar-sensitive area where required by the module footprint.

- [ ] **Step 4: Place back-side modules**

Place:

```text
ESP32-S3 on left, USB at bottom edge
SCD30 on right, airflow openings unobstructed
MS5611 below SCD30 and away from regulator
J1 SEN55 at bottom edge, cable exiting downward
```

Place pull-ups and decoupling close to their rails and loads.

- [ ] **Step 5: Verify mechanical clearances**

Check front/back body overlap in 3D viewer, not only courtyard overlap. Confirm USB, SEN55 cable, SCD30 airflow, OLED face, BH1750 light path, ESP32 antenna, and LD2450 field of view remain accessible.

- [ ] **Step 6: Commit placement**

```bash
git add envsensor/envsensor.kicad_pcb
git commit -m "feat(kicad): place env sensor modules"
```

### Task 6: Route Power, Signals, And Planes

**Files:**
- Modify: `envsensor/envsensor.kicad_pcb`

- [ ] **Step 1: Configure net classes**

Use:

```text
Default signals: 0.25 mm track, 0.20 mm clearance
+3V3: 0.50 mm track
+5V: 0.80 mm track
```

Use larger widths where a connector or module pad permits.

- [ ] **Step 2: Route high-current power first**

Route USB-derived `+5V` from ESP32 5VIN to SCD30, J1 SEN55, and LD2450. Keep return paths short and avoid routing the fan current through sensor ground stubs.

- [ ] **Step 3: Route buses**

Route:

```text
I2C_SDA and I2C_SCL together without unnecessary vias
OLED SPI as short direct tracks
RADAR_TX and RADAR_RX away from I2C and power switching loops
```

- [ ] **Step 4: Add GND zones**

Add `GND` copper zones on `F.Cu` and `B.Cu`, respecting ESP32 and LD2450 keep-outs. Add stitching vias where they improve return continuity and do not obstruct modules.

- [ ] **Step 5: Finish silkscreen**

Add readable labels:

```text
ENV SENSOR
ESP32-S3
SCD30
SEN55
MS5611
BH1750
SSD1327
LD2450
J1: 5V GND SDA SCL SEL NC
```

Keep all silkscreen clear of pads and board edge.

- [ ] **Step 6: Commit routing**

```bash
git add envsensor/envsensor.kicad_pcb envsensor/envsensor.kicad_pro
git commit -m "feat(kicad): route env sensor board"
```

### Task 7: Make DRC And Schematic Parity Clean

**Files:**
- Modify: `envsensor/envsensor.kicad_pcb`
- Create: `envsensor/reports/drc.json`

- [ ] **Step 1: Refill zones and run DRC**

```bash
/Applications/KiCad/KiCad.app/Contents/MacOS/kicad-cli pcb drc \
  --format json \
  --severity-all \
  --schematic-parity \
  --refill-zones \
  --save-board \
  --exit-code-violations \
  --output envsensor/reports/drc.json \
  envsensor/envsensor.kicad_pcb
```

Expected initially: possible courtyard, silkscreen, unconnected-item, or clearance findings.

- [ ] **Step 2: Resolve actionable findings**

Fix every unconnected net, clearance issue, invalid courtyard overlap, silkscreen-pad collision, malformed keep-out, and schematic/PCB mismatch.

- [ ] **Step 3: Re-run DRC**

Run the same command.

Expected: exit 0 with zero error or warning violations and zero unconnected items.

- [ ] **Step 4: Commit DRC-clean PCB**

```bash
git add envsensor/envsensor.kicad_pcb envsensor/reports/drc.json
git commit -m "fix(kicad): resolve board checks"
```

### Task 8: Export And Visually Verify

**Files:**
- Create: `envsensor/reports/front.png`
- Create: `envsensor/reports/back.png`

- [ ] **Step 1: Export front and back review images**

```bash
/Applications/KiCad/KiCad.app/Contents/MacOS/kicad-cli pcb render \
  --side top \
  --quality high \
  --output envsensor/reports/front.png \
  envsensor/envsensor.kicad_pcb

/Applications/KiCad/KiCad.app/Contents/MacOS/kicad-cli pcb render \
  --side bottom \
  --quality high \
  --output envsensor/reports/back.png \
  envsensor/envsensor.kicad_pcb
```

- [ ] **Step 2: Inspect both sides**

Verify:

- Module orientations match silkscreen.
- No module extends beyond the board except intended USB/cable access.
- SCD30 air openings and BH1750 optical path are clear.
- ESP32 and LD2450 keep-outs contain no copper.
- Connector pin 1 and SEN55 pin labels are unambiguous.
- Power and signal tracks do not have accidental neck-downs.

- [ ] **Step 3: Run final checks**

```bash
/Applications/KiCad/KiCad.app/Contents/MacOS/kicad-cli sch erc \
  --format json --severity-all --exit-code-violations \
  --output envsensor/reports/erc.json \
  envsensor/envsensor.kicad_sch

/Applications/KiCad/KiCad.app/Contents/MacOS/kicad-cli pcb drc \
  --format json --severity-all --schematic-parity \
  --refill-zones --save-board --exit-code-violations \
  --output envsensor/reports/drc.json \
  envsensor/envsensor.kicad_pcb
```

Expected: both commands exit 0.

- [ ] **Step 4: Commit final review artifacts**

```bash
git add envsensor/reports envsensor/envsensor.kicad_sch \
  envsensor/envsensor.kicad_pcb envsensor/envsensor.kicad_pro
git commit -m "test(kicad): add final design checks"
```

## Final Acceptance

- Active schematic and PCB open without missing libraries.
- References and values match the approved design.
- Board outline is exactly 52 x 70 mm.
- No mounting holes are present.
- SSD1327 uses SPI; sensors use I2C; LD2450 uses hardware UART.
- USB-derived rails power all modules correctly.
- SEN55 uses the six-pin JST GH cable connector and sits off-board.
- Layout A is implemented.
- ERC and DRC with schematic parity pass.
- Front and back renders show no mechanical or orientation defect.
