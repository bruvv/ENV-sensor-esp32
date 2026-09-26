# ENV Sensor ESP32

Custom ESP32-S3 environmental sensor PCB designed for use with ESPHome and Home Assistant.

The board combines several environmental sensors, a 128×128 OLED display and an HLK-LD2450 mmWave presence sensor on a compact custom PCB.

## Features

- ESP32-S3 DevKitC N16R8
- Sensirion SCD30 CO₂ sensor
- Sensirion SEN55 environmental sensor
- MS5611 barometric pressure sensor
- BH1750 ambient light sensor
- ZJY-M150 128×128 OLED with SSD1327 controller
- HLK-LD2450 mmWave presence sensor
- Shared I²C bus for environmental sensors
- SPI interface for the OLED display
- Hardware UART for the LD2450
- 5 V and 3.3 V power rails
- 4-layer PCB
- 55 × 70 mm board
- Designed in KiCad 10

## Sensors

| Component | Function | Supply | Interface |
| --- | --- | ---: | --- |
| ESP32-S3 DevKitC N16R8 | Main controller, Wi-Fi and Bluetooth | 5 V | - |
| Sensirion SCD30 | CO₂, temperature and humidity | 5 V | I²C |
| Sensirion SEN55 | PM1.0, PM2.5, PM4, PM10, VOC, NOx, temperature and humidity | 5 V | I²C |
| MS5611 | Barometric pressure | 3.3 V | I²C |
| BH1750 | Ambient light | 3.3 V | I²C |
| ZJY-M150 / SSD1327 | 128×128 OLED display | 3.3 V | SPI |
| HLK-LD2450 | mmWave presence and multi-target tracking | 5 V | UART |

## ESP32 Pin Mapping

### I²C

| Signal | ESP32-S3 |
| --- | --- |
| SDA | GPIO8 |
| SCL | GPIO9 |

The environmental sensors share the I²C bus.

| Sensor | I²C address |
| --- | ---: |
| SCD30 | `0x61` |
| SEN55 | `0x69` |
| BH1750 | `0x23` |
| MS5611 | `0x77` |

The PCB contains 4.7 kΩ pull-up resistors for SDA and SCL.

## SSD1327 OLED

The ZJY-M150 OLED is operated in 4-wire SPI mode.

| OLED signal | ESP32-S3 |
| --- | --- |
| CS | GPIO10 |
| MOSI / SDA | GPIO11 |
| CLK / SCL | GPIO12 |
| DC | GPIO13 |
| RESET | GPIO14 |

## HLK-LD2450

The LD2450 uses a dedicated hardware UART.

| Signal | ESP32-S3 |
| --- | --- |
| LD2450 RX | GPIO17 / ESP32 TX |
| LD2450 TX | GPIO18 / ESP32 RX |

UART configuration:

- Baud rate: `256000`
- Data bits: `8`
- Parity: none
- Stop bits: `1`

## PCB

The current PCB revision is designed in KiCad 10.

| Property | Value |
| --- | --- |
| Dimensions | 55 × 70 mm |
| Copper layers | 4 |
| Top copper | `F.Cu` |
| Inner layer 1 | `In1.Cu` |
| Inner layer 2 | `In2.Cu` |
| Bottom copper | `B.Cu` |

The repository contains the KiCad project files as well as production Gerber and drill files.

## LD2450 Connector

The HLK-LD2450 is connected through an 8-pin dual-row SMD female socket.

Required connector specification:

| Property | Requirement |
| --- | --- |
| Type | Female PCB socket |
| Contacts | 8 |
| Rows | 2 × 4 |
| Pitch | 2.00 mm |
| Mounting | SMD / SMT |
| Orientation | Straight |

A suitable branded connector is:

**Würth Elektronik 62100821821**

- 2 × 4
- 8 contacts
- 2.00 mm pitch
- Female
- SMD

For generic alternatives, search for:

`2.0mm 2x4 8 pin female SMD header`

## SEN55 Connection

The SEN55 is mounted away from the main PCB to keep its airflow unobstructed.

The connection provides:

| Signal |
| --- |
| 5 V |
| GND |
| SDA |
| SCL |
| GND / SEL |

## Bill of Materials

