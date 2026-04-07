# esphome-pipsolar


ESPHome example configuration to monitor and control aJSD 6KHB inverter via rs485

## Supported devices

* JSD 6KHB-D1/LV-E - JSDSolar available on aliexpress


## Requirements

* [ESPHome 2024.6.0 or higher](https://github.com/esphome/esphome/releases).
* Plugs provided by inverter distributor (attached in package)
* LilyGo-RS485/CAN or self assembled Generic ESP32 board with RS485toTTL converter 

## Schematics

<a href="https://github.com/suenio/esphome-jsdsolar-6hkb-d1l1-eco/blob/main/images/jsdsolar-img2.png" target="_blank">
  <img src="https://github.com/suenio/esphome-jsdsolar-6hkb-d1l1-eco/blob/main/images/jsdsolar-img2.png" height="172">
</a>

Inverter provides rs485 pins A and B on connector related to CT connection

<a href="https://github.com/suenio/esphome-jsdsolar-6hkb-d1l1-eco/blob/main/images/jsdsolar-img1.png" target="_blank">
  <img src="https://github.com/suenio/esphome-jsdsolar-6hkb-d1l1-eco/blob/main/images/jsdsolar-img1.png" height="172">
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

The inverter provides +5V and GND on pin 1 and 4 of "hidden"  USB (Data Logger) connector. 

You can use a cheap DC-DC converter to power the ESP with 3.3V but most oe esp32 boards have 5V input and 3.3V output.


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

