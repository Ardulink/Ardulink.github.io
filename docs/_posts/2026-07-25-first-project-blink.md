---
layout: default
title: "First Project: Blink an LED from Java"
parent: Documentation
nav_order: 3
permalink: /documentation/first-project/
description: Step-by-step tutorial from scratch
---

# First Project: Blink an LED from Java

In this tutorial, you will build a complete Java application that controls the built-in LED on an Arduino board. This covers the full workflow: project setup, firmware upload, Java code, and running it.

## What You Need

- Arduino Uno (or compatible board)
- USB cable
- Java 11+ with Maven
- A text editor or IDE

## 1. Create the Maven Project

```bash
mkdir ardulink-blink && cd ardulink-blink
```

Create `pom.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>ardulink-blink</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
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
    </dependencies>
</project>
```

## 2. Flash the Firmware

Open the [WebSerial Flasher](https://ardulink.github.io/Firmware/webflasher.html) in your browser, connect your Arduino, and click **Program**. This uploads the Ardulink protocol firmware that lets Java communicate with the board.

See [Arduino Firmware Setup]({{ '/documentation/arduino-firmware/' | relative_url }}) for more details.

## 3. Write the Java Code

Create `src/main/java/com/example/Blink.java`:

```java
package com.example;

import java.util.concurrent.TimeUnit;

import org.ardulink.core.Link;
import org.ardulink.core.pin.Pin;
import org.ardulink.core.pin.Pin.DigitalPin;
import org.ardulink.core.convenience.Links;

public class Blink {

    public static void main(String... args) throws Exception {
        try (Link link = Links.getDefault()) {
            DigitalPin pin = Pin.digitalPin(13);
            boolean state = true;

            System.out.println("Starting LED blink. Press Ctrl+C to stop.");

            while (true) {
                link.switchDigitalPin(pin, state);
                System.out.printf("LED %s%n", state ? "ON" : "OFF");
                TimeUnit.SECONDS.sleep(1);
                state = !state;
            }
        }
    }
}
```

### What This Code Does

1. **`Links.getDefault()`** — Creates a connection using the first available link adapter on the classpath. With `ardulink-link-serial-jssc` present, it connects to the Arduino over serial.

2. **`Pin.digitalPin(13)`** — References digital pin 13, which is connected to the built-in LED on most Arduino boards.

3. **`link.switchDigitalPin(pin, state)`** — Sends a command to the Arduino to set pin 13 HIGH (`true`) or LOW (`false`).

4. The `try-with-resources` block ensures the connection is closed when the program exits (Ctrl+C).

## 4. Run It

```bash
mvn compile exec:java -Dexec.mainClass="com.example.Blink"
```

You should see:

```
Starting LED blink. Press Ctrl+C to stop.
LED ON
LED OFF
LED ON
LED OFF
...
```

And the built-in LED on your Arduino should blink once per second.

## 5. Reading Pin Values

Now let's extend this to also read a button connected to pin 2:

```java
package com.example;

import java.util.concurrent.TimeUnit;

import org.ardulink.core.Link;
import org.ardulink.core.pin.Pin;
import org.ardulink.core.pin.Pin.DigitalPin;
import org.ardulink.core.events.PinChangedEvent;
import org.ardulink.core.events.FilteredEventListenerAdapter;
import org.ardulink.core.convenience.Links;

public class BlinkWithButton {

    public static void main(String... args) throws Exception {
        try (Link link = Links.getDefault()) {
            DigitalPin ledPin = Pin.digitalPin(13);
            DigitalPin buttonPin = Pin.digitalPin(2);

            // Listen for button presses on pin 2, filtered automatically
            link.addListener(FilteredEventListenerAdapter.filter(
                PinChangedEvent.class,
                buttonPin,
                pinEvent -> System.out.printf("Button pressed: %s%n", pinEvent.getValue())
            ));

            // Start listening on pin 2
            link.startListening(buttonPin);
            System.out.println("Listening on pin 2. Press the button...");

            // Blink LED while waiting
            boolean state = true;
            while (true) {
                link.switchDigitalPin(ledPin, state);
                TimeUnit.SECONDS.sleep(1);
                state = !state;
            }
        }
    }
}
```

## Next Steps

- [Reading Pins]({{ '/documentation/reading-pins/' | relative_url }}) — Deep dive into pin event listeners
- [Link Types]({{ '/documentation/link-types/' | relative_url }}) — Connect via Bluetooth, MQTT, etc.
- [Custom Messages]({{ '/documentation/custom-messages/' | relative_url }}) — Send arbitrary data between Java and Arduino
