# WiFi Controlled RC Car using STM32 & ESP8266

A fully custom 4WD WiFi-controlled robotic car built using the STM32F103 Blue Pill microcontroller, NodeMCU ESP8266 WiFi module, and dual L298N motor drivers.

The system enables real-time wireless control of four DC motors through a smartphone web interface over WiFi. The project focuses on embedded systems design, UART communication, PWM motor control, real-time firmware development, and hardware-software integration.

---

# Project Overview

This project implements a low-latency WiFi-controlled RC car architecture where:

- A smartphone sends movement commands through WiFi.
- NodeMCU acts as a WiFi Access Point and web server.
- Commands are transmitted to STM32 through UART communication.
- STM32 processes the commands and generates PWM + GPIO outputs.
- Two L298N motor drivers control four DC geared motors.

The project was developed using an incremental hardware bring-up methodology, where each subsystem was individually validated before full system integration.

---

# System Architecture

```text
 +-------------------+
 | Smartphone (UI)   |
 +----------+--------+
            |
            | Wi-Fi (HTTP GET /c?v=<cmd>)
            |
 +----------v----------+
 | NodeMCU (Soft AP)   |
 | IP: 192.168.4.1     |
 +----------+----------+
            |
            | UART @ 9600 baud
            |
 +----------v----------+
 | STM32F103 Blue Pill |
 | Bare-metal Firmware |
 +-----+----------+----+
       |          |
       | GPIO + PWM
       |
 +----v---+   +---v----+
 | L298N 1|   | L298N 2|
 | Left   |   | Right  |
 +--+--+--+   +--+--+--+
    |  |         |  |
   [FL][BL]    [FR][BR]
```

---

# Features

- WiFi-based remote control
- 4-wheel drive architecture
- Real-time UART communication
- PWM-based speed control
- Differential steering
- Pivot turn support
- Web-based control interface
- Battery-powered portable system
- Low-latency command handling
- Independent left/right motor control

---

# Hardware Components

| Component | Description |
|---|---|
| STM32F103C8T6 Blue Pill | Main microcontroller |
| NodeMCU ESP8266 | WiFi communication module |
| L298N Motor Driver x2 | DC motor control |
| 12.6V Li-ion Battery | Power source |
| LM2596 Buck Converter | 5V regulated supply |
| DC Geared Motors x4 | Vehicle movement |
| Chassis + Wheels | Mechanical platform |

---

# Circuit Description

## Power Distribution

The system is powered using a 12.6V Li-ion battery pack.

### Power Flow
- Battery directly powers both L298N motor drivers.
- LM2596 buck converter steps down 12.6V to regulated 5V.
- 5V powers:
  - STM32 Blue Pill
  - NodeMCU ESP8266

A common ground is shared between:
- STM32
- NodeMCU
- Motor drivers
- Battery supply

This common reference is necessary for reliable UART communication and stable PWM control.

---

# Communication Architecture

## WiFi Layer

The NodeMCU ESP8266 operates in Soft Access Point (Soft AP) mode.

### Example Configuration
- SSID: `RCMotor`
- IP Address: `192.168.4.1`

A smartphone connects directly to the NodeMCU WiFi network.

The control interface is hosted as a lightweight web page served directly by the ESP8266.

---

# UART Communication

The ESP8266 communicates with STM32 using UART serial communication.

## UART Configuration

| Parameter | Value |
|---|---|
| Baud Rate | 9600 |
| Data Bits | 8 |
| Stop Bits | 1 |
| Parity | None |

### Data Flow
```text
Smartphone → ESP8266 → UART → STM32
```

Commands are transmitted as simple character instructions.

Example:

| Command | Action |
|---|---|
| F | Forward |
| B | Backward |
| L | Left |
| R | Right |
| S | Stop |

---

# Motor Control Logic

The STM32 handles all real-time motor control operations.

## GPIO Control

GPIO pins determine:
- Motor direction
- Left/right steering
- Forward/reverse motion

---

# PWM Speed Control

PWM signals are generated using STM32 timers.

