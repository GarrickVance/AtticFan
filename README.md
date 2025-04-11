# Attic Fan Controller

[![ESPHome](https://img.shields.io/badge/ESPHome-2025.3.3-blue.svg)](https://esphome.io/)
[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)
[![ESP32-S2](https://img.shields.io/badge/ESP32--S2-SAOLA--1-green.svg)](https://docs.espressif.com/projects/esp-idf/en/latest/esp32s2/hw-reference/esp32s2/user-guide-saola-1-v1.2.html)

An ESP32-based smart attic fan controller that automatically manages fan operation based on temperature thresholds.

> This project is a fork of [AtticFan](https://github.com/garrick/AtticFan) with modifications for attic fan control.

## Features
- Temperature-based automatic fan control
- Web interface for monitoring and control
- Real-time temperature, humidity, and pressure monitoring
- 30-second update intervals to prevent rapid cycling
- Automatic and manual operation modes
- OTA (Over-The-Air) updates supported

## Hardware Components (BOM)
- [ESP32-S2-SAOLA-1 Development Board](https://www.amazon.com/dp/B0BXX6R15D) - ESP32-S2 board with built-in USB programming
- [BME280 Temperature/Humidity/Pressure Sensor](https://www.amazon.com/dp/B0DHPCFJD6) - I2C interface sensor module
- [5V Relay Module](https://www.amazon.com/dp/B00LW15A4W) - Single channel relay with optocoupler isolation
- [2A Circuit Breaker](https://www.amazon.com/dp/B0DJ9347WK) - Manual reset thermal circuit breaker for overload protection
- Standard USB 5V wall adapter - Any USB power supply that can provide at least 1A

### Pin Connections
- I2C Interface:
  - SDA: GPIO9
  - SCL: GPIO7
- Relay Control:
  - Signal: GPIO5 (inverted logic)
  - VCC: 5V
  - GND: GND

### Pinout Diagram
```
ESP32-S2-SAOLA-1 Pinout:
┌─────────────────────────────────────────────┐
│                                             │
│  USB-C Port                                 │
│  ┌─────────────────────────────────────┐   │
│  │                                     │   │
│  │                                     │   │
│  └─────────────────────────────────────┘   │
│                                             │
│  GPIO Pins:                                 │
│  ┌─────┬─────┬─────┬─────┬─────┬─────┐     │
│  │ 5V  │ GND │ 3V3 │ EN  │ 7   │ 8   │     │
│  ├─────┼─────┼─────┼─────┼─────┼─────┤     │
│  │ 9   │ 10  │ 11  │ 12  │ 13  │ 14  │     │
│  ├─────┼─────┼─────┼─────┼─────┼─────┤     │
│  │ 15  │ 16  │ 17  │ 18  │ 19  │ 20  │     │
│  └─────┴─────┴─────┴─────┴─────┴─────┘     │
│                                             │
└─────────────────────────────────────────────┘
```

### Wiring Diagram
```
Power Supply (5V USB) ───────┐
                            │
                            ▼
┌─────────────────────────────────────────────┐
│ ESP32-S2-SAOLA-1                            │
│                                             │
│ 5V ────────────────────────────────────────┐│
│ GND ───────────────────────────────────────┐│
│ GPIO5 ─────────────────────────────────────┐│
│ GPIO7 (SCL) ──────────────────────────────┐│
│ GPIO9 (SDA) ──────────────────────────────┐│
└─────────────────────────────────────────────┘
    │        │        │        │        │
    │        │        │        │        │
    ▼        ▼        ▼        ▼        ▼
┌─────────────────────────────────────────────┐
│ BME280 Sensor                               │
│                                             │
│ VCC ────────────────────────────────────────┘
│ GND ────────────────────────────────────────┘
│ SCL ────────────────────────────────────────┘
│ SDA ────────────────────────────────────────┘
└─────────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────────┐
│ 5V Relay Module                             │
│                                             │
│ VCC ────────────────────────────────────────┘
│ GND ────────────────────────────────────────┘
│ IN  ────────────────────────────────────────┘
│                                             │
│ NO ────────────────────────────────────────┐
│ COM ───────────────────────────────────────┐
└─────────────────────────────────────────────┘
    │        │
    ▼        ▼
┌─────────────────────────────────────────────┐
│ Attic Fan                                  │
│                                             │
│ Hot ────────────────────────────────────────┘
│ Neutral ────────────────────────────────────┘
└─────────────────────────────────────────────┘
```

### Power Requirements
- Input: 100-240V AC
- Output: 5V DC, 2.5A
- Power consumption: < 500mA typical

## Configuration
```yaml
# Key configuration settings:
esphome:
  name: attic-fan
  friendly_name: attic-fan

# Temperature threshold settings
number:
  - platform: template
    name: "Temperature Threshold"
    min_value: 75
    max_value: 120
    step: 1
    unit_of_measurement: "°F"
    initial_value: 100

# Sensor update interval
sensor:
  - platform: bme280_i2c
    update_interval: 30s
```

## Installation
1. Install ESPHome
2. Copy the configuration file
3. Create a `secrets.yaml` file with your WiFi credentials:
   ```yaml
   wifi_ssid: "your_wifi_ssid"
   wifi_password: "your_wifi_password"
   web_username: "your_web_username"
   web_password: "your_web_password"
   ```
4. Flash to ESP32:
   - For ESP32-S2-SAOLA-1: Use the Adafruit WebSerial ESPTool (https://adafruit.github.io/Adafruit_WebSerial_ESPTool/) as the web-based flashing method may not work
   - Steps for manual flashing:
     1. Connect the ESP32 to your computer via USB
     2. Open the Adafruit WebSerial ESPTool in your browser
     3. Select the correct port
     4. Upload the generated .bin file
     5. Wait for the flashing process to complete
5. Access web interface at attic-fan.local

## Web Interface Features
- Current temperature display in °F
- Current humidity and pressure readings
- Temperature threshold adjustment
- Automatic/Manual mode toggle
- Real-time fan status

## Contributing
Based on ESPHome 2025.3.3. Feel free to submit issues or pull requests.

## License
This project is licensed under the Creative Commons Attribution-NonCommercial 4.0 International License (CC BY-NC 4.0).

[![License: CC BY-NC 4.0](https://licensebuttons.net/l/by-nc/4.0/80x15.png)](https://creativecommons.org/licenses/by-nc/4.0/)

You are free to:
- Share — copy and redistribute the material in any medium or format
- Adapt — remix, transform, and build upon the material

Under the following terms:
- Attribution — You must give appropriate credit, provide a link to the license, and indicate if changes were made
- NonCommercial — You may not use the material for commercial purposes

For more information about the license, visit: https://creativecommons.org/licenses/by-nc/4.0/