| Qty | Component | Notes |
| ---: | --- | --- |
| 1 | ESP32-S3 DevKitC N16R8 | ESP32-S3-WROOM based |
| 1 | Sensirion SCD30 | CO₂ sensor |
| 1 | Sensirion SEN55 | Environmental and particulate sensor |
| 1 | MS5611 module | Pressure sensor |
| 1 | BH1750 module | Ambient light sensor |
| 1 | ZJY-M150 128×128 OLED | SSD1327 controller, 7-pin |
| 1 | HLK-LD2450 | mmWave presence sensor |
| 1 | 2×4 2.00 mm female SMD socket | LD2450 connector |
| 2 | 4.7 kΩ resistor | I²C pull-ups |

## Design Considerations

### SCD30

The SCD30 should be kept away from heat sources and strong direct airflow that could influence its temperature and CO₂ measurements.

Datasheet:  
https://sensirion.com/media/documents/4EAF6AF8/61652C3C/Sensirion_CO2_Sensors_SCD30_Datasheet.pdf

### SEN55

The SEN55 requires unobstructed airflow through its inlet and outlet.

For best performance:

- Keep the inlet and outlet separated.
- Avoid recirculating exhaust air into the inlet.
- Keep the sensor away from heat sources.
- Orient the sensor so dust cannot easily accumulate inside.
- Keep the fan inlet and exhaust unobstructed.

Datasheet:  
https://sensirion.com/media/documents/6791EFA0/62A1F68F/Sensirion_Datasheet_Environmental_Node_SEN5x.pdf

### MS5611

Keep the pressure sensor away from the ESP32 voltage regulator and other heat-producing components.

Datasheet:  
https://www.te.com/commerce/DocumentDelivery/DDEController?Action=showdoc&DocId=Data+Sheet%7FMS5611-01BA03%7FB3%7Fpdf%7FEnglish%7FENG_DS_MS5611-01BA03_B3.pdf%7FCAT-BLPS0036

### BH1750

The BH1750 needs a clear optical path to the room.

Avoid placing it behind opaque enclosure material or in the shadow of other components.

Datasheet:  
https://components101.com/sites/default/files/component_datasheet/BH1750.pdf

### SSD1327 OLED

The display is a ZJY-M150 1.5-inch 128×128 OLED using an SSD1327 controller.

The PCB uses SPI for the display.

SSD1327 datasheet:  
https://www.waveshare.com/w/upload/a/ac/SSD1327-datasheet.pdf

### HLK-LD2450

The LD2450 provides mmWave presence detection and multi-target position tracking.

Placement and enclosure design can significantly affect radar performance.

Recommendations:

- Face the radar toward the room.
- Avoid metal directly in front of the antenna.
- Keep sufficient clearance around the antenna area.
- Avoid placing large copper areas or metal parts directly in the radar field of view.

## Repository Structure

`envsensor/`  
KiCad schematic, PCB project and project-specific libraries.

`gerber/`  
Production Gerber and drill files.

`datasheet/`  
Component documentation.

`docs/`  
Hardware design and implementation documentation.

`README.md`  
Project documentation.

## Manufacturing

The `gerber/` directory contains the generated PCB production files, including:

- `F.Cu`
- `In1.Cu`
- `In2.Cu`
- `B.Cu`
- `F.Mask`
- `B.Mask`
- `F.Paste`
- `B.Paste`
- `F.Silkscreen`
- `B.Silkscreen`
- `Edge.Cuts`
- PTH drill file
- NPTH drill file

The repository also contains `gerber/gerber.zip`, which can be uploaded to most PCB manufacturers.

Always verify the Gerbers in the PCB manufacturer's Gerber viewer before placing an order.

## Software

The board is intended for use with ESPHome and Home Assistant.

Useful ESPHome documentation:

- SCD30: https://esphome.io/components/sensor/scd30/
- SEN5x: https://esphome.io/components/sensor/sen5x/
- BH1750: https://esphome.io/components/sensor/bh1750/
- MS5611: https://esphome.io/components/sensor/ms5611/
- SSD1327: https://esphome.io/components/display/ssd1327/
- LD2450: https://esphome.io/components/sensor/ld2450/

## License

See [LICENSE](LICENSE).
