
# ⚡ Charging Controller with Power Failure Recovery

## 📖 Overview

The **Smart E-Bike Charging Controller** is an IoT-enabled embedded system designed to automate and safeguard the charging process of electric bicycles. Built around the **ESP8266 (ESP-01)**, the controller provides intelligent charging control through configurable timers, remote monitoring, and automatic power restoration after unexpected outages.

The system is designed as a **low-cost and reliable charging automation solution** with both online and offline operating capabilities. It controls the AC supply to an e-bike charger using a relay interface while implementing intelligent timer management to prevent unnecessary charging beyond the required duration.

A major design consideration of this project was the **limited GPIO availability of the ESP8266 ESP-01 module**. Compared with development boards such as NodeMCU or ESP32, the ESP-01 provides only a small number of accessible pins, making integration of additional peripherals more challenging. 

To overcome this limitation, two different power recovery approaches were developed:

1. **RTC-Based Recovery Version**  
   Uses an external **DS3231 Real-Time Clock (RTC)** module for accurate time tracking during power interruptions.

2. **Flash Memory-Based Recovery Version**  
   Uses the ESP8266's internal flash memory to periodically store charging progress and restore the timer state after a power failure without requiring additional hardware.

Both approaches ensure that charging resumes correctly after unexpected power interruptions, preventing timer resets and reducing the risk of overcharging.

The system supports both **offline autonomous operation** and **online IoT control** through the **Blynk platform**, allowing users to monitor and control charging remotely.

---

# ✨ Key Features

## 🔌 Charging Control

* Automatic e-bike charger ON/OFF control
* Default **4-hour charging timer**
* Relay-based AC load switching
* Automatic charger shutdown after timer completion
* Configurable charging schedule

## 📱 IoT Features

* ESP8266 Wi-Fi connectivity
* Blynk IoT mobile application integration
* Remote relay control
* Real-time charger status monitoring
* Online and offline operating modes

## ⚡ Power Failure Recovery

* Automatic recovery after unexpected power loss
* Charging timer preservation
* RTC-based recovery option
* Flash memory-based recovery option
* Automatic timer restoration after restart

## 🔧 Hardware Optimization

* Designed specifically for ESP-01 limitations
* Reduced dependency on external hardware
* Software-based recovery implementation
* Low-cost embedded architecture

---

# 🛠 Technologies Used

## Embedded Systems

* ESP8266 (ESP-01)
* ESP32-S3 *(development and testing platform)*
* Embedded C/C++
* Arduino IDE

## IoT & Communication

* Blynk IoT Platform
* Wi-Fi Communication
* NTP (Network Time Protocol)

## Firmware Development

* Event-driven programming
* State machine logic
* Timer management
* Non-volatile flash memory storage
* RTC integration (DS3231)
* Persistent data storage
* Power failure recovery algorithms
* Timer state restoration logic

## Electronics

* 240 VAC to 5 V AC-DC power supply
* 3.3 V voltage regulator
* Relay driver circuit
* Single-channel relay module


---

# ⚙️ System Architecture

```text
               
                ESP8266 ESP-01
                        │
          ┌─────────────┴─────────────┐
          │                           │
          ▼                           ▼
   Blynk Relay Control              Timer Management
          │                           │
          ▼                           ▼
 Charging Control                     Flash Memory / RTC

````

---



# 🔧 Hardware Design

<img width="1051" height="598" alt="image" src="https://github.com/user-attachments/assets/277bdf01-d632-4552-aa46-fbddb040564c" />

# PCB Design 

<img width="1093" height="656" alt="image" src="https://github.com/user-attachments/assets/a4eead6c-517f-4f2c-b2d4-ff97cd25ed80" />

<img width="1338" height="738" alt="image" src="https://github.com/user-attachments/assets/02d70a4d-440f-4184-ab41-4296201adf16" />

# Hardware Implementation 

<img width="360" height="640" alt="axxe6i" src="https://github.com/user-attachments/assets/abf24751-7171-4b17-bbf0-a448142bcde9" />

## ESP8266 ESP-01 Controller

The ESP8266 ESP-01 was selected because of:

* Integrated Wi-Fi capability
* Compact size
* Low cost
* Sufficient processing capability for timer control and IoT communication

### ESP-01 Design Considerations

The limited number of exposed GPIO pins requires careful hardware selection.

Available pins must support:

* Relay control
* Programming interface
* Boot configuration
* Communication functions

Because of these limitations, the flash-memory recovery version was developed to reduce additional hardware requirements.

---

# ⚡ Power Supply Section

The controller uses an isolated low-voltage power supply.

```text
        240 VAC
           │
           ▼
   AC-DC Power Converter
           │
           ▼
          5 V DC
           │
           ▼
    3.3 V Voltage Regulator
           │
           ▼
     ESP8266 ESP-01
