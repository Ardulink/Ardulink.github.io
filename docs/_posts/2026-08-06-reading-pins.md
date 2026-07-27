---
layout: default
title: Reading Digital and Analog Pins
parent: Documentation
nav_order: 8
permalink: /documentation/reading-pins/
---

# Reading Digital and Analog Pins

Ardulink-2 supports event-driven communication with Arduino. Instead of polling pins, you can subscribe to changes and react when they happen.

## Listening for Pin Changes

```java
import org.ardulink.core.Link;
import org.ardulink.core.pin.Pin;
import org.ardulink.core.pin.Pin.DigitalPin;
import org.ardulink.core.pin.Pin.AnalogPin;
import org.ardulink.core.events.PinChangedEvent;
import org.ardulink.core.events.FilteredEventListenerAdapter;
import org.ardulink.core.convenience.Links;

public class PinReader {
    public static void main(String... args) throws Exception {
        try (Link link = Links.getDefault()) {

            // Digital pin 2 (e.g., a button)
            DigitalPin buttonPin = Pin.digitalPin(2);
            link.addListener(FilteredEventListenerAdapter.filter(
                PinChangedEvent.class, buttonPin,
                e -> System.out.printf("Button: %s%n", e.getValue())
            ));
            link.startListening(buttonPin);

            // Analog pin A0 (e.g., a potentiometer)
            AnalogPin sensorPin = Pin.analogPin(0);
            link.addListener(FilteredEventListenerAdapter.filter(
                PinChangedEvent.class, sensorPin,
                e -> System.out.printf("Sensor: %s%n", e.getValue())
            ));
            link.startListening(sensorPin);

            System.out.println("Listening for pin changes...");

            // Keep running
            Thread.sleep(Long.MAX_VALUE);
        }
    }
}
```

## How It Works

1. **`FilteredEventListenerAdapter.filter(...)`** — Creates a listener that only fires for changes on the specified pin
2. **`link.addListener(...)`** — Registers the filtered listener
3. **`link.startListening(pin)`** — Tells the Arduino firmware to report changes on this pin
3. The Arduino firmware monitors the pin and sends a message when it changes
4. The Java side receives the message and fires the event callback

## Pin Types

### Digital Pins

```java
DigitalPin pin = Pin.digitalPin(13);  // Digital pin 13
link.startListening(pin);
```

Digital values are `boolean` — `true` (HIGH) or `false` (LOW).

### Analog Pins

```java
AnalogPin pin = Pin.analogPin(0);  // Analog pin A0
link.startListening(pin);
```

Analog values are `int` — 0 to 1023 (10-bit resolution).

## Stopping Listeners

```java
// Stop listening to a specific pin
link.stopListening(Pin.digitalPin(2));

// Remove the event listener
link.removeListener(myListener);
```

## Practical Example: Button + LED

```java
import org.ardulink.core.Link;
import org.ardulink.core.pin.Pin;
import org.ardulink.core.pin.Pin.DigitalPin;
import org.ardulink.core.events.PinChangedEvent;
import org.ardulink.core.events.FilteredEventListenerAdapter;
import org.ardulink.core.convenience.Links;

public class ButtonLED {
    public static void main(String... args) throws Exception {
        try (Link link = Links.getDefault()) {
            DigitalPin led = Pin.digitalPin(13);
            DigitalPin button = Pin.digitalPin(2);

            // When button changes, toggle LED — filtered to pin 2 only
            link.addListener(FilteredEventListenerAdapter.filter(
                PinChangedEvent.class, button,
                e -> {
                    boolean pressed = Boolean.TRUE.equals(e.getValue());
                    link.switchDigitalPin(led, pressed);
                    System.out.println(pressed ? "LED ON" : "LED OFF");
                }
            ));

            link.startListening(button);
            System.out.println("Press the button to toggle LED...");
            Thread.sleep(Long.MAX_VALUE);
        }
    }
}
```

## Reading Current Pin State

To read the current value of a pin without waiting for changes, you can send a custom message request. See [Custom Messages]({{ '/documentation/custom-messages/' | relative_url }}) for the protocol details.

## Next Steps

- [Custom Messages]({{ '/documentation/custom-messages/' | relative_url }) — Send arbitrary data
- [Tones and Key Events]({{ '/documentation/tones-key-events/' | relative_url }) — Sound and keyboard simulation
- [Sample: Smart Car Driver]({{ '/documentation/sample-smart-car/' | relative_url }) — GUI control example
