# KiCad-library voor ZJY-M150-128x128-7P

Deze library is bedoeld voor de module op de aangeleverde foto's:

- Opschrift: `ZJY-M150-128*128-7P`
- Fabrikant-ID: `ZJY150S0700WG01`
- Controller: `SSD1327`
- Resolutie: 128 x 128
- Interface: standaard 4-wire SPI, ombouwbaar naar I2C
- Module-afmetingen: 34,00 x 47,00 mm
- Montagegatafstand: 29,00 x 42,00 mm
- Header: 1 x 7, steek 2,54 mm
- Pinvolgorde: GND, VCC, SCL, SDA, RES, DC, CS

## Inhoud

- `SSD1327_ZJY_M150.kicad_sym`: schemasymbool
- `ZJY_M150_128x128_7P.pretty/`: footprint-library
- `sym-lib-table`: voorbeeld om de symbool-library projectlokaal te registreren
- `fp-lib-table`: voorbeeld om de footprint-library projectlokaal te registreren

## Installatie in een KiCad-project

Kopieer de volledige inhoud van deze map naar de hoofdmap van je KiCad-project.

Open daarna KiCad en laad het project opnieuw. De meegeleverde `sym-lib-table` en
`fp-lib-table` registreren de libraries via `${KIPRJMOD}`.

Je kunt de libraries ook handmatig toevoegen via:

- Preferences > Manage Symbol Libraries
- Preferences > Manage Footprint Libraries

## Pinout

| Pin | Naam | Functie |
|---:|---|---|
| 1 | GND | Massa |
| 2 | VCC | Voeding module, 3 tot 5 V |
| 3 | SCL | SPI-clock of I2C-clock |
| 4 | SDA | SPI-data of I2C-data |
| 5 | RES | Reset, actief laag |
| 6 | DC | Data/command bij SPI |
| 7 | CS | Chip select bij SPI, actief laag |

## Belangrijke elektrische opmerking

De modulespecificatie noemt 3 tot 5 V voor VCC, maar maximaal 3,3 V voor de
logicasignalen SCL, SDA, RES, DC en CS. Gebruik dus 3,3 V-logica of passende
level shifting.

## SPI naar I2C

Volgens de modulespecificatie:

1. Verplaats de 0-ohm weerstand van R4 naar R5.
2. Verbind DC en CS met GND.
3. Gebruik de I2C-driverconfiguratie.

De module staat vanaf de fabriek standaard op SPI.

## Mechanische aannames

De PCB-maat, gatafstanden, actieve displaymaat en headerpositie zijn gebaseerd
op de mechanische tekening voor `ZJY150S0700WG01`.

De vier montagegaten zijn als 3,0 mm NPTH opgenomen. Controleer dit bij voorkeur
met een schuifmaat voordat je een definitieve PCB bestelt. De actieve displayzone
op `Dwgs.User` is ter referentie en is niet bedoeld als koper- of soldeermaskerkeepout.
