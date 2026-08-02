---
layout: default
title: "Advanced: Understanding the Ardulink-2 Module Architecture"
parent: Documentation
nav_order: 17
permalink: /documentation/architecture/
description: Module system, internals, custom links
---

# Advanced: Understanding the Ardulink-2 Module Architecture

This post covers the internal architecture of Ardulink-2 — the module system, link discovery mechanism, and how to extend the framework.

## Maven Multi-Module Structure

Ardulink-2 is built as a Maven multi-module project. Each module has a specific role:

```
ardulink-2/
├── ardulink-core-base          Core: Link interface, Pin model, events
├── ardulink-core-beans         Bean-based configuration utilities
├── ardulink-core-util          Shared utility classes
├── ardulink-core-firmata-proto Firmata protocol implementation
│
├── ardulink-link-serial-jssc   Serial transport (JSSC)
├── ardulink-link-serial-nrrxtx Serial transport (NRSerialRxTx)
├── ardulink-link-serial-rxtx   Serial transport (Rxtx)
├── ardulink-link-bluetooth     Bluetooth transport
├── ardulink-link-mqtt          MQTT transport
├── ardulink-link-nodemcu       NodeMCU/ESP8266 transport
├── ardulink-link-raspberry     Raspberry Pi GPIO
├── ardulink-link-digispark     Digispark transport
├── ardulink-link-proxy         Network proxy transport
├── ardulink-link-virtual       Virtual (testing) transport
│
├── ardulink-mqtt               Standalone MQTT bridge
├── ardulink-rest               REST API server
├── ardulink-camel              Apache Camel component
├── ardulink-swing              Swing GUI components
├── ardulink-mail               Email notifications
└── ardulink-console            Interactive console
```

## The Link Interface

The core abstraction is `org.ardulink.core.Link`:

```java
public interface Link extends Closeable {
    Link addListener(EventListener listener) throws IOException;
    Link removeListener(EventListener listener) throws IOException;
    Link addRplyListener(RplyListener listener) throws IOException;
    Link removeRplyListener(RplyListener listener) throws IOException;
    Link addCustomListener(CustomListener listener) throws IOException;
    Link removeCustomListener(CustomListener listener) throws IOException;
    long startListening(Pin pin) throws IOException;
    long stopListening(Pin pin) throws IOException;
    long switchAnalogPin(AnalogPin analogPin, int value) throws IOException;
    long switchDigitalPin(DigitalPin digitalPin, boolean value) throws IOException;
    long sendKeyPressEvent(char keychar, int keycode, int keylocation,
            int keymodifiers, int keymodifiersex) throws IOException;
    long sendTone(Tone tone) throws IOException;
    long sendNoTone(AnalogPin analogPin) throws IOException;
    long sendCustomMessage(String... messages) throws IOException;
}
```

Every link adapter implements this interface. The return values are message IDs used for correlation.

## Link Discovery and Factory Pattern

Ardulink uses a convenience layer for link creation:

```java
Links.getDefault();                           // auto-detect first link
Links.getLink("ardulink://serial?port=...");  // specific URI
```

How it works:
1. `Links` uses `LinkManager` to scan the classpath for available link adapters
2. Each link adapter registers its URI scheme (e.g., `ardulink://serial`, `ardulink://bluetooth`)
3. `getDefault()` returns the first available link (serial preferred)
4. `getLink(uri)` creates a link for the specific connection string

### Connection URL Format

```
ardulink://<transport>?<param1>=<value1>&<param2>=<value2>
```

Examples:
- `ardulink://serial?port=/dev/ttyACM0&baudrate=115200`
- `ardulink://bluetooth?address=00:11:22:33:44:55`
- `ardulink://mqtt?broker=tcp://localhost:1883&topic=arduino1`
- `ardulink://virtual-console`
- `ardulink://virtual-random`

## Event System

The event system supports three listener types:

| Interface | Event Type | Description |
|:----------|:-----------|:------------|
| `EventListener` | `PinChangedEvent` | Pin state changes |
| `RplyListener` | `RplyEvent` | Reply/acknowledgment messages |
| `CustomListener` | `CustomEvent` | Custom string messages |

Events flow from Arduino → Link adapter → Java listeners.

## ConnectionBasedLink

The `ConnectionBasedLink` class extends `Link` with connection lifecycle management:

```java
public abstract class ConnectionBasedLink implements Link {
    // Connection management
    void addConnectionListener(ConnectionListener listener);
    void removeConnectionListener(ConnectionListener listener);

    // Connection state
    boolean isConnected();
    void disconnect();
}
```

Connection listeners receive `reconnected()` and `connectionLost()` callbacks.

## Extending with Custom Link Adapters

To create a custom transport:

1. Implement `Link` (or extend `ConnectionBasedLink`)
2. Implement the transport-specific connection logic
3. Register as a `LinkFactory` implementation
4. Package as a Maven module

```java
public class MyCustomLink extends ConnectionBasedLink {
    @Override
    public long switchDigitalPin(DigitalPin pin, boolean value) throws IOException {
        // Send command over your transport
        byte[] message = encodeCommand(pin, value);
        transport.send(message);
        return messageId;
    }

    // ... implement other Link methods
}
```

## Firmata Protocol Support

Version 2.2.0 introduces experimental [Firmata protocol](https://github.com/firmata/protocol) support via `ardulink-core-firmata-proto`. Enable it by setting a system property:

```java
System.setProperty("protocol.firmata.enabled", "true");
```

See [Firmata Protocol Support]({{ '/documentation/firmata/' | relative_url }}) for details.

## Future Direction

The 2.2.0 release notes indicate:
- **Next version will require Java 11** (2.2.0 is the last Java 8 release)
- Network proxy server may be removed (MQTT covers the same use case)
- Firmata protocol support will be expanded

## Next Steps

- [Firmata Protocol Support]({{ '/documentation/firmata/' | relative_url }) — Experimental Firmata
- [Link Types]({{ '/documentation/link-types/' | relative_url }) — All available transports
- [Custom Messages]({{ '/documentation/custom-messages/' | relative_url }) — Protocol details
