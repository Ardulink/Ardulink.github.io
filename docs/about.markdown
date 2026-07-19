---
layout: default
title: About
nav_order: 4
permalink: /about/
---

# About Ardulink-2

Ardulink-2 is the evolution of the original [Ardulink](http://ardulink.org/) project — a complete, open source, Java solution for the control and coordination of Arduino boards.

## History

The original Ardulink project provided a Java-to-Arduino communication framework. Ardulink-2 was rebuilt from the ground up with a modular architecture, separating the core protocol from transport-specific link adapters.

### Release History

| Version | Name | Date | Highlights |
|:--------|:-----|:-----|:-----------|
| **2.2.0** | *(current)* | November 2024 | Firmata support, REST API with Swagger UI, Java 8 minimum |
| **2.1.1** | Gordio SP1 | May 2016 | Bug fixes |
| **2.1.0** | Gordio | January 2016 | Camel reorganization, MQTT separatedTopics, NodeMCU support |
| **2.0.1** | Phoenix SP1 | November 2015 | Bug fixes, additional example |
| **2.0.0** | Phoenix | October 2015 | First Ardulink-2 release, Maven Central artifacts |

## Architecture

Ardulink-2 is built as a modular Maven project. The core provides the `Link` interface and pin abstraction, while link adapters implement specific transport protocols.

```
ardulink-core-base          -- Link interface, Pin model, events
ardulink-core-beans         -- Bean-based configuration
ardulink-core-util          -- Utility classes
ardulink-core-firmata-proto -- Firmata protocol support

ardulink-link-serial-jssc   -- Serial via JSSC
ardulink-link-serial-nrrxtx -- Serial via NRSerialRxTx
ardulink-link-serial-rxtx   -- Serial via Rxtx
ardulink-link-bluetooth     -- Bluetooth
ardulink-link-mqtt          -- MQTT
ardulink-link-nodemcu       -- NodeMCU/ESP8266
ardulink-link-raspberry     -- Raspberry Pi GPIO
ardulink-link-digispark     -- Digispark
ardulink-link-proxy         -- Network proxy
ardulink-link-virtual       -- Virtual (testing)

ardulink-mqtt               -- MQTT bridge (standalone jar)
ardulink-rest               -- REST API server
ardulink-camel              -- Apache Camel component
ardulink-swing              -- Swing GUI components
ardulink-mail               -- Email integration
ardulink-console            -- Interactive console
```

## License

Ardulink-2 is released under the [Apache License 2.0](https://github.com/Ardulink/Ardulink-2/blob/master/LICENSE).

## Contributing

Contributions are welcome! Visit the [Ardulink-2 GitHub repository](https://github.com/Ardulink/Ardulink-2) to report issues, suggest features, or submit pull requests.

## Links

- [GitHub Repository](https://github.com/Ardulink/Ardulink-2)
- [Maven Central](https://search.maven.org/#search%7Cga%7C1%7Cg%3A%22org.ardulink%22)
- [Sample Projects](https://github.com/Ardulink/Ardulink-2-Samples)
- [Firmware](https://github.com/Ardulink/Firmware)
