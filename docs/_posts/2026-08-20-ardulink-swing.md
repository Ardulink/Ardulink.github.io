---
layout: default
title: Building GUI Applications with Ardulink-Swing
parent: Documentation
nav_order: 14
permalink: /documentation/ardulink-swing/
description: Building desktop control applications
---

# Building GUI Applications with Ardulink-Swing

The `ardulink-swing` module provides pre-built Java Swing components for building desktop GUIs that control Arduino boards.

## Available Components

| Component | Description |
|:----------|:------------|
| `ConnectionPanel` | Connection settings (port, baud rate, transport) |
| `ConnectionStatus` | Visual connection status indicator |
| `SignalButton` | Button that sends analog/digital values to a pin |
| `Linkable` | Interface for components that bind to a Link |

## Maven Dependency

```xml
<dependency>
    <groupId>org.ardulink</groupId>
    <artifactId>ardulink-swing</artifactId>
    <version>2.2.0</version>
</dependency>
```

## Quick Example

```java
import javax.swing.*;
import java.awt.*;

import org.ardulink.core.Link;
import org.ardulink.core.pin.Pin;
import org.ardulink.gui.ConnectionPanel;
import org.ardulink.gui.ConnectionStatus;

public class SimpleGUI extends JFrame {

    public SimpleGUI() {
        setTitle("Ardulink Controller");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        // Connection panel with port/baud selection
        ConnectionPanel connectionPanel = new ConnectionPanel();
        add(connectionPanel, BorderLayout.NORTH);

        // LED toggle button
        JButton ledButton = new JButton("Toggle LED");
        ledButton.addActionListener(e -> {
            try {
                Link link = connectionPanel.createLink();
                link.switchDigitalPin(Pin.digitalPin(13), ledButton.isSelected());
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        });
        add(ledButton, BorderLayout.CENTER);

        // Status indicator
        ConnectionStatus status = new ConnectionStatus();
        add(status, BorderLayout.SOUTH);

        pack();
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new SimpleGUI().setVisible(true));
    }
}
```

## Linkable Interface

Components implementing `Linkable` automatically bind to a link:

```java
import org.ardulink.gui.Linkable;

public class MyComponent extends JPanel implements Linkable {
    private Link link;

    @Override
    public void setLink(Link link) {
        this.link = link;
        // React to link changes
    }
}
```

When `ConnectionPanel` establishes a connection, all `Linkable` components in the frame receive the link via `setLink()`.

## SignalButton

`SignalButton` is a specialized button that sends analog or digital values:

```java
import org.ardulink.gui.customcomponents.SignalButton;

SignalButton aheadBtn = new SignalButton();
aheadBtn.setButtonText("Forward");
aheadBtn.setId("ahead");
aheadBtn.setValue("100");         // Analog value to send
aheadBtn.setValueLabel("Speed");  // Label for value slider
aheadBtn.setIcon(new ImageIcon("arrow-up.png"));
```

When pressed, it sends the configured value to the Arduino pin mapped to its ID.

## Sample: See the Full Application

The [Smart Car Driver sample]({{ '/documentation/sample-smart-car/' | relative_url }}) demonstrates a complete GUI application using these components.

## Next Steps

- [Sample: Smart Car Driver]({{ '/documentation/sample-smart-car/' | relative_url }) — Full GUI walkthrough
- [Reading Pins]({{ '/documentation/reading-pins/' | relative_url }) — Event-driven input
- [Link Types]({{ '/documentation/link-types/' | relative_url }) — Connection options
