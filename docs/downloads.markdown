---
layout: default
title: Downloads
nav_order: 3
permalink: /downloads/
---

# Downloads

## Maven Central

The recommended way to use Ardulink-2 is via Maven. Add dependencies to your `pom.xml`:

### Core

```xml
<dependency>
    <groupId>org.ardulink</groupId>
    <artifactId>ardulink-core-base</artifactId>
    <version>2.2.0</version>
</dependency>
```

### Link Adapters

Choose one or more transport adapters:

| Artifact | Transport | Description |
|:---------|:----------|:------------|
| `ardulink-link-serial-jssc` | Serial (USB) | Serial via JSSC library (recommended) |
| `ardulink-link-serial-nrrxtx` | Serial (USB) | Serial via NRSerialRxTx |
| `ardulink-link-serial-rxtx` | Serial (USB) | Serial via legacy Rxtx |
| `ardulink-link-bluetooth` | Bluetooth | Bluetooth serial connections |
| `ardulink-link-mqtt` | MQTT | MQTT broker connections |
| `ardulink-link-nodemcu` | NodeMCU/ESP8266 | NodeMCU serial or MQTT |
| `ardulink-link-raspberry` | Raspberry Pi | GPIO on Raspberry Pi |
| `ardulink-link-digispark` | Digispark | Digispark USB boards |
| `ardulink-link-proxy` | Network Proxy | Network proxy server |
| `ardulink-link-virtual` | Virtual | Testing without hardware |

Example for serial via JSSC:

```xml
<dependency>
    <groupId>org.ardulink</groupId>
    <artifactId>ardulink-link-serial-jssc</artifactId>
    <version>2.2.0</version>
</dependency>
```

### Integration Modules

| Artifact | Description |
|:---------|:------------|
| `ardulink-mqtt` | Standalone MQTT bridge (jar with CLI) |
| `ardulink-rest` | REST API server with Swagger UI |
| `ardulink-camel` | Apache Camel component |
| `ardulink-swing` | Java Swing GUI components |
| `ardulink-mail` | Email notification integration |
| `ardulink-console` | Interactive console |
| `ardulink-networkproxyserver` | Network proxy server |

### Find All Artifacts

Browse all available artifacts on [Maven Central](https://search.maven.org/#search%7Cga%7C1%7Cg%3A%22org.ardulink%22).

## Pre-compiled Firmware

Download ready-to-flash `.hex` files from the [Firmware Releases](https://github.com/Ardulink/Firmware/releases):

- Arduino Uno
- Arduino Nano
- Arduino Mega

Upload using:
- [WebSerial Flasher](https://ardulink.github.io/Firmware/webflasher.html) (browser, no install needed)
- [XLoader](http://rushemotto.com/xloader/) (Windows)
- [avrdude](https://github.com/avrdudes/avrdude) (macOS/Linux/Windows)
- [Arduino IDE](https://www.arduino.cc/en/software/)

## Sample Projects

Clone the [Ardulink-2-Samples](https://github.com/Ardulink/Ardulink-2-Samples) repository:

```bash
git clone https://github.com/Ardulink/Ardulink-2-Samples.git
cd Ardulink-2-Samples
./mvnw install
```

Available samples:
- `example-simplesmartcardriver` — GUI car controller
- `example-buttonquest` — Button-based interaction
- `example-datareceiver` — Data reception
- `example-joysticksmartcardriver` — Joystick car controller
- `example-uniqueid` — Unique board identification
- `node-red` — Node-RED dashboard integration