## PWM Functions
- Motor speed regulation
- Smooth acceleration
- Controlled turning
- Differential drive steering

### Timer Usage
- TIM2
- TIM3
- TIM4

PWM duty cycle determines motor speed.

---

# Differential Steering

The RC car uses differential steering logic.

## Forward Movement
Both left and right motors move forward.

## Left Turn
Left-side motors slow down or stop while right-side motors continue.

## Right Turn
Right-side motors slow down or stop while left-side motors continue.

## Pivot Turn
One side rotates forward while the opposite side rotates backward.

This enables:
- Sharp turning
- Better maneuverability
- Zero-radius turning capability

---

# Software Development Flow

The project was developed in multiple stages.

## 1. Component Selection
Selection of:
- STM32
- ESP8266
- Motor drivers
- Power system
- Motors

---

## 2. Hardware Assembly
Mechanical chassis assembly and wiring layout.

---

## 3. STM32 Environment Setup
- STM32CubeIDE installation
- ST-Link setup
- GPIO testing
- Initial firmware validation

---

## 4. Single Motor Testing
Validation of:
- Direction control
- GPIO switching
- Motor driver behavior

---

## 5. PWM Implementation
PWM generation was tested independently before full integration.

---

## 6. 4-Motor Integration
Second motor driver added for full 4WD control.

---

## 7. NodeMCU Bring-Up
ESP8266 configured as:
- Soft AP
- Embedded web server

---

## 8. UART Integration
Reliable serial communication established between:
- NodeMCU
- STM32

---

## 9. Web UI Development
Browser-based control interface created.

### UI Evolution
- Tap-and-play controls
- Hold-to-move controls
- Latency optimization
- Emergency stop improvements

---

## 10. Final Testing
Complete battery-powered field testing performed.

---

# Problems Encountered & Solutions

## Problem 1: STM32 Boot/Programming Issues

### Cause
ST-Link communication instability.

### Solution
Reduced SWD frequency to 480 kHz.

---

## Problem 2: PWM Pin Failure

### Cause
PA6 PWM output stopped functioning.

### Solution
PWM remapped to alternate timer channel.

---

## Problem 3: Incorrect Motor Rotation

### Cause
Motor mounting orientation mismatch.

### Solution
Direction logic inverted in firmware.

---

## Problem 4: Random Commands on ESP8266 Reboot

### Cause
UART garbage data during startup.

### Solution
Added:
- UART validation
- Error clearing
- Command filtering

---

## Problem 5: High Stop Latency

### Cause
WiFi communication delay.

### Solution
Implemented:
- Heartbeat timeout
- STOP burst on release
- Reduced URL size

---

# Safety Features

- Common ground protection
- Regulated 5V logic supply
- Software emergency stop
- Controlled PWM startup
- Power isolation through buck converter

---

# Future Improvements

- Mobile app integration
- Camera streaming
- Obstacle avoidance
- PID speed control
- Encoder feedback
- Battery monitoring
- FreeRTOS task scheduling
- Bluetooth support
- Autonomous navigation mode

---

# Applications

- Embedded systems learning
- Robotics projects
- IoT experimentation
- Wireless control systems
- Educational demonstrations
- STM32 firmware practice

---

# Technologies Used

## Embedded Systems
- STM32F103C8T6
- Bare-metal Embedded C
- STM32CubeIDE

## Communication
- UART
- WiFi
- HTTP GET requests

## Power Electronics
- PWM control
- Motor driver interfacing
- Buck conversion

## Robotics
- Differential drive
- Motor control
- Real-time actuation

---

# Project Outcome

The final system achieved:

- Stable WiFi control
- Reliable UART communication
- Smooth 4WD movement
- Low-latency response
- Real-time directional control
- Battery-powered portability

The project successfully demonstrates practical embedded systems integration involving firmware, communication protocols, motor control, and hardware debugging.

---

# Author

Shubham Baniwal
B.Tech Electronics & Communication Engineering  
Delhi Technological University (DTU)

```
