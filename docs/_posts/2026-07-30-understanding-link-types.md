---
layout: default
title: Understanding Link Types
parent: Documentation
nav_order: 5
permalink: /documentation/link-types/
---

# Understanding Link Types

Ardulink-2 uses a pluggable transport system. The core library defines a `Link` interface, and each link adapter implements it for a specific communication protocol. You only need to include the adapters you actually use.

## The Link Interface

Every connection to an Arduino goes through the `Link` interface (`org.ardulink.core.Link`). It provides methods for:

| Method | Description |
|:-------|:------------|
| `switchDigitalPin(pin, value)` | Set a digital pin HIGH or LOW |
| `switchAnalogPin(pin, value)` | Set an analog pin to a value (0-1023) |
| `startListening(pin)` | Subscribe to changes on a pin |
| `stopListening(pin)` | Unsubscribe from pin changes |
| `sendKeyPressEvent(...)` | Simulate a keyboard event |
| `sendTone(tone)` | Play a tone on a pin |
| `sendNoTone(pin)` | Stop playing a tone |
| `sendCustomMessage(messages)` | Send arbitrary strings to the Arduino |
| `addListener(listener)` | Listen for pin change events |
| `addRplyListener(listener)` | Listen for reply messages |
| `addCustomListener(listener)` | Listen for custom messages |

## Available Link Adapters

### Serial (USB)

| Module | Library | When to Use |
|:-------|:--------|:------------|
| `ardulink-link-serial-jssc` | JSSC | **Recommended** for most setups |
| `ardulink-link-serial-nrrxtx` | NRSerialRxTx | Alternative if JSSC has issues |
| `ardulink-link-serial-rxtx` | Rxtx | Legacy projects already using Rxtx |

All three communicate over USB serial. Choose JSSC unless you have a specific reason to use another.

### Wireless

| Module | Transport | When to Use |
|:-------|:----------|:------------|
| `ardulink-link-bluetooth` | Bluetooth SPP | Short-range wireless with HC-05/HC-06 modules |
| `ardulink-link-mqtt` | MQTT | IoT deployments, remote access, broker-based |
| `ardulink-link-nodemcu` | ESP8266 | NodeMCU boards via serial or MQTT |

### Single-Board Computers

| Module | Board | When to Use |
|:-------|:------|:------------|
| `ardulink-link-raspberry` | Raspberry Pi | GPIO control directly on Pi |
| `ardulink-link-digispark` | Digispark | Tiny USB boards |

### Special Purpose

| Module | Description |
|:-------|:------------|
| `ardulink-link-virtual` | Virtual links for testing without hardware |
| `ardulink-link-proxy` | Network proxy for remote serial access |

## How Link Discovery Works

When you call `Links.getDefault()`, Ardulink scans the classpath for link adapter JARs. Each adapter registers itself, and the first available one is used.

This means:
1. Add the adapter JAR to your classpath (Maven dependency)
2. Call `Links.getDefault()`
3. Ardulink picks the available adapter automatically

If you need a specific adapter, configure it explicitly.

## Using a Specific Link

You can configure a specific link type and connection string:

```java
import org.ardulink.core.Link;
import org.ardulink.core.convenience.Links;

// Connect to a specific serial port
Link link = Links.getLink("ardulink://serial?port=/dev/ttyACM0&baudrate=115200");
```

Connection string formats:
- `ardulink://serial?port=/dev/ttyACM0&baudrate=115200`
- `ardulink://bluetooth?address=00:11:22:33:44:55`
- `ardulink://mqtt?broker=tcp://localhost:1883`
- `ardulink://virtual-console` (for testing)
- `ardulink://virtual-random` (random values for testing)

## Multiple Arduino Connections

You can connect to multiple Arduinos simultaneously:

```java
Link link1 = /* configured for Arduino 1 */;
Link link2 = /* configured for Arduino 2 */;

link1.switchDigitalPin(Pin.digitalPin(13), true);
link2.switchDigitalPin(Pin.digitalPin(13), false);
```

## Next Steps

- [Serial Connections]({{ '/documentation/serial-connections/' | relative_url }) — Deep dive into serial options
- [Bluetooth and Wireless]({{ '/documentation/bluetooth-wireless/' | relative_url }) — Wireless setups
- [Ardulink-2 Architecture]({{ '/documentation/architecture/' | relative_url }) — Internal module system
