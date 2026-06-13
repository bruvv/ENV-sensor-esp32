# ENV Sensor Hardware Design

## Scope

Finish and clean the KiCad schematic and PCB in `envsensor/`.

The board uses complete modules:

- ESP32-S3 DevKit from the existing project footprint
- Sensirion SCD30
- Sensirion SEN55, connected by cable
- MS5611 breakout
- Waveshare 1.5-inch SSD1327 128x128 OLED module
- BH1750 breakout
- HLK-LD2450 using the existing project footprint and onboard header

The PCB remains 52 x 70 mm. It has no mounting holes.

## Interfaces

### I2C

Use one shared 3.3 V I2C bus:

- SDA: ESP32-S3 GPIO8
- SCL: ESP32-S3 GPIO9
- SCD30: address 0x61
- SEN55: address 0x69
- BH1750: address 0x23
- MS5611: address 0x77

Add one pair of 4.7 kOhm pull-up resistors to 3.3 V. Keep the bus suitable
for the external SEN55 cable. Connect SEN55 SEL to GND to select I2C.

### Display

Use the SSD1327 in its factory-default four-wire SPI mode:

- MOSI: GPIO11
- CLK: GPIO12
- CS: GPIO10
- DC: GPIO13
- RESET: GPIO14

The display is powered from 3.3 V.

### Radar

Use a hardware UART for the LD2450:

- ESP32 TX: GPIO17 to LD2450 RX
- ESP32 RX: GPIO18 from LD2450 TX
- 256000 baud, no parity, one stop bit

Preserve the existing LD2450 footprint as the mechanical basis.

## Power

Power the assembly through the ESP32-S3 DevKit USB connector.

- 5VIN powers SCD30, SEN55, and LD2450.
- 3V3 powers SSD1327, BH1750, MS5611, and I2C pull-ups.
- Add local bulk and decoupling capacitance on both rails.
- Use wider 5 V tracks for the fan-based SEN55 and other 5 V modules.

Do not use ESP32-S3 strapping pins, native USB pins, or flash/PSRAM pins
for these interfaces.

## Connectors And Assembly

- SCD30, MS5611, BH1750, SSD1327, and ESP32-S3 are soldered to the board.
- SEN55 sits away from the PCB and connects through a JST GHR-06V-S
  six-pin cable connector.
- LD2450 plugs directly into the existing onboard header arrangement.
- Place clear silkscreen labels for module names, connector pinout, pin 1,
  power rails, and orientation.

## Mechanical Layout

Use layout option A.

Front:

- SSD1327 at the top.
- BH1750 below the display with an unobstructed light path.
- LD2450 at the bottom, facing into the room.

Back:

- ESP32-S3 on the left, with USB accessible at the bottom edge.
- SCD30 on the right with unobstructed airflow openings.
- MS5611 below the SCD30 and away from heat sources.
- SEN55 connector at the bottom edge.

Keep SCD30 and MS5611 away from the ESP32 regulator and other heat sources.
Respect antenna and copper keep-out requirements for the ESP32-S3 and
LD2450.

## Schematic Structure

Organize the schematic into readable blocks:

1. Power and decoupling
2. ESP32-S3
3. I2C sensors
4. SSD1327 display
5. LD2450 radar

Use these references:

- U1: ESP32-S3
- U2: SCD30
- U3: MS5611
- U4: BH1750
- U5: SSD1327
- J1: SEN55
- U6: LD2450

Mark intentionally unused module pins as no-connect.

## PCB Routing

- Use two copper layers with GND pours.
- Keep I2C tracks short, grouped, and away from noisy power routing.
- Keep SPI routing short between ESP32-S3 and SSD1327.
- Route UART separately from sensor lines.
- Provide clean return paths and stitching vias where useful.
- Preserve the 52 x 70 mm rectangular board outline.

## Validation

- Run KiCad ERC on the finished schematic.
- Run KiCad DRC on the fully placed and routed PCB.
- Resolve all actionable findings.
- Document any accepted warning with a concrete hardware reason.
- Confirm footprints, connector orientation, module clearances, power
  rails, and pin mappings against manufacturer documentation.

ESPHome configuration is outside this task.

## Sources

- ESPHome SSD1327:
  https://esphome.io/components/display/ssd1327/
- ESPHome I2C:
  https://esphome.io/components/i2c/
- ESPHome SCD30:
  https://esphome.io/components/sensor/scd30/
- ESPHome SEN5x:
  https://esphome.io/components/sensor/sen5x/
- ESPHome BH1750:
  https://esphome.io/components/sensor/bh1750/
- ESPHome MS5611:
  https://esphome.io/components/sensor/ms5611/
- ESPHome LD2450:
  https://esphome.io/components/sensor/ld2450/
- Waveshare SSD1327 module:
  https://www.waveshare.com/wiki/1.5inch_OLED_Module
- Sensirion SEN5x datasheet:
  https://sensirion.com/media/documents/6791EFA0/62A1F68F/Sensirion_Datasheet_Environmental_Node_SEN5x.pdf
- Espressif ESP32-S3 GPIO restrictions:
  https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/api-reference/peripherals/gpio.html
