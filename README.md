# STM32 Smart Environmental Monitoring System

![Platform](https://img.shields.io/badge/Platform-STM32F103C8T6-blue)
![Language](https://img.shields.io/badge/Language-Embedded%20C-green)
![Protocol](https://img.shields.io/badge/Protocol-I2C%20%7C%20SPI-orange)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## Overview

This project implements a real-time environmental and motion monitoring system deployed on an STM32F103C8T6 ("Blue Pill") microcontroller. It reads ambient temperature from an LM75B digital sensor and tri-axis acceleration data from an MMA7660 accelerometer — both over I2C — and renders live readings on a C12832 SPI-driven LCD display.

Designed with industrial deployment in mind, the system applies a moving-average filter to smooth noisy sensor readings and triggers configurable threshold alerts when environmental conditions exceed safe operating limits. This is directly applicable to equipment health monitoring, cold-chain logistics, and vibration-sensitive manufacturing processes.

---

## Features

- Real-time temperature acquisition via LM75B (±2°C accuracy, 0.5°C resolution)
- Tri-axis accelerometer data via MMA7660 (6-bit resolution, ±1.5g range)
- Moving-average filter (N=8 samples) for noise reduction on both sensor channels
- Configurable threshold alerts — visual indicator on LCD when limits exceeded
- SPI-driven C12832 128×32 LCD display with live data rendering
- Low power I2C communication at 400 kHz (Fast Mode)
- Modular firmware architecture with dedicated driver layers

---

## Tech Stack

| Layer | Technology |
|---|---|
| Microcontroller | STM32F103C8T6 (ARM Cortex-M3, 72 MHz) |
| Temperature Sensor | NXP LM75B (I2C, 9-bit resolution) |
| Accelerometer | Freescale MMA7660FC (I2C, 64 samples/sec) |
| Display | C12832 128×32 LCD (SPI, 8 MHz) |
| IDE | STM32CubeIDE / Keil uVision |
| HAL | STM32 HAL (Hardware Abstraction Layer) |
| Language | Embedded C (C99) |
| Programmer | ST-LINK V2 |

---

## Repository Structure

```
project1-stm32-environmental-monitor/
├── README.md
├── code/
│   ├── Core/
│   │   ├── main.c
│   │   ├── lm75b_driver.c / .h
│   │   ├── mma7660_driver.c / .h
│   │   ├── c12832_driver.c / .h
│   │   └── filter.c / .h
│   └── startup/
│       └── startup_stm32f103xb.s
├── diagrams/
│   ├── block_diagram.png
│   ├── circuit_schematic.png
│   └── flowchart.png
├── docs/
│   ├── system_architecture.md
│   ├── component_datasheets/
│   └── testing_report.md
└── images/
    ├── hardware_setup.jpg
    └── lcd_output.jpg
```

---

## System Architecture

```
+------------------+       I2C (400kHz)      +------------------+
|   LM75B Temp     |<----------------------->|                  |
|   Sensor         |  Addr: 0x48             |                  |
+------------------+                         |  STM32F103C8T6   |
                                             |                  |
+------------------+       I2C (400kHz)      |  - I2C1 (PB6/7) |
|   MMA7660        |<----------------------->|  - SPI1 (PA5-7) |
|   Accelerometer  |  Addr: 0x4C             |  - GPIO Alerts  |
+------------------+                         |                  |
                                             +--------+---------+
+------------------+       SPI (8MHz)                |
|   C12832 LCD     |<--------------------------------+
|   128x32 Display |  CS, RST, A0
+------------------+
```

---

## Hardware Connections

### I2C Bus (PB6 = SCL, PB7 = SDA)

| Signal | STM32 Pin | LM75B Pin | MMA7660 Pin |
|--------|-----------|-----------|-------------|
| SCL | PB6 | SCL | SCL |
| SDA | PB7 | SDA | SDA |
| VCC | 3.3V | VS | VDD |
| GND | GND | GND | GND |

> Pull-up resistors: 4.7kΩ on both SDA and SCL lines to 3.3V

### SPI Bus (C12832 LCD)

| Signal | STM32 Pin | C12832 Pin |
|--------|-----------|------------|
| MOSI | PA7 | SI |
| SCK | PA5 | SCK |
| CS | PA4 | CS |
| RST | PB0 | RST |
| A0 | PB1 | A0 |

---

## Setup & Build Instructions

### Prerequisites

- STM32CubeIDE v1.13+ installed
- ST-LINK V2 programmer
- STM32F103C8T6 board ("Blue Pill")
- LM75B, MMA7660, C12832 modules wired per schematic

### Build Steps

```bash
# 1. Clone the repository
git clone https://github.com/yourusername/stm32-env-monitor.git
cd stm32-env-monitor

# 2. Open STM32CubeIDE → File → Import → Existing Project
#    Navigate to /code/ directory

# 3. Build: Project → Build All (Ctrl+B)

# 4. Flash: Run → Debug (Ctrl+F11) with ST-LINK connected
```

### Configuration (main.h)

```c
#define TEMP_HIGH_THRESHOLD     40.0f   // °C — alert above this
#define TEMP_LOW_THRESHOLD      5.0f    // °C — alert below this
#define ACCEL_SHOCK_THRESHOLD   1.2f    // g  — alert above this
#define FILTER_WINDOW_SIZE      8       // Moving average window
#define DISPLAY_REFRESH_MS      500     // Display update interval
```

---
