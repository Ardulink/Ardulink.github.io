---
layout: default
title: "Sample: Simple Smart Car Driver"
parent: Documentation
nav_order: 15
permalink: /documentation/sample-smart-car/
---

# Sample: Simple Smart Car Driver

This walkthrough covers the `example-simplesmartcardriver` sample from the [Ardulink-2-Samples](https://github.com/Ardulink/Ardulink-2-Samples) repository — a complete GUI application for controlling a robot car.

## What It Does

The Smart Car Driver provides a tabbed interface with:
- **Connection tab** — Select serial port, baud rate, and connect/disconnect
- **Control tab** — Directional buttons (Ahead, Left, Right, Back) with adjustable speed

## Running the Sample

1. Clone the samples repository:

```bash
git clone https://github.com/Ardulink/Ardulink-2-Samples.git
cd Ardulink-2-Samples
```

2. Run the sample:

```bash
./mvnw -pl example-simplesmartcardriver -am compile exec:java \
    -Dexec.mainClass="org.ardulink.gui.SimpleSmartCarDriver"
```

## How It Works

### Architecture

The application uses these Ardulink-Swing components:

```
SimpleSmartCarDriver (JFrame)
├── ConnectionPanel    — Transport/port configuration
├── ConnectionStatus   — Visual status indicator
├── SignalButton[4]    — Direction buttons (ahead/left/right/back)
└── Linkable interface — Auto-binding to Link
```

### Key Code Patterns

**Linkable pattern** — All UI components implement `Linkable` and receive the link when connected:

```java
private final List<Linkable> linkables = Lists.newArrayList();

public SimpleSmartCarDriver() {
    genericConnectionPanel = new ConnectionPanel();
    linkables.add(genericConnectionPanel);

    btnAhead = new SignalButton();
    btnAhead.setButtonText("Ahead");
    btnAhead.setId("ahead");
    btnAhead.setValue("100");
    linkables.add(btnAhead);
    // ... more buttons
}

public void setLink(Link link) {
    this.link = link;
    for (Linkable linkable : linkables) {
        linkable.setLink(link);
    }
}
```

**SignalButton configuration:**

```java
btnAhead = new SignalButton();
btnAhead.setButtonText("Ahead");
btnAhead.setId("ahead");        // Maps to Arduino pin/command
btnAhead.setValue("100");       // Default analog value (0-255)
btnAhead.setValueLabel("Strength");  // Label for value slider
btnAhead.setIcon(AHEAD_ICON);
```

**Connection management:**

```java
btnConnect.addActionListener(e -> {
    try {
        setLink(genericConnectionPanel.createLink());
    } catch (Exception ex) {
        JOptionPane.showMessageDialog(this, ex.getMessage(),
            "Error", ERROR_MESSAGE);
    }
});
```

### Direction Mapping

| Button | Direction | Signal |
|:-------|:----------|:-------|
| Ahead | Forward | Sends configured speed value |
| Left | Turn left | Sends configured speed value |
| Right | Turn right | Sends configured speed value |
| Back | Reverse | Sends configured speed value |

The `strength` slider on each button allows adjusting the speed in real-time.

## Building Your Own Car Controller

1. Start from this sample
2. Add more buttons or a joystick component
3. Map buttons to your motor driver's H-bridge pins
4. Add sensor displays (ultrasonic, infrared)

## Next Steps

- [Swing GUI Components]({{ '/documentation/ardulink-swing/' | relative_url }) — All available GUI components
- [Sample: Node-RED Dashboard]({{ '/documentation/sample-node-red/' | relative_url }) — Web-based alternative
- [Reading Pins]({{ '/documentation/reading-pins/' | relative_url }) — Add sensor feedback
