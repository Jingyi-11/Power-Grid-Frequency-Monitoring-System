# Grid Frequency Monitor (ESP32)

A low-cost **real-time grid frequency monitoring device** based on ESP32.  
The system measures mains frequency with high precision and uploads timestamped data to a cloud database for monitoring and analysis.

This project integrates **embedded hardware, signal conditioning circuits, real-time firmware, and cloud data logging** to create an open and scalable frequency monitoring platform.

---

## Overview

Maintaining stable grid frequency is critical for reliable power system operation.  
Frequency reflects the real-time balance between power generation and demand, and deviations can indicate system disturbances or instability.

This project develops a compact embedded monitoring device capable of:

- High-precision frequency measurement
- Real-time local display
- Wireless cloud data upload
- Long-term frequency monitoring

Key features:

- Measurement accuracy ≈ **±0.01 Hz**
- Real-time **OLED display**
- **WiFi data upload** to InfluxDB
- Local **data buffering during network outages**
- **FreeRTOS multitasking firmware**

---

## System Architecture

The system consists of two main subsystems:

- Hardware signal acquisition
- Embedded firmware + cloud communication


The device measures grid frequency in real time, displays it locally, and uploads the data to a cloud database for remote monitoring.

---

## Hardware Design

### Main Components

| Component | Function |
|---|---|
| ESP32 DevKitC | Main microcontroller |
| LM339 Comparator | Signal conditioning |
| Step-down transformer | 230V → 12V AC conversion |
| Bridge rectifier | AC waveform processing |
| OLED display | Real-time frequency display |
| Switching power module | 220V → 5V DC power supply |

---

### Signal Acquisition

The grid voltage is converted to a digital signal through multiple stages:

1. Step-down transformer
2. Voltage divider
3. Full-wave rectifier
4. Comparator threshold detection

This produces a **clean 100 Hz square wave**, which is used for frequency measurement.

---

## Frequency Measurement Algorithm

Frequency is calculated using **interrupt-based edge detection**.

### Process

1. Comparator outputs a **100 Hz digital pulse signal**
2. ESP32 GPIO interrupt triggers on **rising edges**
3. After **20 edges (10 cycles)**, the time interval is measured
4. Frequency is calculated using: f = 10 / Δt


Where:

- Δt = time difference between two measurements
- time measured using `micros()`

### Advantages

- High timing resolution
- Low CPU overhead
- Noise-resistant measurement
- Suitable for embedded systems

Averaging over multiple cycles improves measurement stability while maintaining real-time performance.

---

## Firmware Architecture

The firmware runs on **ESP32 with FreeRTOS**.

Main modules:



Key features:

- Interrupt-driven signal capture
- FreeRTOS task scheduling
- NTP time synchronization
- HTTPS cloud communication
- Automatic WiFi reconnection

---

## Data Upload & Cloud Database

Frequency data is uploaded to **InfluxDB Cloud**.

Each record contains:

- frequency value
- timestamp
- device identifier


### Upload Mechanism

- Measurements stored in **local circular buffer**
- Upload task runs every **5 seconds**
- Data sent via **HTTPS POST**

If WiFi disconnects:

- Up to **1500 measurements are cached**
- Data automatically uploads after reconnection

---

## Testing & Validation

### Hardware Testing

The hardware subsystem was validated through:

- transformer waveform analysis
- signal conditioning verification
- comparator output testing
- power supply stability tests

Oscilloscope measurements confirmed that the circuit generates a stable **100 Hz digital pulse** suitable for interrupt-based detection.

---

### System Integration

End-to-end tests verified:

- real-time frequency calculation
- OLED display functionality
- WiFi connectivity
- cloud database upload

---

### Long-Term Reliability Test

A **24-hour continuous operation test** was conducted.

Results:

- ~432,000 frequency samples collected
- WiFi reconnection within **3–7 seconds**
- data loss ≈ **0.5%**
- no system crashes or resets

The system maintained stable operation and accurate measurements during continuous monitoring.

---

## Example Output

Example OLED display:


---

## Future Work

Potential improvements include:

- digital filtering algorithms (moving average / Kalman filter)
- advanced visualization dashboards (Grafana)
- improved power filtering for EMI suppression
- distributed multi-node monitoring networks

These improvements could transform the system into a **scalable distributed grid monitoring platform**.
