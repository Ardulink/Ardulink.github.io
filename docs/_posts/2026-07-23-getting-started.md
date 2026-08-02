---
layout: default
title: Getting Started with Ardulink-2
parent: Documentation
nav_order: 2
permalink: /documentation/getting-started/
description: Prerequisites, dependencies, first connection
---

# Getting Started with Ardulink-2

This guide walks you through setting up Ardulink-2 and making your first connection to an Arduino board.

## Prerequisites

- **Java 11+** (JDK installed and `java` on PATH)
- **Arduino board** (Uno, Nano, Mega, or compatible)
- **USB cable** to connect the Arduino to your computer
- **Maven** or another build tool (Gradle, etc.)
- **Arduino IDE** (optional, for manual firmware upload)

## Step 1: Add Maven Dependencies

Add the core library and at least one link adapter to your `pom.xml`:

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

{: .note }
`ardulink-core-base` is always required. The link adapter (`ardulink-link-serial-jssc` here) depends on your connection method. See [Serial Connections]({{ '/documentation/serial-connections/' | relative_url }}}) for alternatives.

For Gradle:

```groovy
implementation 'org.ardulink:ardulink-core-base:2.2.0'
implementation 'org.ardulink:ardulink-link-serial-jssc:2.2.0'
```

## Step 2: Flash the Arduino Firmware

Your Arduino needs the [Ardulink firmware](https://github.com/Ardulink/Firmware) to communicate with the Java library.

**Easiest method — WebSerial Flasher (no install):**

1. Open the [WebSerial Flasher](https://ardulink.github.io/Firmware/webflasher.html) in Chrome or Edge
2. Click **↻ Latest** to load the latest firmware
3. Click **Program** and select your Arduino's serial port
4. Wait for the upload to complete

See [Arduino Firmware Setup]({{ '/documentation/arduino-firmware/' | relative_url }}}) for alternative methods.

## Step 3: Write Your First Java Program

```java
package com.example.ardulink;

import org.ardulink.core.Link;
import org.ardulink.core.pin.Pin;
import org.ardulink.core.pin.Pin.DigitalPin;
import org.ardulink.core.convenience.Links;

public class Main {
    public static void main(String... args) throws Exception {
        try (Link link = Links.getDefault()) {
            DigitalPin pin = Pin.digitalPin(13);
            boolean state = true;
            while (true) {
                link.switchDigitalPin(pin, state);
                System.out.printf("pin switched %s%n", (state ? "on" : "off"));
                Thread.sleep(1000);
                state = !state;
            }
        }
    }
}
```

{: .note }
`Links.getDefault()` returns the first available link — typically a serial connection when `ardulink-link-serial-jssc` is on the classpath.

## Step 4: Run It

```bash
mvn compile exec:java -Dexec.mainClass="com.example.ardulink.Main"
```

You should see the built-in LED on your Arduino blink on and off, with console output:

```
pin switched on
pin switched off
pin switched on
...
```

## Troubleshooting

### No serial port found

- Check that the Arduino is connected via USB
- On Linux, ensure your user has permission to access serial ports: `sudo usermod -a -G dialout $USER`
- On macOS, the port is typically `/dev/cu.usbmodem*`
- On Windows, check Device Manager for the COM port

### `Links.getDefault()` throws exception

- Make sure you have a link adapter JAR on the classpath (e.g., `ardulink-link-serial-jssc`)
- If using Maven, verify the dependency is in your `pom.xml`

### Connection hangs or times out

- Ensure the Ardulink firmware is uploaded to the board (not a blank sketch or other firmware)
- Try a different USB cable (some are charge-only)
- Check the serial port isn't occupied by another application

## Next Steps

- [First Project: Blink an LED]({{ '/documentation/first-project/' | relative_url }}}) — Detailed walkthrough
- [Link Types]({{ '/documentation/link-types/' | relative_url }}}) — Explore all transport options
- [Reading Pins]({{ '/documentation/reading-pins/' | relative_url }}}) — Listen for pin changes
