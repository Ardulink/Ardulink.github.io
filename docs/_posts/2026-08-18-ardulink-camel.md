---
layout: default
title: Apache Camel Integration with Ardulink
parent: Documentation
nav_order: 13
permalink: /documentation/ardulink-camel/
description: Enterprise messaging patterns
---

# Apache Camel Integration with Ardulink

The `ardulink-camel` module integrates Ardulink-2 with [Apache Camel](https://camel.apache.org/), enabling enterprise integration patterns with Arduino devices.

## What It Does

`ardulink-camel` provides a Camel component that lets you:
- Use Arduino pins as Camel message endpoints
- Route Arduino events to/from other systems (JMS, HTTP, files, etc.)
- Apply Camel's enterprise integration patterns to Arduino communication
- Bridge Arduino with enterprise service buses (ESB)

## Basic Setup

### Maven Dependency

```xml
<dependency>
    <groupId>org.ardulink</groupId>
    <artifactId>ardulink-camel</artifactId>
    <version>2.2.0</version>
</dependency>
```

### Simple Route

```java
import org.apache.camel.CamelContext;
import org.apache.camel.builder.RouteBuilder;
import org.apache.camel.impl.DefaultCamelContext;

public class ArdulinkCamelExample {
    public static void main(String[] args) throws Exception {
        CamelContext context = new DefaultCamelContext();

        context.addRoutes(new RouteBuilder() {
            @Override
            public void configure() throws Exception {
                // When Arduino sends data, log the ALP protocol message
                from("ardulink:serial?port=/dev/ttyACM0")
                    .log("Received: ${body}");

                // Set pin 13 HIGH every 5 seconds using ALP protocol
                from("timer:blink?period=5000")
                    .setBody(constant("alp://ppp/D13/1"))
                    .to("ardulink:serial?port=/dev/ttyACM0");
            }
        });

        context.start();
        Thread.sleep(Long.MAX_VALUE);
        context.stop();
    }
}
```

## Integration Patterns

### Bridge with Message Queue

Forward Arduino sensor data to a JMS/ActiveMQ queue:

```java
from("ardulink:serial?port=/dev/ttyACM0")
    .convertBodyTo(String.class)
    .to("activemq:queue:sensor.readings");
```

### HTTP Webhook

Forward Arduino events to an HTTP endpoint:

```java
from("ardulink:serial?port=/dev/ttyACM0")
    .setHeader(Exchange.HTTP_METHOD, constant("POST"))
    .setHeader(Exchange.CONTENT_TYPE, constant("application/json"))
    .to("http://myserver.com/api/arduino/event");
```

### Filter and Transform

Process specific ALP messages or values:

```java
from("ardulink:serial?port=/dev/ttyACM0")
    .filter(body().contains("A0"))
    .log("Analog reading message: ${body}")
    .to("mailto:alerts@example.com?subject=Sensor+Alert");
```

### MQTT Bridge via Camel

```java
from("ardulink:serial?port=/dev/ttyACM0")
    .to("paho:arduino/messages?brokerUrl=tcp://localhost:1883");

from("paho:commands?brokerUrl=tcp://localhost:1883")
    .to("ardulink:serial?port=/dev/ttyACM0");
```

## Use Cases

- **Enterprise integration** — Connect Arduino to existing Camel-based middleware
- **IoT gateways** — Bridge local Arduino networks to cloud services
- **Data pipelines** — Route sensor data through ETL processes
- **Monitoring** — Forward Arduino events to monitoring systems
- **Complex event processing** — Apply CEP rules to Arduino data streams

## Next Steps

- [Swing GUI]({{ '/documentation/ardulink-swing/' | relative_url }}) — Desktop applications
- [REST API]({{ '/documentation/ardulink-rest/' | relative_url }}) — Simpler HTTP alternative
- [Ardulink-MQTT]({{ '/documentation/ardulink-mqtt/' | relative_url }}) — IoT alternative
