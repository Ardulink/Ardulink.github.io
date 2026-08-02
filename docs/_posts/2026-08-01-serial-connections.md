---
layout: default
title: Serial Connections
parent: Documentation
nav_order: 6
permalink: /documentation/serial-connections/
description: USB serial with JSSC, NRSerialRxTx, or Rxtx
---

# Serial Connections with Ardulink-2

Serial over USB is the most common way to connect Arduino boards. Ardulink-2 provides three serial link adapters based on different Java serial libraries.

## Which Serial Adapter to Choose

| Adapter | Maven Artifact | Pros | Cons |
|:--------|:---------------|:-----|:-----|
| **JSSC** | `ardulink-link-serial-jssc` | Pure Java, no native deps, actively maintained | Fewer platform quirks |
| **NRSerialRxTx** | `ardulink-link-serial-nrrxtx` | Good cross-platform support | Less commonly used |
| **Rxtx** | `ardulink-link-serial-rxtx` | Legacy compatibility | Unmaintained upstream |

**Recommendation:** Use `ardulink-link-serial-jssc` unless you have a specific reason to choose another.

## Setup

### Maven

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

### Gradle

```groovy
implementation 'org.ardulink:ardulink-core-base:2.2.0'
implementation 'org.ardulink:ardulink-link-serial-jssc:2.2.0'
```

## Connecting

```java
import org.ardulink.core.Link;
import org.ardulink.core.pin.Pin;
import org.ardulink.core.convenience.Links;

public class SerialExample {
    public static void main(String... args) throws Exception {
        try (Link link = Links.getDefault()) {
            // The adapter auto-discovers the serial port
            link.switchDigitalPin(Pin.digitalPin(13), true);
        }
    }
}
```

## Specifying the Serial Port

If you need to target a specific port, use the connection string:

```java
// Linux
Link link = Links.getLink("ardulink://serial?port=/dev/ttyACM0&baudrate=115200");

// macOS
Link link = Links.getLink("ardulink://serial?port=/dev/cu.usbmodem14201&baudrate=115200");

// Windows
Link link = Links.getLink("ardulink://serial?port=COM3&baudrate=115200");
```

## Finding Your Serial Port

### Linux

```bash
ls /dev/ttyACM* /dev/ttyUSB*
# or
ls /dev/serial/by-id/*
```

### macOS

```bash
ls /dev/cu.usbmodem* /dev/cu.usbserial*
```

### Windows

Check Device Manager under **Ports (COM & LPT)** for your Arduino.

## Serial Port Permissions (Linux)

By default, serial ports require root access. Add your user to the `dialout` group:

```bash
sudo usermod -a -G dialout $USER
```

Log out and back in for the change to take effect.

## Baud Rate

The default baud rate for the Ardulink firmware is **115200**. This should match between the firmware and the Java connection. You can change it in the connection string:

```
ardulink://serial?port=/dev/ttyACM0&baudrate=9600
```

## Troubleshooting

| Problem | Solution |
|:--------|:---------|
| Port not found | Check cable, try different USB port, verify driver installed |
| Permission denied (Linux) | Add user to `dialout` group |
| Garbage data | Baud rate mismatch — ensure both sides use same rate |
| Connection timeout | Wrong port, or firmware not uploaded |
| Port busy | Close Arduino IDE Serial Monitor, or other applications using the port |

## Next Steps

- [Bluetooth and Wireless]({{ '/documentation/bluetooth-wireless/' | relative_url }) — Go wireless
- [Reading Pins]({{ '/documentation/reading-pins/' | relative_url }) — Listen for events
- [Link Types Overview]({{ '/documentation/link-types/' | relative_url }) — All transport options
