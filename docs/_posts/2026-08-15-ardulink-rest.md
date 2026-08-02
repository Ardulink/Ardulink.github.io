---
layout: default
title: "Controlling Arduino via REST API"
parent: Documentation
nav_order: 12
permalink: /documentation/ardulink-rest/
description: HTTP/JSON control with Swagger UI
---

# Controlling Arduino via REST API

The `ardulink-rest` module exposes your Arduino as a REST API, letting you control pins and read values using HTTP/JSON. It includes a built-in Swagger UI so you can interact with your Arduino directly from a browser.

## What It Does

`ardulink-rest` is a standalone server that:
1. Connects to your Arduino via any Ardulink link
2. Exposes REST endpoints for pin control
3. Serves a Swagger UI for interactive exploration
4. Supports full CRUD operations on digital and analog pins

```
┌──────────┐  Serial   ┌──────────────┐  REST/HTTP  ┌──────────────┐
│  Arduino  │ <───────> │ ardulink-rest│ <─────────> │   Browser /  │
│  (FW)     │           │ (server)     │             │   curl / API │
└──────────┘           └──────────────┘             └──────────────┘
```

## Quick Start

### 1. Start the REST Server

```bash
java -jar ardulink-rest-2.2.0.jar \
    -connection "ardulink://serial?port=/dev/ttyACM0"
```

For testing:

```bash
java -jar ardulink-rest-2.2.0.jar \
    -connection "ardulink://virtual-console"
```

### 2. Open Swagger UI

Navigate to [http://localhost:8080](http://localhost:8080) in your browser. You'll see the Swagger UI with all available endpoints.

### 3. Control Arduino from the Browser

Use the interactive Swagger UI to:
- List available pins
- Read digital/analog pin values
- Switch digital pins on/off
- Set analog pin values
- Send custom messages

## REST API Endpoints

| Method | Endpoint | Description |
|:-------|:---------|:------------|
| `GET` | `/api/pins` | List all available pins |
| `GET` | `/api/digital/{pin}` | Read digital pin value |
| `PUT` | `/api/digital/{pin}` | Set digital pin value |
| `GET` | `/api/analog/{pin}` | Read analog pin value |
| `PUT` | `/api/analog/{pin}` | Set analog pin value |
| `POST` | `/api/custom` | Send custom message |

## Using with curl

### Set Digital Pin HIGH

```bash
curl -X PUT http://localhost:8080/api/digital/13 \
    -H "Content-Type: application/json" \
    -d '{"value": true}'
```

### Read Analog Pin

```bash
curl http://localhost:8080/api/analog/0
```

### Set Analog Pin Value

```bash
curl -X PUT http://localhost:8080/api/analog/0 \
    -H "Content-Type: application/json" \
    -d '{"value": 512}'
```

## Maven Dependency

For programmatic REST server setup:

```xml
<dependency>
    <groupId>org.ardulink</groupId>
    <artifactId>ardulink-rest</artifactId>
    <version>2.2.0</version>
</dependency>
```

## Use Cases

- **Web dashboards** — Build HTML/JS dashboards that control Arduino
- **Mobile apps** — REST APIs work from any HTTP client
- **Testing** — Quick pin testing from the browser
- **Integration** — Connect Arduino to any REST-compatible system
- **Microservices** — Arduino as a REST endpoint in a larger architecture

## Next Steps

- [Apache Camel Integration]({{ '/documentation/ardulink-camel/' | relative_url }}) — Enterprise messaging
- [Ardulink-MQTT]({{ '/documentation/ardulink-mqtt/' | relative_url }}) — IoT broker alternative
- [Custom Messages]({{ '/documentation/custom-messages/' | relative_url }}) — Build your own protocols
