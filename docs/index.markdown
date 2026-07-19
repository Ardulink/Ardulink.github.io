---
layout: default
title: Home
nav_order: 1
permalink: /
---

# Ardulink-2
{: .fs-9 }

A complete, open source, Java solution for the control and coordination of Arduino boards.
{: .fs-6 .fw-300 }

[Get Started](#getting-started){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
[View on GitHub](https://github.com/Ardulink/Ardulink-2){: .btn .fs-5 .mb-4 .mb-md-0 }

---

## What is Ardulink-2?

Ardulink-2 is a Java framework that lets you control and monitor Arduino boards from Java applications. It provides a modular, transport-agnostic architecture — connect via Serial, Bluetooth, MQTT, TCP/IP, or even virtual links for testing.

```java
public static void main(String... args) throws Exception {
    try (Link link = Links.getDefault()) {
        DigitalPin pin = Pin.digitalPin(13);
        boolean state = true;
        while (true) {
            link.switchDigitalPin(pin, state);
            System.out.printf("pin switched %s%n", (state ? "on" : "off"));
            TimeUnit.SECONDS.sleep(1);
            state = !state;
        }
    }
}
```

## Key Features

| Feature | Description |
|:--------|:------------|
| **Modular Architecture** | Core library plus optional link adapters — only include what you need |
| **Multiple Transports** | Serial (USB), Bluetooth, MQTT, TCP/IP, NodeMCU, Raspberry Pi, Digispark |
| **Event-Driven** | Listen for digital/analog pin changes, custom messages, and connection events |
| **REST API** | Control Arduino via HTTP/JSON with built-in Swagger UI |
| **MQTT Integration** | IoT-ready: bridge Arduino to MQTT brokers and Node-RED dashboards |
| **Apache Camel** | Enterprise integration patterns with Arduino devices |
| **Swing GUI** | Pre-built GUI components for desktop control applications |
| **Firmata Protocol** | Experimental support for the standard Firmata protocol |

## Quick Start

### 1. Add Dependencies (Maven)

```xml
<dependency>
    <groupId>org.ardulink</groupId>
    <artifactId>ardulink-core-base</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>org.ardulink</groupId>
    <artifactId>ardulink-link-serial-jssc</artifactId>
    <version>2.2.0</version>
</dependency>
```

### 2. Flash the Firmware

Upload the [Ardulink firmware](https://github.com/Ardulink/Firmware) to your Arduino board using the [WebSerial Flasher](https://ardulink.github.io/Firmware/webflasher.html) or Arduino IDE.

### 3. Connect and Control

```java
try (Link link = Links.getDefault()) {
    link.switchDigitalPin(Pin.digitalPin(13), true);
}
```

---

## Latest Release

**Version 2.2.0** (November 17, 2024) — [Release Notes](https://github.com/Ardulink/Ardulink-2/releases/tag/2.2.0)

- Firmata protocol support (preview)
- REST API with Swagger UI
- Migrated to Java 8 minimum
- Moved samples to [Ardulink-2-Samples](https://github.com/Ardulink/Ardulink-2-Samples)

[View all releases](https://github.com/Ardulink/Ardulink-2/releases)