```

### Components

* 240 VAC to 5 V AC-DC module
* 3.3 V regulator
* ESP8266 ESP-01
* Relay module
* E-bike charger interface

---

# 🔧 System Operation

## Online Mode

When internet connectivity is available:

* Connects to the Blynk IoT platform
* Allows remote relay control
* Displays charging status
* Supports scheduled charging
* Synchronizes time using NTP

---

## Offline Mode

The controller can operate independently without internet access.

Features:

* Starts default **4-hour charging timer**
* Controls charger automatically
* Does not depend on cloud services
* Restores charging state after power interruption

---

# ⚡ Power Failure Recovery

One of the main objectives of this project was preventing timer reset problems caused by unexpected power failures.

The system implements two independent recovery methods.

---

# Version 1 — RTC-Based Recovery

## DS3231 Real-Time Clock Implementation

The first implementation uses an external DS3231 RTC module.

The RTC continuously tracks charging time even when the ESP8266 restarts.

### Features

* Battery-backed timekeeping
* High timing accuracy
* Independent operation from microcontroller reset
* Reliable elapsed-time calculation

### Advantages

* Accurate time measurement
* No flash write operations
* Independent timing source

### Limitations

* Requires additional hardware
* Requires extra communication pins
* Increased circuit complexity

---

# Version 2 — Flash Memory Recovery

## ESP8266 Internal Flash Storage Implementation

Due to the limited GPIO availability of the ESP-01 module, a software-based recovery method was developed using the ESP8266 internal flash memory.

This approach eliminates the need for an external RTC module while maintaining charging recovery capability.

During operation, the controller periodically stores:

* Remaining charging duration
* Timer status
* Charging session information

After a power failure:

1. ESP8266 restarts
2. Stored data is retrieved from flash memory
3. Previous charging state is restored
4. Remaining charging time continues

---

## Flash Recovery Optimization

To reduce unnecessary flash wear:

* Data is not saved continuously
* Periodic saving intervals are used
* Only required variables are stored
* Stored data is validated before restoration

---

# 🔄 Charging Workflow

```text
Power ON
    │
    ▼
Initialize System
    │
    ▼
Start Default 4-Hour Timer
    │
    ▼
Relay ON
    │
    ▼
Charging
    │
    ├───────────────┐
    │               │
Internet        Power Failure
Available           │
    │               ▼
    │        Restore Timer State
    │               │
    ▼               ▼
Remote Control   Resume Charging
    │
    ▼
Timer Expires
    │
    ▼
Relay OFF
```

---

# 📲 Blynk Integration

The controller communicates with the **Blynk IoT platform** for remote monitoring and control.

## Virtual Pin Mapping

| Virtual Pin | Function                                |
| ----------- | --------------------------------------- |
| **V0**      | Manual Relay Control                    |
| **V1**      | Charging Timer / Remaining Time Display |
| **V2**      | Relay Status Indicator                  |

---

# 📁 Repository Structure

```text
Smart-Ebike-Charging-Controller/

├── Firmware/
│   ├── ESP01_Flash_Recovery/
│   ├── ESP01_RTC_Recovery/
│
├── Circuit_Diagram/
├── PCB/
└── README.md
```

---

# 🧪 Testing Performed

## Functional Testing

✔ Relay switching test
✔ Timer operation test
✔ Offline charging test
✔ Blynk communication test
✔ Power restart recovery test

## Recovery Testing

| Test Condition          | Expected Result             |
| ----------------------- | --------------------------- |
| Normal startup          | Charging timer starts       |
| Power interruption      | Previous state restored     |
| Internet unavailable    | Offline operation continues |
| Restart during charging | Remaining time resumes      |

---

# 💡 Engineering Highlights

* Embedded firmware development using ESP8266
* IoT-based e-bike charging automation
* ESP-01 GPIO limitation analysis and optimization
* Flash memory-based persistent timer storage
* RTC-based backup timekeeping
* Fault-tolerant charging control
* Wi-Fi communication and cloud integration
* Event-driven embedded programming
* Relay-based AC load switching
* Offline autonomous operation
* Low-cost embedded system design

---

# 🚀 Future Improvements

* Battery State-of-Charge (SoC) estimation
* Voltage and current monitoring
* Energy consumption logging
* Overcurrent and short-circuit protection
* OTA firmware updates
* MQTT and Home Assistant integration
* Mobile push notifications
* Charging history and analytics dashboard

---

# 📸 Demonstration

The repository includes:

* ESP8266 firmware
* ESP32 development firmware
* RTC-based recovery implementation
* Flash memory recovery implementation
* Circuit schematics
* PCB design files
* Hardware photographs
* Blynk dashboard screenshots
* System demonstration videos

---

# 👨‍💻 Author

**Navod Lowe**
Department of Electrical & Electronic Engineering
University of Jaffna

---

# 📄 License

This project is released under the **MIT License**.

