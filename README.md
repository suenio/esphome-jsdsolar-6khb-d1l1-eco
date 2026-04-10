# esphome-jsdsolar


ESPHome example configuration to monitor and control aJSD 6KHB inverter via rs485

## Supported devices

* JSD 6KHB-D1/LV-E - JSDSolar available on aliexpress


## Requirements

* [ESPHome 2024.6.0 or higher](https://github.com/esphome/esphome/releases).
* Plugs provided by inverter distributor (attached in package)
* LilyGo-RS485/CAN or self assembled Generic ESP32 board with RS485toTTL converter 

## Schematics

<a href="https://github.com/suenio/esphome-jsdsolar-6hkb-d1l1-eco/blob/main/images/jsdsolar-img2.png" target="_blank">
  <img src="https://github.com/suenio/esphome-jsdsolar-6hkb-d1l1-eco/blob/main/images/jsdsolar-img2.png" height="250">
</a>

Inverter provides rs485 pins A and B on connector related to CT connection

<a href="https://github.com/suenio/esphome-jsdsolar-6hkb-d1l1-eco/blob/main/images/jsdsolar-img1.png" target="_blank">
  <img src="https://github.com/suenio/esphome-jsdsolar-6hkb-d1l1-eco/blob/main/images/jsdsolar-img1.png" height="250">
</a>

```
LilyGo-RS485/CAN version

                  rs485                   
┌──────────┐                 ┌──────────┐      
│          │                 │          │
│          │<─────  A  ─────>│  LilyGo  │
│   JSD    │<─────  B  ─────>│  rs485   │
│   6KHB   │<───── GND ─────>│  / CAN   │<── VCC ──┐
│          │<─┐         │    │          │<── GND ┐ │ 
└──────────┘  │         │    └──────────┘        │ │       
              |         └────────────────────────┘ │ 
              └─ 5V ───────────────────────────────┘  
              
```               

```
self assembled Generic ESP32 board with RS485toTTL converter 
  
                  rs485                        UART-TTL           
┌──────────┐                 ┌──────────┐                   ┌──────────┐
│          │                 │          │                   │          │
│          │<─────  A  ─────>│  LilyGo  │<───  RX - TX  ───>│          │
│   JSD    │<─────  B  ─────>│  rs485   │<───  TX - RX  ───>│  ESP32   │
│   6KHB   │<───── GND ─────>│  / CAN   │<─────  GND  ─────>│          │<────── VCC ──┐
│          │<─┐         │    │          │<─── 3.3V VCC ────>│          │<────── GND ┐ │ 
└──────────┘  │         │    └──────────┘                   └──────────┘            │ │       
              |         └───────────────────────────────────────────────────────────┘ │ 
              └─ 5V ──────────────────────────────────────────────────────────────────┘  

```

The inverter provides +5V and GND on pin 1 and 4 of "hidden"  USB (Data Logger) connector. (See picture below but keep in mind that +5V is blue wire and GND is red wire on the picture). 

<a href="https://github.com/suenio/esphome-jsdsolar-6hkb-d1l1-eco/blob/main/images/jsdsolar-img4.jpeg" target="_blank">
  <img src="https://github.com/suenio/esphome-jsdsolar-6hkb-d1l1-eco/blob/main/images/jsdsolar-img4.jpeg" height="400">
</a>

For simplicity you can buy 4pin JST XH connector

Also you can use a cheap DC-DC converter to power the ESP with 3.3V but most oe esp32 boards have 5V input and 3.3V output.

Finally your results after installing esphome code on the lilygo device will be:

<a href="https://github.com/suenio/esphome-jsdsolar-6hkb-d1l1-eco/blob/main/images/jsdsolar-img3.png" target="_blank">
  <img src="https://github.com/suenio/esphome-jsdsolar-6hkb-d1l1-eco/blob/main/images/jsdsolar-img3.png" height="1200">
</a>

## Installation in home assistant

Use the `eesphome-jsdsolar-6hkbd1l1-eco.yaml` for testing purposes

```bash
# Install esphome addon in home assistant


# Create new device in esphome plugin using "Empty configuration" option and copy and paste content of yaml file

# Create a secret.yaml containing some setup specific secrets

wifi_ssid: YOUR_WIFI_SSID
wifi_password: YOUR_WIFI_PASSWORD

api_key: YOUR_API_KEY
ota_password: YOUR_OTA_PASSWORD

# Validate the configuration, create a binary, upload it, and start logs

```

## Known issues

1. This is work in progress but majority sensors works out of the box keep that in mind when using automations

2. Not tested on esp8266 but most likely with proper configuration and build it will work

3. If you configure a lot of the possible sensors etc. it could be that you run out of memory (on esp32). If you configure nearly all sensors etc. you run in a stack-size issue. In this case you have to increase stack size: https://github.com/esphome/issues/issues/855


## Work in progress

1. ABility to modify time control periods.

    From Reverse engineering activities data related to time based configuration periods is stored in 6 blocks with 8 registers per block starting at register 0x2600. Each block contains:

      Register 0 - Bit controls responsible for settings of charge, discharge, once, weekly params in menu
          
          First 4 bits of Register 0
            Register0 Bit0-1
               if value 0 this time period is disabled  
               if value 1 this time period is in charge mode and enabled 
               if value 2 this time period is in discharge mode and enabled 
            Register0 Bit2 
              if 0 this time persiod is executed once and is using as active day date provided in Register6
              if set to 1 is in weekly mode and is using daily time range and days activated within a week (Register0 Bits-4-10)  
            Register0 Bit3 - TBD but so far always set to 0 

          Next 7 bits of Register 0 - used if time based control is set to weekly
            Register0 Bit4 - if 0 Monday is not actie for time control and if 1 Monday is active for time control
            Register0 Bit5 - if 0 Tuesday is not actie for time control and if 1 Tuesday is active for time control
            Register0 Bit6 - if 0 Wednesday is not actie for time control and if 1 Wednesday is active for time control
            Register0 Bit7 - if 0 Thursday is not actie for time control and if 1 Thursday is active for time control
            Register0 Bit8 - if 0 Friday is not actie for time control and if 1 Friday is active for time control
            Register0 Bit9 - if 0 Saturday is not actie for time control and if 1 Saturday is active for time control
            Register0 Bit10 - if 0 Sunday is not actie for time control and if 1 Sunday is active for time control 
      
      Register 1 - used to store definition of start time within a active day

            Register contains two bytes. One byte is an hour and another is minute of the day
            Example:
              Register1 value: 0x0909 -> 9:09AM
              Register1 value: 0x0F0F -> 15:15 or 3:15PM
      
      Register 2 - used to store definition of end time within a active day

            Register contains two bytes. One byte is an hour and another is minute of the day
            Example:
              Register1 value: 0x0909 -> 9:09AM
              Register1 value: 0x0F0F -> 15:15 or 3:15PM
      
      Register 3 - TBD but so far always value 0x0000
      
      Register 4 - used to store definition of power level for charge or discharge durign this time period. Decimal value in Watts (W)
      
      Register 5 - used to store definition of Battery Level SOC (%) to stop activity (either charge or discharge) 
      
      Register 6 - TBD but so far always value 0x01C2 (450 decimal)
      
      Register 7 - used to store definition of active day when in "once active" mode

            Register7 Bit0-4 - day of the month eg. value 0x3 in those bits means 3 day of the month
            Register7 Bit5-8 - month eg. value 0x2 in those bits means February
            Register7 Bit9-Bit14 - year since 2020 but no more than 2025 eg. value 0xA in those bits means 2030 

    Lookiong for volenteers that would map this in yaml and create sonfiguration dashboard in home assistant
      

2. Mapping of registers to inverter menu and documenting this in yaml file. Looking for volenteers ;) 

