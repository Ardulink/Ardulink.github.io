---
layout: default
title: What is Ardulink-2?
parent: Documentation
nav_order: 1
permalink: /documentation/what-is-ardulink-2/
---

# What is Ardulink-2?

Ardulink-2 is a complete, open source, Java framework for controlling and coordinating Arduino boards from Java applications. It is the successor to the original [Ardulink](http://ardulink.org/) project, rebuilt with a modular architecture to support multiple communication transports and integration patterns.

## The Problem

Arduino boards are powerful microcontrollers, but building complex control logic, dashboards, or enterprise integrations purely in C++ on the Arduino is limiting. Java offers rich libraries, GUI frameworks, enterprise messaging, REST APIs, and much more.

Ardulink-2 bridges this gap: run your control logic in Java while the Arduino handles the physical I/O.

## How It Works

Ardulink-2 has two sides:

1. **Java side** — The Ardulink-2 library running in your Java application
2. **Arduino side** — The [Ardulink firmware](https://github.com/Ardulink/Firmware) uploaded to your Arduino board

They communicate over a transport layer (Serial, Bluetooth, MQTT, etc.) using the Ardulink protocol. The Java side sends commands (set pin, read pin, play tone) and the firmware executes them on the Arduino.

```
┌──────────────────┐         Serial/USB         ┌──────────────────┐
│  Java Application│ ──────────────────────────> │   Arduino Board  │
│  (Ardulink-2)    │ <────────────────────────── │  (Ardulink FW)   │
└──────────────────┘    pin events, responses    └──────────────────┘
```

## Modular Architecture

Unlike the original monolithic Ardulink, version 2 separates concerns into modules:

- **Core** (`ardulink-core-base`) — The `Link` interface, `Pin` model, and event system. This is always required.
- **Link Adapters** — Pluggable transport modules (serial, Bluetooth, MQTT, etc.). Add only the ones you need.
- **Integration Modules** — Higher-level features like REST APIs, Camel components, Swing GUIs.

This means your application only pulls in the code it actually uses.

## What Changed from Ardulink 1

| Aspect | Ardulink 1 | Ardulink 2 |
|:-------|:-----------|:-----------|
| Architecture | Monolithic | Modular (Maven multi-module) |
| Transports | Hardcoded serial | Pluggable link adapters |
| Distribution | Custom download | Maven Central |
| MQTT | Not supported | Full support via `ardulink-mqtt` |
| REST | Not supported | Full support via `ardulink-rest` including Swagger UI |
| Camel | Not supported | Apache Camel integration |
| Protocol | Custom | Custom + Firmata (preview) |
| Java version | Java 6+ | Java 8+ (2.2.0), Java 11+ (future) |

## Who Is It For?

- **Arduino developers** who want richer control logic in Java
- **Java developers** who want to interact with Arduino hardware
- **IoT tinkerers** who want to bridge Arduino with MQTT, REST, or enterprise systems
- **Educators** teaching embedded systems with a familiar language

## Next Steps

- [Getting Started]({{ '/documentation/getting-started/' | relative_url }}) — Set up your first connection
- [Arduino Firmware]({{ '/documentation/arduino-firmware/' | relative_url }}) — Flash the firmware to your board
- [Understanding Link Types]({{ '/documentation/link-types/' | relative_url }}) — Explore available transports
