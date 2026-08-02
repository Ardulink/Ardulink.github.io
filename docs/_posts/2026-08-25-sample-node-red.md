---
layout: default
title: "Sample: Arduino with Node-RED Dashboard"
parent: Documentation
nav_order: 16
permalink: /documentation/sample-node-red/
description: Arduino + Node-RED + MQTT dashboard
---

# Sample: Arduino with Node-RED Dashboard

This walkthrough covers the Node-RED integration sample from [Ardulink-2-Samples](https://github.com/Ardulink/Ardulink-2-Samples) — controlling Arduino via a web-based dashboard using MQTT.

## Architecture

```
┌──────────┐  Serial  ┌──────────────┐  MQTT  ┌───────────┐  HTTP  ┌──────────┐
│  Arduino  │ <─────> │ ardulink-mqtt│ <────> │ Mosquitto │ <────> │ Node-RED │
│  (FW)     │          │ (bridge)     │        │ (broker)  │        │ (UI)     │
└──────────┘          └──────────────┘        └───────────┘        └──────────┘
```

- **Arduino** — Runs the Ardulink firmware
- **ardulink-mqtt** — Bridges serial/Arduino to MQTT
- **Mosquitto** — MQTT message broker
- **Node-RED** — Visual flow-based programming with dashboard

## Prerequisites

- Docker installed on your machine

## Quick Start

### 1. Start Node-RED + Mosquitto

The sample includes a Docker Compose file:

```bash
cd Ardulink-2-Samples/node-red
docker-compose up -d
```

This starts:
- **Mosquitto** MQTT broker on port `1883`
- **Node-RED** on port `1880`

### 2. Start the MQTT Bridge

Connect to your Arduino (or use virtual-console for testing):

```bash
java -jar ardulink-mqtt-2.2.0.jar \
    -connection ardulink://virtual-console
```

### 3. Open the Dashboard

Navigate to [http://localhost:1880/ui](http://localhost:1880/ui) in your browser.

You'll see a dashboard with controls that send commands through MQTT to the Arduino.

### 4. Edit the Flow

Navigate to [http://localhost:1880/](http://localhost:1880/) to see or modify the Node-RED flow.

## How It Works

### Node-RED Flow

The flow consists of:

1. **MQTT In nodes** — Subscribe to Arduino pin topics
2. **Dashboard widgets** — Sliders, buttons, switches
3. **MQTT Out nodes** — Publish commands back to Arduino

### Topic Mapping

| Node-RED Topic | Arduino Action |
|:---------------|:---------------|
| `ardulink/digital/13` | Read/write digital pin 13 |
| `ardulink/analog/0` | Read/write analog pin A0 |
| `ardulink/custom` | Custom messages |

## Building Your Own Dashboard

### Add a Slider

1. Drag an **MQTT Out** node to the flow
2. Set the topic to `ardulink/analog/0`
3. Connect a **Slider** node (range 0-1023)
4. Deploy — moving the slider sets the Arduino's analog pin

### Add a Status Display

1. Drag an **MQTT In** node, topic: `ardulink/digital/13`
2. Connect to a **Text** or **Gauge** node
3. Deploy — pin changes appear on the dashboard

### Complex Dashboard

Add widgets for:
- Motor speed sliders
- Direction buttons
- Sensor value gauges
- LED status indicators
- Temperature/humidity charts

## Using with Physical Arduino

Replace the virtual-console connection with your real serial port:

```bash
java -jar ardulink-mqtt-2.2.0.jar \
    -connection "ardulink://serial?port=/dev/ttyACM0" \
    -broker "tcp://localhost:1883"
```

## Next Steps

- [Ardulink-MQTT]({{ '/documentation/ardulink-mqtt/' | relative_url }}) — MQTT details
- [REST API]({{ '/documentation/ardulink-rest/' | relative_url }}) — HTTP alternative
- [Sample: Smart Car Driver]({{ '/documentation/sample-smart-car/' | relative_url }}) — Desktop GUI alternative
