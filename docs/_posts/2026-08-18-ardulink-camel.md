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
                // When Arduino sends data on pin 2, log it
                from("ardulink:digital?link=serial&port=/dev/ttyACM0&pin=2")
                    .log("Pin 2 changed: ${body}");

                // Set pin 13 HIGH every 5 seconds
                from("timer:blink?period=5000")
                    .setBody(constant(true))
                    .to("ardulink:digital?link=serial&port=/dev/ttyACM0&pin=13");
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
from("ardulink:analog?link=serial&port=/dev/ttyACM0&pin=0")
    .convertBodyTo(String.class)
    .to("activemq:queue:sensor.readings");
```

### HTTP Webhook

Forward Arduino events to an HTTP endpoint:

```java
from("ardulink:digital?link=serial&port=/dev/ttyACM0&pin=2")
    .setHeader(Exchange.HTTP_METHOD, constant("POST"))
    .setHeader(Exchange.CONTENT_TYPE, constant("application/json"))
    .to("http://myserver.com/api/arduino/event");
```

### Filter and Transform

Process only specific pin values:

```java
from("ardulink:analog?link=serial&port=/dev/ttyACM0&pin=0")
    .filter(body().isGreaterThan(500))
    .log("High reading: ${body}")
    .to("mailto:alerts@example.com?subject=Sensor+Alert");
```

### MQTT Bridge via Camel

```java
from("ardulink:digital?link=serial&port=/dev/ttyACM0&pin=13")
    .to("paho:arduino/digital/13?brokerUrl=tcp://localhost:1883");

from("paho:commands/digital/13?brokerUrl=tcp://localhost:1883")
    .to("ardulink:digital?link=serial&port=/dev/ttyACM0&pin=13");
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
