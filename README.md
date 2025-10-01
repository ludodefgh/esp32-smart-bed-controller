# Smart Bed Controller with ESP32 and ESPHome

Transform your adjustable bed into a smart home device using an ESP32 microcontroller and ESPHome. This project allows you to control your bed remotely through Home Assistant while preserving the original remote functionality.

## Table of Contents

- [Overview](#overview)
- [Hardware Requirements](#hardware-requirements)
- [Wiring Guide](#wiring-guide)
- [ESPHome Configuration](#esphome-configuration)
- [Home Assistant Integration](#home-assistant-integration)
- [3D Printed Case](#3d-printed-case)
- [Installation Steps](#installation-steps)
- [Troubleshooting](#troubleshooting)
- [Safety Considerations](#safety-considerations)

## Overview

This project enables smart control of adjustable beds by intercepting and replicating the signals from the original remote control. The ESP32 board acts as an intermediary, allowing both the original remote and Home Assistant to control the bed functions.

### Features

- Control bed head and foot positions remotely via Home Assistant
- Preserve original remote functionality
- WiFi connectivity for smart home integration
- Status LED indicator
- Safe relay-based switching

## Hardware Requirements

### Essential Components

| Component | Description | Example Link |
|-----------|-------------|--------------|
| ESP32 Development Board | 4-relay ESP32 board with 7-30V power input | [Amazon Canada](https://www.amazon.ca/dp/B0DF2KVQ3X) |
| USB to TTL Converter | For programming the ESP32 board | [Amazon Canada](https://www.amazon.ca/dp/B07D6LLX19) |
| DIN5 Extension Cable | To interface with bed's control system | [Amazon Canada](https://www.amazon.ca/dp/B0FK5RSHBZ) |

### Tools Needed

- Wire strippers
- Soldering iron and solder
- Multimeter (recommended)
- Screwdriver set

## Wiring Guide

### Understanding the Original Remote

The bed remote uses a DIN5 connector with the following pin configuration:

| DIN5 Pin | Wire Color | Function |
|----------|------------|----------|
| 1 | Red | Motor 2, Terminal 1 (Feet Up) |
| 2 | Yellow | Motor 1, Terminal 1 (Head Up) |
| 3 | Green | Positive (+) Power |
| 4 | Blue/Orange* | Motor 2, Terminal 2 (Feet Down) |
| 5 | Black | Motor 1, Terminal 2 (Head Down) |
| 6 | White | Negative (-) Power |

*Note: Extension cable may use orange instead of blue

### Power Connections

- **Green (+)**: Connect to ESP32 7-30V input and daisy-chain to all relay NO (Normally Open) contacts
- **White (-)**: Connect to ESP32 GND input and daisy-chain to all relay NC (Normally Closed) contacts

### Motor Control Wiring

Split the extension cable, connect the male end to the bed and wire the cables to the board as following :

| ESP32 Relay | Wire Color | Function | DIN5 Pin |
|-------------|------------|----------|----------|
| Switch 1 COM | Black | Head Down | Pin 5 |
| Switch 2 COM | Orange/Blue | Feet Down | Pin 4 |
| Switch 3 COM | Yellow | Head Up | Pin 2 |
| Switch 4 COM | Red | Feet Up | Pin 1 |

### Remote Preservation Wiring

Connect the remote in the female end of the extension cable and wire the cables to ESP32 GPIO pins to maintain remote functionality:

| ESP32 GPIO | Wire Color | Function |
|------------|------------|----------|
| GPIO 2 | Black | Head Down Detection |
| GPIO 4 | Red | Feet Up Detection |
| GPIO 17 | Yellow | Head Up Detection |
| GPIO 18 | Orange/Blue | Feet Down Detection |
| GND | Green | Ground Reference |

## ESPHome Configuration

The ESP32 runs ESPHome firmware with the provided `SmartBed.yaml` configuration. Key features:

### Relay Configuration

```yaml
output:
  - platform: gpio
    pin: GPIO32
    id: relay_pin_1  # Head Down
  - platform: gpio
    pin: GPIO33
    id: relay_pin_2  # Feet Down
  - platform: gpio
    pin: GPIO25
    id: relay_pin_3  # Head Up
  - platform: gpio
    pin: GPIO26
    id: relay_pin_4  # Feet Up
```

### Switch Definitions

```yaml
switch:
  - platform: output
    id: relay_1
    name: "Head down"
    output: relay_pin_1
  - platform: output
    id: relay_2
    name: "Feet down"
    output: relay_pin_2
  - platform: output
    id: relay_3
    name: "Head up"
    output: relay_pin_3
  - platform: output
    id: relay_4
    name: "Feet up"
    output: relay_pin_4
```

### Remote Button Detection

The configuration includes binary sensors for each remote button that trigger the corresponding relays, ensuring the original remote continues to work alongside the smart controls.

## Home Assistant Integration

Once the ESP32 is running ESPHome, it will automatically appear in Home Assistant as four switch entities:

- `switch.esphome_web_861ea0_head_down`
- `switch.esphome_web_861ea0_head_up`
- `switch.esphome_web_861ea0_feet_down`
- `switch.esphome_web_861ea0_feet_up`

These can be used in automations, scripts, and dashboards.

## 3D Printed Case

A custom 3D printable case is provided to protect the ESP32 board and provide professional mounting options.

### Case Features

- **Snap-fit lid**: Tool-free access for maintenance
- **Cable management**: Two cable entry points with integrated strain relief
- **LED visibility**: Windows for all relay and status LEDs
- **Ventilation**: Slots for heat dissipation
- **Internal wire management**: Channels to secure Dupont connections

### Files Included

- `box.stl` - Main enclosure box for 3D printing
- `cover.stl` - Top cover/lid for 3D printing

### Printing Instructions

**Print Settings:**
- Layer height: 0.2mm
- Infill: 20%
- Support: None required
- Print orientation: Bottom face down for both parts

**Materials:**
- PLA: Recommended for indoor use
- PETG: Better temperature resistance
- ABS: Maximum durability

**Post-Processing:**
1. Remove any stringing or supports
2. Test fit the snap mechanism before assembly
3. Ensure cable holes are clear of debris

### Assembly

1. **Install board**: Mount ESP32 board using M3 screws into integrated posts
2. **Route cables**: Thread cables through strain relief channels and out cable holes
3. **Secure connections**: Use internal channels to manage and secure wire connections
4. **Close case**: Snap lid onto base using integrated tabs

### Mounting Options

**Under-bed mounting (recommended):**
- Position for easy cable routing to bed connection
- Ensure adequate clearance for lid removal

**Wall mounting:**
- Use appropriate anchors for wall type
- Consider cable management to connection points

## Installation Steps

1. **Prepare the Hardware**
   - Cut the DIN5 extension cable in half
   - Strip wire ends and identify each wire's function using a multimeter

2. **Program the ESP32**
   - Install ESPHome
   - Update WiFi credentials in `SmartBed.yaml`
   - Flash the firmware using the USB to TTL converter

3. **Make Connections**
   - Connect power wires (green/white) to ESP32 power input and relay commons
   - Connect motor control wires to relay COM terminals
   - Connect remote detection wires to GPIO pins

4. **Test the System**
   - Verify original remote still functions
   - Test each relay function from Home Assistant
   - Check that movements stop when buttons are released

5. **Install and Secure**
   - Mount the ESP32 board in a safe, accessible location
   - Secure all connections
   - Test final installation

## Troubleshooting

### Common Issues

**Remote not working after installation:**
- Check GPIO connections for remote detection
- Verify pullup resistors are enabled in configuration
- Test continuity of extension cable connections

**ESP32 not responding:**
- Check power connections (7-30V input)
- Verify WiFi credentials in configuration
- Check serial connection for programming

**Bed movements don't stop:**
- Verify NC (Normally Closed) relay connections
- Check that switches return to OFF state
- Review relay configuration in ESPHome

### Safety Checks

- Always test with original remote first
- Verify all movements stop when controls are released
- Check that relays are properly isolated
- Monitor for any unusual heating of components

## Safety Considerations

⚠️ **Important Safety Notes:**

- This project involves modifying electrical systems - ensure you understand basic electrical safety
- Always disconnect power when making connections
- Test thoroughly before regular use
- The original remote should always remain functional as a backup
- Consider having the installation reviewed by a qualified technician

## License

This project is provided as-is for educational and personal use. Modify your bed's electrical system at your own risk.

## Contributing

Feel free to submit issues, improvements, or variations of this design. Photos and documentation updates are especially welcome.
