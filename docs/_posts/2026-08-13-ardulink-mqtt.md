---
layout: default
title: "Ardulink-MQTT: IoT Ready Arduino Communication"
parent: Documentation
nav_order: 11
permalink: /documentation/ardulink-mqtt/
description: IoT-ready MQTT integration
---

# Ardulink-MQTT: IoT Ready Arduino Communication

The `ardulink-mqtt` module bridges Ardulink-2 with MQTT brokers, enabling IoT-style communication between Arduino boards and any MQTT-compatible system.

## What It Does

`ardulink-mqtt` acts as a translator:
- Connects to your Arduino via any Ardulink link (serial, Bluetooth, etc.)
- Exposes the Arduino's pins as MQTT topics
- Publishes pin changes from Arduino to MQTT
- Subscribes to MQTT topics and forwards commands to Arduino

```
┌──────────┐  Serial   ┌──────────────┐  MQTT  ┌─────────────┐
│  Arduino  │ <───────> │ ardulink-mqtt│ <────> │ MQTT Broker │
│  (FW)     │           │ (bridge)     │        │ (Mosquitto) │
└──────────┘           └──────────────┘        └─────────────┘
                                                        │
                                               ┌────────┴────────┐
                                               │  Java / Node-RED │
                                               │  / Any MQTT app  │
                                               └─────────────────┘
```

## Quick Start

### 1. Start the Bridge

```bash
java -jar ardulink-mqtt-2.2.0.jar \
    -connection "ardulink://serial?port=/dev/ttyACM0" \
    -broker "tcp://localhost:1883"
```

For testing without hardware:

```bash
java -jar ardulink-mqtt-2.2.0.jar \
    -connection "ardulink://virtual-console" \
    -broker "tcp://localhost:1883"
```

### 2. Subscribe to Arduino Events

Using `mosquitto_sub`:

```bash
# Subscribe to all Arduino messages
mosquitto_sub -t "ardulink/#"

# Subscribe to pin 13 digital changes
mosquitto_sub -t "ardulink/digital/13"
```

### 3. Send Commands to Arduino

```bash
# Turn on LED on pin 13
mosquitto_pub -t "ardulink/digital/13" -m "true"

# Set analog pin A0 to 512
mosquitto_pub -t "ardulink/analog/0" -m "512"
```

## Maven Dependency

For programmatic MQTT link usage (not the standalone bridge):

```xml
<dependency>
    <groupId>org.ardulink</groupId>
    <artifactId>ardulink-link-mqtt</artifactId>
    <version>2.2.0</version>
</dependency>
```

```java
try (Link link = Links.getLink("ardulink://mqtt?broker=tcp://localhost:1883&topic=arduino1")) {
    link.switchDigitalPin(Pin.digitalPin(13), true);
}
```

## Command Line Options

| Option | Description |
|:-------|:------------|
| `-connection <url>` | Ardulink connection URL (e.g., `ardulink://serial?port=...`) |
| `-broker <url>` | MQTT broker URL (e.g., `tcp://localhost:1883`) |
| `-topic <name>` | Base topic prefix (default: `ardulink`) |
| `-username <user>` | MQTT authentication username |
| `-password <pass>` | MQTT authentication password |
| `-separatedTopics <bool>` | Use separate topics per pin type |

## MQTT Topic Structure

By default, topics follow this pattern:

| Topic | Direction | Description |
|:------|:----------|:------------|
| `ardulink/digital/<pin>` | Both | Digital pin read/write |
| `ardulink/analog/<pin>` | Both | Analog pin read/write |
| `ardulink/tone/<pin>` | Java → Arduino | Play a tone |
| `ardulink/custom` | Both | Custom messages |

## Integration with Node-RED

Ardulink-MQTT works seamlessly with [Node-RED](https://nodered.org/). See the [Node-RED Sample]({{ '/documentation/sample-node-red/' | relative_url }}}) for a complete walkthrough including Docker Compose setup.

## Integration with Home Automation

Since MQTT is a standard IoT protocol, Ardulink-MQTT integrates with:

- **Home Assistant** — MQTT sensor/switch entities
- **OpenHAB** — MQTT binding
- **Node-RED** — Flow-based visual programming
- **Grafana** — Via MQTT data source
- **Custom dashboards** — Any MQTT client

## Next Steps

- [Sample: Node-RED Dashboard]({{ '/documentation/sample-node-red/' | relative_url }}) — Complete Node-RED example
- [REST API]({{ '/documentation/ardulink-rest/' | relative_url }}) — HTTP alternative
- [Bluetooth and Wireless]({{ '/documentation/bluetooth-wireless/' | relative_url }}) — Other wireless options
