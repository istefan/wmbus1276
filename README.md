# W-MBus Sniffer for LILYGO T3S3 (SX1276)

This project is a dedicated, stable ESPHome configuration for the **LILYGO® T3S3** board equipped with the **SX1276** LoRa chip, tuned for **868 MHz (Europe)**.

It serves as a high-performance **W-MBus Sniffer** that receives radio frames from utility meters (water, heat, electricity) and forwards the **RAW** data via MQTT to a central server for decoding.

## ⚠️ Important Note regarding Forks
This repository is a stable fork of the excellent [SzczepanLeon/esphome-components](https://github.com/SzczepanLeon/esphome-components).
We created this fork to preserve a specific version of the library that offers **superior scanning speed for the SX1276 chip**. Newer upstream versions introduced heavy optimizations for SX1262 which caused significant slowdowns (latency) on the older SX1276 chips. 

By using this repository, you ensure your T3S3 scans hundreds of meters in minutes, rather than one per minute.

## Hardware Requirements

*   **Board:** [LILYGO® T3S3 V1.0 ESP32-S3 LoRa Module](https://lilygo.cc/products/t3s3-v1-0?_pos=1&_sid=e9e14be31&_ss=r)
*   **Chip:** **SX1276** (Important! This code is specific to the 1276 version).
*   **Frequency:** 868 MHz (Standard for W-MBus in Europe).

## How it Works

Unlike "Reader" projects that decode data on the ESP32 itself, this is a **Sniffer**:
1.  **Listen:** The T3S3 listens for W-MBus radio frames on 868 MHz.
2.  **Capture:** It captures the raw hexadecimal frame.
3.  **Forward:** It immediately publishes the raw frame to an MQTT broker.
4.  **Decode (Server-side):** Your Home Assistant or a standalone service running [wmbusmeters](https://github.com/wmbusmeters/wmbusmeters) picks up the message and decodes the actual values (m³, kWh, etc.).

**Why this approach?**
Offloading the decoding process allows the ESP32 to focus purely on listening, ensuring fewer missed packets and faster scanning in areas with high meter density.

## Installation & Usage

### 1. Grab the Code
Download the `wmbussniffer1276.yaml` file from this repository.

### 2. Configure Credentials
Open the YAML file and edit the following lines with your own settings:

```yaml
wifi:
  networks:
    - ssid: "YOUR_WIFI_SSID"      <-- Change this
      password: "YOUR_WIFI_PASSWORD" <-- Change this

mqtt:
  broker: "YOUR_MQTT_BROKER_IP"   <-- Change this
```

### 3. Flash to Board
Use ESPHome to compile and upload the firmware to your LILYGO T3S3.
> **Note:** The `external_components` section is already configured to pull the stable library from this repository automatically.

## Display Features
The configuration includes a custom layout for the SSD1306 OLED display included on the T3S3:
*   **Device MAC/ID:** Useful for identifying the specific scanner.
*   **Battery Status:** Accurate voltage reading (calibrated for T3S3 voltage divider) and a visual battery bar.
*   **Activity Indicator:** Shows "RX ACTIV!" whenever a packet is received.
*   **Wi-Fi Status:** Connection icon.

## MQTT Payload
The device publishes data to the topic: `ahoi/wmbus/raw`
The payload format is:
`MAC_ADDRESS:RAW_HEX_FRAME:RSSI`

Example:
`A0B1C2D3E4F5:1C440562...: -85`

## Credits
*   Original core library: [SzczepanLeon](https://github.com/SzczepanLeon/esphome-components)
*   Hardware manufacturer: [LILYGO](https://www.lilygo.cc/)