---
layout: default
title: Bluetooth and Wireless Connections
parent: Documentation
nav_order: 7
permalink: /documentation/bluetooth-wireless/
description: Bluetooth, NodeMCU, and MQTT connections
---

# Bluetooth and Wireless Connections

Ardulink-2 supports wireless Arduino communication through Bluetooth, MQTT, and NodeMCU/ESP8266 modules.

## Bluetooth

### Hardware Requirements

- Arduino board with a Bluetooth module (HC-05, HC-06, or similar)
- Bluetooth-enabled computer (or USB Bluetooth adapter)

### Setup

1. Connect the Bluetooth module to Arduino (typically TX/RX pins)
2. Pair the module with your computer via OS Bluetooth settings
3. Add the dependency:

```xml
<dependency>
    <groupId>org.ardulink</groupId>
    <artifactId>ardulink-link-bluetooth</artifactId>
    <version>2.2.0</version>
</dependency>
```

4. Connect using the Bluetooth MAC address:

```java
import org.ardulink.core.Link;
import org.ardulink.core.pin.Pin;
import org.ardulink.core.convenience.Links;

public class BluetoothExample {
    public static void main(String... args) throws Exception {
        try (Link link = Links.getLink("ardulink://bluetooth?address=00:11:22:33:44:55")) {
            link.switchDigitalPin(Pin.digitalPin(13), true);
        }
    }
}
```

{: .note }
Replace `00:11:22:33:44:55` with your Bluetooth module's actual MAC address.

## MQTT

MQTT is ideal for IoT deployments where Arduino and Java run on different machines, or when you need broker-based communication.

### Setup

```xml
<dependency>
    <groupId>org.ardulink</groupId>
    <artifactId>ardulink-link-mqtt</artifactId>
    <version>2.2.0</version>
</dependency>
```

### Using the Standalone MQTT Bridge

The `ardulink-mqtt` module provides a standalone JAR that bridges an Arduino connection to an MQTT broker:

```bash
# Download from Maven Central or use the standalone jar
java -jar ardulink-mqtt-2.2.0.jar \
    -connection "ardulink://serial?port=/dev/ttyACM0" \
    -broker "tcp://localhost:1883"
```

### Connecting via MQTT in Java

```java
try (Link link = Links.getLink("ardulink://mqtt?broker=tcp://localhost:1883&topic=arduino1")) {
    link.switchDigitalPin(Pin.digitalPin(13), true);
}
```

### MQTT Configuration Options

| Parameter | Default | Description |
|:----------|:--------|:------------|
| `broker` | — | MQTT broker URL (e.g., `tcp://localhost:1883`) |
| `topic` | `ardulink` | Base topic for messages |
| `username` | — | MQTT authentication username |
| `password` | — | MQTT authentication password |
| `separatedTopics` | `false` | Use separate topics per pin type |

## NodeMCU / ESP8266

Ardulink-2 has dedicated support for NodeMCU boards, which can connect via serial or MQTT.

### Setup

```xml
<dependency>
    <groupId>org.ardulink</groupId>
    <artifactId>ardulink-link-nodemcu</artifactId>
    <version>2.2.0</version>
</dependency>
```

### Serial Connection

```java
try (Link link = Links.getLink("ardulink://nodemcu?port=/dev/ttyUSB0")) {
    link.switchDigitalPin(Pin.digitalPin(2), true);
}
```

### MQTT Connection

```java
try (Link link = Links.getLink("ardulink://nodemcu?broker=tcp://localhost:1883")) {
    link.switchDigitalPin(Pin.digitalPin(2), true);
}
```

{: .note }
When using MQTT with NodeMCU, the firmware includes a Lua `init.lua` script (available in the distribution) that connects the NodeMCU to the MQTT broker.

## Raspberry Pi

The Raspberry Pi link adapter controls GPIO pins directly on the Pi (no Arduino needed):

```xml
<dependency>
    <groupId>org.ardulink</groupId>
    <artifactId>ardulink-link-raspberry</artifactId>
    <version>2.2.0</version>
</dependency>
```

```java
try (Link link = Links.getLink("ardulink://raspberry")) {
    link.switchDigitalPin(Pin.digitalPin(7), true);
}
```

## Choosing a Transport

| Use Case | Recommended Transport |
|:---------|:---------------------|
| Direct USB connection | Serial (JSSC) |
| Short-range untethered | Bluetooth |
| IoT / remote access | MQTT |
| ESP8266 projects | NodeMCU |
| Raspberry Pi GPIO | Raspberry Pi |
| Testing without hardware | Virtual |

## Next Steps

- [Ardulink-MQTT]({{ '/documentation/ardulink-mqtt/' | relative_url }}) — Deep dive into MQTT integration
- [Reading Pins]({{ '/documentation/reading-pins/' | relative_url }}) — Event listeners
- [Sample: Node-RED Dashboard]({{ '/documentation/sample-node-red/' | relative_url }}) — Dashboard with Node-RED
