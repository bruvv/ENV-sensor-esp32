# ENV-sensor-esp32

Consists off the following sensors:

- ESP32-S3
- SCD30 (CO2)
- SEN55 (Dust)
- MS5611 (Pressure)
- SSD1327 (Screen)
- BH1750 (LUX)

| Sensor   |   V   |  mA | SDA | SCL |
|----------|-------|-----|-----|-----|
| ESP32-S3 |       |     |     |     |
| SCD30    |   5   | 75  |     |     |
| SEN55    |   5   | 110 |     |     |
| MS5611   |  3.3  | >1  |     |     |
| SSD1327  |  3.3  | ~50 |     |     |
| BH1750   |   5   | >1  |     |     |

# Considerations per sensor
Each sensor has requirements in order to give reliable readings. These are summarized below:

## SCD30 (CO2)
See also the manufacureres [Design-In guidelines](https://sensirion.com/media/documents/84D49268/616536CB/Sensirion_CO2_Sensors_SCD30_Design-In_Guidelines_D1.pdf).

- Avoid air currents over the device
- Avoid ambient heat

## SEN55 (Particulate matter)
See manufacturers [Mechanical Design and Assembly Guidelines](https://sensirion.com/media/documents/546FBC5B/61E9586E/Sensirion_Mechanical_Design_and_Assembly_Guidelines_SEN5x.pdf)

-  Inlet and outlet must be well insulated from each other by proper sealing
-  Placing the sensor with the inlets/outlet facing down avoids dust accumulation and accelerated sensor aging
-  Decouple from external heat sources

## LD2450 (Radar precense detection)
[Datasheet](https://www.tinytronics.nl/product_files/006000_HLK-LD2450-Instruction-Manual.pdf)

- 6m detection range
- Azimuth angle ±60°, pitch angle ±35°
  - ?? volgens mij is het ±30° of 60° totaal...???
- Recommended installation height range: 1.5m - 2m
- 
