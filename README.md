# 🏭 INDUSTRIAL-WATER-QUALITY-MONITORING

> A fully embedded, cloud-connected industrial water quality monitoring system using STM32F103C6T6, ESP32, DHT11 Temperature Sensor, TDS Sensor Module, HC-SR04 Ultrasonic Sensor, and 1.8" TFT Display — with edge processing, UART industrial communication, and real-time IoT visualization via ThingSpeak.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [System Architecture](#system-architecture)
- [Hardware Components](#hardware-components)
- [Software Tools](#software-tools)
- [Circuit & Pin Connections](#circuit--pin-connections)
- [How It Works](#how-it-works)
- [Communication Protocols](#communication-protocols)
- [Results](#results)
- [Future Scope](#future-scope)

---

## Overview

Industrial water quality monitoring is a critical requirement in sectors such as chemical processing, textiles, food production, and wastewater treatment. Traditional approaches — manual sampling and laboratory testing — are time-consuming, labor-intensive, and incapable of real-time response. Early IoT prototypes lack industrial-grade reliability, robust communication protocols, and proper scalability.

This project presents a **fully embedded, cloud-edge integrated platform for real-time industrial water quality monitoring**. The system uses an **STM32F103C6T6 microcontroller** as the sensor-side slave node, acquiring data from a **DHT11 temperature sensor**, **TDS sensor module**, and **HC-SR04 ultrasonic sensor** for water level. Edge processing — including filtering and calibration — is performed locally using the **STM32 HAL library**. Processed data is transmitted to an **ESP32 master controller** via **UART communication**, which drives a **1.8-inch TFT display** for on-site visualization, triggers **LED alerts** on threshold breaches, and uploads all parameters to the **ThingSpeak IoT cloud platform** over Wi-Fi — all without any supervisory PC or SCADA infrastructure.

---

## Features

- ✅ Distributed master-slave architecture — STM32 slave for edge sensing, ESP32 master for communication 
- ✅ Real-time measurement of Temperature, Total Dissolved Solids (TDS/EC), and Water Level
- ✅ Edge processing on STM32 — signal filtering, calibration, and validation before transmission
- ✅ STM32 HAL library-based firmware using STM32CubeMX and STM32CubeIDE
- ✅ UART industrial communication between STM32 slave and ESP32 master
- ✅ SPI-driven 1.8" TFT display (128×160, ST7735) for real-time on-site parameter visualization
- ✅ LED local alert indicators triggered when parameters exceed predefined safety thresholds
- ✅ Wi-Fi IoT connectivity via ESP32 to ThingSpeak cloud platform
- ✅ ThingSpeak dashboard with live graphs for Water Level, Temperature, and TDS trends
- ✅ Modular and scalable architecture — additional STM32 slave nodes can be added via UART
- ✅ Low-cost, compact, and suitable for student research and industrial prototyping

---

## System Architecture

The system is divided into three functional layers:

```
┌─────────────────────────────────────────────────────────────────────┐
│  SENSING LAYER       DHT11 (Temperature & Humidity)                 │
│                      TDS Sensor Module (Conductivity → ppm)         │
│                      HC-SR04 Ultrasonic Sensor (Water Level)        │
├─────────────────────────────────────────────────────────────────────┤
│  EDGE PROCESSING     STM32F103C6T6 (ARM Cortex-M3 @ 72 MHz)         │
│  LAYER               ADC Sampling, Calibration, Filtering           │
│                      HAL Library Firmware (CubeMX + CubeIDE)        │
├─────────────────────────────────────────────────────────────────────┤
│  CONTROL & IOT       ESP32 (Dual-Core Xtensa LX6 @ 240 MHz)         │
│  LAYER               UART Reception, Threshold Monitoring           │
│                      TFT Display (SPI), LED Alerts, ThingSpeak Wi-Fi│
└─────────────────────────────────────────────────────────────────────┘
```

### Block Diagram

```
DHT11 Sensor ──┐
TDS Sensor  ──►  STM32F103C6T6 (Slave Node)  ──► UART ──► ESP32 (Master)──► TFT Display (SPI)
HC-SR04     ──┘   │  ADC + Timer + HAL Lib                    │              LED Alert Indicators
                  │  Edge Filtering & Calibration              │
                  │                                            ▼
                  └── ST-Link V2 (Flash Programming)     Wi-Fi (ESP32)
                                                              │
                                                              ▼
                                                    ThingSpeak Cloud Platform
                                                    └── Water Level Chart
                                                    └── Temperature Chart
                                                    └── TDS Level Chart
```

---

## Hardware Components

| Component | Model | Interface | Purpose |
|---|---|---|---|
| Slave Microcontroller | STM32F103C6T6 (ARM Cortex-M3) | — | Sensor acquisition, edge processing, UART transmission |
| Master Microcontroller | ESP32 (Dual-Core Xtensa LX6) | — | UART reception, display, alerts, IoT connectivity |
| Temperature Sensor | DHT11 | Single-Wire GPIO (PA0) | Ambient temperature and humidity measurement |
| TDS Sensor Module | Analog TDS/EC Probe | ADC (PA1) | Total Dissolved Solids concentration in water |
| Ultrasonic Sensor | HC-SR04 | GPIO Trigger/Echo (PB0/PB1) | Water level measurement via time-of-flight |
| TFT Display | 1.8" ST7735 (128×160) | SPI | Real-time on-site visualization of all parameters |
| LED Indicators | Standard LEDs × 2 | GPIO (ESP32) | Local alert for threshold breaches |
| Programmer/Debugger | ST-Link V2 | SWD (PA13/PA14) | STM32 firmware flashing and debugging |
| Power Supply | USB / DC Supply | — | System power for both microcontrollers |

---

## Software Tools

| Tool | Purpose |
|---|---|
| **STM32CubeMX** | Graphical peripheral configuration and HAL initialization code generation |
| **STM32CubeIDE** | Firmware development, compilation, and debugging for STM32 |
| **STM32CubeProgrammer** | Firmware flashing to STM32 flash memory via ST-Link V2 |
| **Arduino IDE** | ESP32 firmware development — UART reception, TFT control, Wi-Fi, ThingSpeak |
| **ThingSpeak IoT Platform** | Cloud dashboard for real-time data visualization and trend analysis |

---

## Circuit & Pin Connections

### STM32F103C6T6 — ADC Sensor Inputs

| STM32 Pin | ADC Channel | Connected Device | Description |
|---|---|---|---|
| PA0 | ADC Channel 0 | DHT11 Sensor | Reads analog signal from DHT11 for temperature measurement |
| PA1 | ADC Channel 1 | TDS Sensor | Reads analog voltage output corresponding to TDS concentration |

### STM32F103C6T6 — Ultrasonic Sensor (HC-SR04)

| STM32 Pin | GPIO Function | HC-SR04 Pin | Description |
|---|---|---|---|
| PB0 | GPIO Output | TRIG | Sends 10 µs trigger pulse to initiate ultrasonic burst |
| PB1 | GPIO Input | ECHO | Measures echo pulse duration to calculate distance to water surface |

### STM32F103C6T6 — UART Communication to ESP32

| STM32 Pin | Function | ESP32 Pin | Description |
|---|---|---|---|
| PA9 (TX) | USART1 Transmit | RX (GPIO16) | Sends processed sensor data string to ESP32 master |
| PA10 (RX) | USART1 Receive | TX (GPIO17) | Receives commands from ESP32 (if required) |
| GND | Common Ground | GND | Shared ground reference |

### STM32F103C6T6 — ST-Link V2 Debugger (SWD Programming)

| STM32 Pin | ST-Link Pin | Function |
|---|---|---|
| VCC | 3.3V | Power reference for programming interface |
| GND | GND | Common ground |
| PA13 | SWDIO | Serial Wire Data — bidirectional data line |
| PA14 | SWCLK | Serial Wire Clock — programming clock |
| RESET | NRST | Microcontroller reset during programming |

### ESP32 — TFT Display (1.8" ST7735 via SPI)

| TFT Pin | ESP32 Pin | Description |
|---|---|---|
| VCC | 3.3V | Display power supply |
| GND | GND | Common ground |
| SCK (CLK) | GPIO18 | SPI clock signal |
| SDA (MOSI) | GPIO23 | SPI data from ESP32 to display |
| RES | GPIO4 | Display reset during initialization |
| DC (A0) | GPIO2 | Data / Command selection pin |
| CS | GPIO5 | Chip select — enables SPI communication |

### ESP32 — LED Alert Indicators

| LED | ESP32 Pin | Trigger Condition |
|---|---|---|
| LED 1 (Green) | GPIO25 | Normal operating range — all parameters within safe limits |
| LED 2 (Red) | GPIO26 | Alert state — one or more parameters exceeded threshold |

---

## How It Works

1. **Boot & Init (STM32)** — STM32 initializes ADC, GPIO, UART (USART1), and TIM1 peripherals using HAL library functions generated by STM32CubeMX. ST-Link V2 flashes the compiled firmware via SWD interface.
2. **DHT11 Reading** — STM32 sends a start pulse on PA0, reads back 40-bit response (humidity + temperature + checksum), validates checksum, and extracts temperature in °C.
3. **TDS Measurement** — STM32 ADC reads analog voltage from the TDS probe on PA1, converts raw ADC value to voltage (3.3V / 4095), applies the calibration polynomial formula to compute TDS in ppm.
4. **Water Level Measurement** — STM32 generates a 10 µs trigger pulse on PB0, then measures the echo pulse duration on PB1 using TIM1. Distance is calculated as `Distance = (Time × 0.034) / 2` cm, determining the water surface level from the sensor.
5. **Edge Processing** — STM32 performs local signal filtering and calibration on all three sensor readings to eliminate noise and ensure accurate values before transmission.
6. **UART Transmission** — STM32 formats processed values into a structured string (`Temp: XX C | Dist: XX cm | TDS: XX.XX ppm\r\n`) and transmits to ESP32 via USART1 at 115200 baud using `HAL_UART_Transmit()`.
7. **ESP32 Reception & Parsing** — ESP32 receives the UART data string, parses temperature, distance, and TDS values for downstream processing.
8. **Threshold Monitoring** — ESP32 compares received sensor values against predefined safety thresholds. If any parameter is out of range, the red LED is activated as an immediate local alert.
9. **TFT Display Update** — ESP32 sends graphical commands and pixel data to the 1.8" TFT via SPI, updating real-time readings of Temperature, TDS, Water Level, and system alert status on screen.
10. **ThingSpeak Cloud Upload** — ESP32 connects to Wi-Fi and transmits sensor data to the ThingSpeak server. Three channel fields are updated — Field 1 (Water Level), Field 2 (Temperature), Field 3 (TDS Level) — enabling remote dashboard visualization and historical trend analysis.

### HAL Library Function Summary

```
STM32 Peripheral   HAL Function Used
─────────────────────────────────────────────────────────
ADC (TDS)          HAL_ADC_Start() → HAL_ADC_PollForConversion() → HAL_ADC_GetValue()
GPIO (DHT11)       HAL_GPIO_WritePin() / HAL_GPIO_ReadPin() — Set_Pin_Output/Input()
GPIO (Ultrasonic)  HAL_GPIO_WritePin(TRIG) → HAL_TIM_GET_COUNTER(ECHO timing)
UART (ESP32)       HAL_UART_Transmit(&huart1, msg, strlen(msg), HAL_MAX_DELAY)
Timer (Timing)     HAL_TIM_Base_Start() → HAL_TIM_SET/GET_COUNTER() for µs delays
System Init        HAL_Init() → SystemClock_Config() → MX_*_Init() functions
```

### ThingSpeak Operation Steps

| Step | Operation | Description |
|---|---|---|
| Step 1 | Device Authentication | Unique API key embedded in ESP32 firmware for secure channel access |
| Step 2 | Wi-Fi Connection | ESP32 connects to local network via WiFi library |
| Step 3 | Data Transmission | `ThingSpeak.writeFields(channelID, apiKey)` uploads all three fields |
| Step 4 | Data Visualization | Dashboard displays Water Level chart, Temperature gauge, TDS trend graph |

---

## Communication Protocols

### UART (STM32 → ESP32 Industrial Data Link)
- Asynchronous serial communication at **115200 bps**
- STM32 USART1 TX (PA9) → ESP32 RX; common GND shared
- Structured ASCII string format transmitted every **2000 ms**
- Suitable for industrial environments — robust against electrical noise

**Real-time UART Output Format:**
```
Temp: 28 C | Dist: 22 cm | TDS: 289.45 ppm
```

### SPI (ESP32 → TFT Display)
- Synchronous serial protocol at high speed for pixel data transfer
- ESP32 as SPI Master; ST7735 display controller as SPI Slave
- Dedicated lines: MOSI (GPIO23), SCK (GPIO18), CS (GPIO5), DC (GPIO2), RST (GPIO4)
- Display refreshes with new sensor values every UART reception cycle

**Key SPI Display Content:**
```
[TFT Screen]
Temp   : 28 °C
TDS    : 289 ppm
Level  : 22 cm
Status : NORMAL
```

### SWD (ST-Link V2 → STM32 Flash Programming)
- 2-wire Serial Wire Debug interface: SWDIO (PA13) + SWCLK (PA14)
- Used by STM32CubeProgrammer to upload compiled `.hex` / `.bin` firmware
- Supports in-circuit debugging via STM32CubeIDE breakpoints and variable watch

### Wi-Fi (ESP32 → ThingSpeak Cloud)
- ESP32 built-in 802.11 b/g/n Wi-Fi module
- HTTPS REST API calls to ThingSpeak server using ThingSpeak Arduino library
- Data upload interval: every **15 seconds** (ThingSpeak free-tier minimum)
- Three channel fields updated per upload cycle: Water Level, Temperature, TDS

---

## Results

The system was successfully implemented and validated on a bench rig with an active water tank. Key outcomes:

- **UART Communication** — Stable structured data transmission from STM32 to ESP32 at 115200 bps every 2 seconds; no packet loss observed during bench testing.
- **Temperature Measurement** — DHT11 consistently reported ambient temperature with reliable checksum validation; readings stable at 28°C across multiple trials.
- **TDS Measurement** — TDS sensor accurately reflected changes in dissolved solids concentration; calibration polynomial produced values in the 287–291 ppm range for tap water, consistent with expected readings.
- **Water Level (Ultrasonic)** — HC-SR04 measured distance to water surface accurately in the 18–26 cm range; time-of-flight formula produced stable, noise-free level readings.
- **TFT Display** — Real-time parameter display on 1.8" ST7735 screen refreshed correctly with each UART reception; color graphics and alert status rendered without flicker.
- **LED Alert System** — Red LED activated reliably when TDS or temperature exceeded predefined thresholds; green LED held during normal operation.
- **ThingSpeak Cloud Dashboard** — All three fields (Water Level, Temperature, TDS) visualized as live charts on ThingSpeak; 221 data entries logged within the first session; remote access confirmed via browser and mobile.
- **Edge Processing** — STM32-side filtering and calibration reduced noise in TDS ADC readings and ensured clean data before UART transmission, offloading processing burden from ESP32.

---

## Future Scope

- **Additional Sensors** — Integrate pH probe, turbidity sensor, dissolved oxygen (DO) analyzer, and conductivity meter for comprehensive multi-parameter water quality profiling.
- **Machine Learning Analytics** — Deploy ML models on cloud or edge to predict water contamination events or equipment failures from historical ThingSpeak trend data.
- **Automated Control Actuation** — Connect the monitoring system to pumps, solenoid valves, or chemical dosing units for automatic corrective action when parameters go out of range.
- **Multi-Node Scalable Architecture** — Deploy multiple STM32 slave nodes across tanks or pipeline segments, all feeding a single ESP32 master via UART bus for large-scale facility monitoring.
- **Secure IoT Communication** — Implement MQTT over TLS, token-based authentication, and encrypted payload transmission to protect industrial water data from cyber threats.
- **Energy-Efficient Deployment** — Incorporate deep sleep modes on ESP32 and STM32, with battery + solar power supply for remote or off-grid industrial site deployment.
- **Mobile Application** — Develop a dedicated Android / iOS app for real-time monitoring, threshold configuration, historical trend viewing, and push alert notifications.
- **MODBUS / RS485 Integration** — Replace UART with MODBUS RTU over RS485 for true industrial-grade communication compliant with standard SCADA/PLC interoperability.
- **Extended Kalman Filter** — Fuse DHT11 temperature compensation into TDS calibration for improved measurement accuracy across varying water temperatures.
- **Digital Twin** — Build a cloud-side simulation model of the industrial water process, synchronized with live sensor data for predictive maintenance and process optimization.

---

> *Built to demonstrate that effective, scalable industrial water quality monitoring is achievable on compact, low-cost embedded hardware — no SCADA, no PLC, no supervisory PC required.*
