# ENV-sensor-esp32

Consists off the following sensors:

- ESP32-S3 DevKitC N16R8 Development Board WiFi/Buetooth
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
See also the manufacureres [Design-In guidelines](https://sensirion.com/media/documents/4EAF6AF8/61652C3C/Sensirion_CO2_Sensors_SCD30_Datasheet.pdf).

- Avoid air currents over the device
- Avoid ambient heat

## SEN55 (Particulate matter)
See manufacturers [Mechanical Design and Assembly Guidelines](https://sensirion.com/media/documents/6791EFA0/62A1F68F/Sensirion_Datasheet_Environmental_Node_SEN5x.pdf)

-  Inlet and outlet must be well insulated from each other by proper sealing
-  Placing the sensor with the inlets/outlet facing down avoids dust accumulation and accelerated sensor aging
-  Decouple from external heat sources

## LD2450 (Radar precense detection)
[Datasheet](https://make.net.za/wp-content/datasheets/HLK%20LD2450%20Serial%20Communication%20Protocol%20v1.03.pdf)

- 6m detection range
- Azimuth angle ±60°, pitch angle ±35°
  - ?? volgens mij is het ±30° of 60° totaal...???
- Recommended installation height range: 1.5m - 2m

## MS5611 (Barometric Pressure Sensor)
[Datasheet](https://www.te.com/commerce/DocumentDelivery/DDEController?Action=showdoc&DocId=Data+Sheet%7FMS5611-01BA03%7FB3%7Fpdf%7FEnglish%7FENG_DS_MS5611-01BA03_B3.pdf%7FCAT-BLPS0036)

- Can be placed everywhere

## ZJY-M150-128x128-7P SSD1327 (Screen)
[Datasheet](https://www.waveshare.com/w/upload/a/ac/SSD1327-datasheet.pdf)

- Works great!

## BH1750 (Ambient Light Sensor)
[Datasheet](https://components101.com/sites/default/files/component_datasheet/BH1750.pdf)
- Make sure it receive light.
