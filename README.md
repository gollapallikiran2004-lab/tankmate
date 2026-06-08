# TankMate

Solar-powered smart water tank monitoring and pump automation system.
Built with ESP32, LoRa, BLE, ESP-NOW, and Firebase.
Developed by Sana Tech Inc.

---

## What It Does

TankMate is a complete IoT product. It monitors water tank level in real time, automatically controls a pump, detects leaks, protects the pump from overrun, and streams all data to a Flutter mobile app via Bluetooth and Firebase cloud.

No manual monitoring. No wasted water. No pump burnouts.

---

## Hardware

### Tank Unit
| Component | Purpose |
|---|---|
| ESP32 Dev Module | Main controller |
| HC-SR04 Ultrasonic Sensor | Water level |
| ai-ra02 LoRa (SX1278, 433MHz) | Long-range communication |
| LiPo Battery (3.0-4.2V) | Primary power |
| Solar Panel | Battery charging |

### Motor Unit
| Component | Purpose |
|---|---|
| ESP32 Dev Module | Motor controller |
| Relay Module | Pump ON/OFF |

### Tank Unit Pin Map
| Function | GPIO |
|---|---|
| Ultrasonic TRIG | 32 |
| Ultrasonic ECHO | 33 |
| Battery ADC | 34 |
| Solar ADC | 35 |
| LED | 2 |
| LoRa SCK/MISO/MOSI/SS | 18/19/23/5 |
| LoRa RST/DIO0 | 14/26 |

---

## System Architecture

Tank Unit communicates via three channels:
- BLE to mobile app - live data, config, WiFi provisioning
- ESP-NOW to Motor Unit - instant pump commands under 5ms
- LoRa 433MHz SF12 to Motor Unit - long-range backup
- Firebase HTTPS - cloud sync and remote command polling every 2s

Motor Unit controls the relay/pump with 45-min watchdog and 90s silence safety cutoff.

---

## Key Features

Sensing:
- 5-ping average plus 10-sample moving median filter on ultrasonic
- 3 tank geometries: rectangular, tapered frustum, horizontal cylinder
- Battery and solar voltage monitoring

Communication - Triple Redundancy:
- BLE NimBLE: live data, pump commands, WiFi provisioning
- ESP-NOW: instant pump commands under 5ms
- LoRa 433MHz SF12: long-range backup, 3500ms reply window
- Firebase HTTPS: cloud sync and remote commands every 2s

Safety:
- Pump runtime limiter: auto shutoff after 45 minutes
- Tank silence watchdog: stops pump if no signal for 90s
- Leak detection: rate-of-change algorithm
- TANK_FULL, TANK_CRITICAL, LOW_BATTERY, SENSOR_ERROR, PUMP_OVERTIME, POSSIBLE_LEAK alerts

Security:
- Device ID from ESP32 MAC - no hardcoding
- WiFi credentials XOR-encrypted with MAC in NVS
- Signed LoRa packets via FNV-1a HMAC SecureIoT library
- Paired device ID validation on ESP-NOW and LoRa

Power:
- Adaptive deep sleep: 60s default, up to 5 minutes when stable
- Solar charging with LiPo battery backup

---

## Mobile App (Flutter)

Screens: Dashboard, My Tank, Modes, Settings

Connection modes:
1. BLE direct - live data, under 300ms command latency
2. Firebase cloud - remote monitoring without BLE

Dependencies: firebase_core, firebase_database, flutter_blue_plus, flutter_local_notifications, shared_preferences, permission_handler

---

## Firmware Build Settings

Board: ESP32 Dev Module
Partition (Tank Unit): Huge APP 3MB No OTA 1MB SPIFFS
Partition (Motor Unit): Default
Arduino Core: 2.0.17
Baud Rate: 115200
LoRa: 433MHz SF12 17dBm

Libraries: NimBLE-Arduino, LoRa by Sandeep Mistry

---

## Getting Started

1. Copy secrets.h.example to secrets.h and add Firebase credentials
2. Flash MotorUnit.ino first and note its MAC address
3. Update MOTOR_MAC[] in TankUnit.ino
4. Flash TankUnit.ino with Huge APP partition
5. Monitor at 115200 baud

App:
cd app
flutter pub get
flutter run

Use Settings tab to provision WiFi over BLE.

---

## Project Status

- Water level monitoring 3 tank shapes: done
- Battery and solar monitoring: done
- BLE mobile app: done
- ESP-NOW instant pump control: done
- LoRa long-range backup 433MHz SF12: done
- Firebase cloud sync and remote commands: done
- Leak detection rate-of-change: done
- Pump overtime protection 45min watchdog: done
- Tank silence safety cutoff 90s: done
- Adaptive deep sleep: done
- OTA updates: planned
- AI water usage prediction: planned

---

## About

Sana Tech Inc. - US-based IoT startup building smart home and industrial automation products.

TankMate is our flagship product: fully autonomous, solar-powered water management for households and small businesses.

---

## License

Proprietary - 2025 Sana Tech Inc. All rights reserved.
