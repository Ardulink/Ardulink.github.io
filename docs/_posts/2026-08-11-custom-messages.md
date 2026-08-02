---
layout: default
title: Custom Messages Between Java and Arduino
parent: Documentation
nav_order: 10
permalink: /documentation/custom-messages/
description: Arbitrary string communication protocols
---

# Custom Messages Between Java and Arduino

Ardulink-2 supports arbitrary string-based communication between Java and Arduino, letting you build custom protocols for any application.

## Sending Custom Messages

```java
import org.ardulink.core.Link;
import org.ardulink.core.convenience.Links;

public class CustomMessageExample {
    public static void main(String... args) throws Exception {
        try (Link link = Links.getDefault()) {
            // Send a custom message
            link.sendCustomMessage("Hello from Java!");
            link.sendCustomMessage("SENSOR", "1024");
            link.sendCustomMessage("CMD", "SET", "SPEED", "100");
        }
    }
}
```

## Receiving Custom Messages

Register a `CustomListener` to receive messages from the Arduino:

```java
import org.ardulink.core.Link;
import org.ardulink.core.events.CustomEvent;
import org.ardulink.core.convenience.Links;

public class CustomReceiver {
    public static void main(String... args) throws Exception {
        try (Link link = Links.getDefault()) {

            link.addCustomListener(event -> {
                if (event instanceof CustomEvent) {
                    CustomEvent ce = (CustomEvent) event;
                    String[] messages = ce.getMessages();
                    System.out.printf("Received %d message(s): %s%n",
                        messages.length, String.join(", ", messages));
                }
            });

            System.out.println("Waiting for messages from Arduino...");
            Thread.sleep(Long.MAX_VALUE);
        }
    }
}
```

## Custom Messages on the Arduino Side

In the [Ardulink firmware](https://github.com/Ardulink/Firmware), custom messages are exchanged using the protocol's custom message command. The firmware processes incoming custom messages and can send responses back.

### Example: Sensor Data Protocol

Define a simple protocol:
- Java sends: `READ_SENSOR <pin>`
- Arduino responds with: `SENSOR_VALUE <pin> <value>`

**Java side:**

```java
// Request sensor reading
link.sendCustomMessage("READ_SENSOR", "A0");

// Listen for response
link.addCustomListener(event -> {
    String[] msg = ((CustomEvent) event).getMessages();
    if (msg.length >= 3 && "SENSOR_VALUE".equals(msg[0])) {
        String pin = msg[1];
        int value = Integer.parseInt(msg[2]);
        System.out.printf("Sensor %s = %d%n", pin, value);
    }
});
```

## Building a Custom Protocol

For more complex use cases, you can layer a protocol on top of the custom message mechanism:

```java
// Command: SET LED <pin> <state>
link.sendCustomMessage("SET", "LED", "13", "ON");

// Command: GET ANALOG <pin>
link.sendCustomMessage("GET", "ANALOG", "0");

// Command: MOTOR <direction> <speed>
link.sendCustomMessage("MOTOR", "FORWARD", "200");
```

On the Arduino side, parse the first message element as a command and subsequent elements as parameters.

## Use Cases

- **Sensor queries** — Request specific sensor readings on demand
- **Configuration** — Send configuration parameters to Arduino at runtime
- **Status reporting** — Arduino sends status updates to Java
- **Custom drivers** — Implement any proprietary protocol
- **Multi-device coordination** — Route messages between multiple sensors/actuators

## Protocol Details

See the [Ardulink2 Specification](https://github.com/Ardulink/Firmware/blob/main/Ardulink2-Specification.md) for the exact wire format of custom messages.

## Next Steps

- [Ardulink-MQTT]({{ '/documentation/ardulink-mqtt/' | relative_url }}) — IoT broker integration
- [REST API]({{ '/documentation/ardulink-rest/' | relative_url }}) — HTTP/JSON control
- [Ardulink-2 Architecture]({{ '/documentation/architecture/' | relative_url }}) — Protocol internals
