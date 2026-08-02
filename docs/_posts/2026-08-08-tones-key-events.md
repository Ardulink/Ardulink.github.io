---
layout: default
title: Sending Tones and Key Events
parent: Documentation
nav_order: 9
permalink: /documentation/tones-key-events/
description: Playing sounds and simulating keyboard input
---

# Sending Tones and Key Events

Ardulink-2 can play tones through Arduino's piezo buzzer and simulate keyboard events — useful for audio feedback and HID-like interactions.

## Playing Tones

### Basic Tone

```java
import org.ardulink.core.Link;
import org.ardulink.core.pin.Pin;
import org.ardulink.core.pin.Pin.AnalogPin;
import org.ardulink.core.Tone;
import org.ardulink.core.convenience.Links;

public class ToneExample {
    public static void main(String... args) throws Exception {
        try (Link link = Links.getDefault()) {
            AnalogPin pin = Pin.analogPin(8);  // Piezo buzzer on pin 8

            // Play a 440Hz tone (A4) for 200ms
            Tone tone = new Tone(pin, 440, 200);
            link.sendTone(tone);

            Thread.sleep(300);

            // Play a different frequency
            link.sendTone(new Tone(pin, 880, 200));

            Thread.sleep(300);

            // Stop the tone
            link.sendNoTone(pin);
        }
    }
}
```

### Tone Parameters

The `Tone` class takes:
- **Pin** — The analog pin connected to the buzzer
- **Frequency** — Tone frequency in Hz (e.g., 440 for A4, 880 for A5)
- **Duration** — How long to play in milliseconds

### Common Frequencies

| Note | Hz | Note | Hz |
|:-----|:---|:-----|:---|
| C4 | 262 | C5 | 523 |
| D4 | 294 | D5 | 587 |
| E4 | 330 | E5 | 659 |
| F4 | 349 | F5 | 698 |
| G4 | 392 | G5 | 784 |
| A4 | 440 | A5 | 880 |
| B4 | 494 | B5 | 988 |

### Melody Example

```java
AnalogPin buzzer = Pin.analogPin(8);

int[] notes = {262, 294, 330, 349, 392, 440, 494, 523};
int[] durations = {200, 200, 200, 200, 200, 200, 200, 400};

for (int i = 0; i < notes.length; i++) {
    link.sendTone(new Tone(buzzer, notes[i], durations[i]));
    Thread.sleep(durations[i] + 50);
}

link.sendNoTone(buzzer);
```

## Sending Key Press Events

Ardulink-2 can simulate keyboard events on the Arduino side:

```java
import org.ardulink.core.Link;
import org.ardulink.core.convenience.Links;

public class KeyEventExample {
    public static void main(String... args) throws Exception {
        try (Link link = Links.getDefault()) {
            // Send key press: character, keycode, key location, modifiers, modifier extras
            link.sendKeyPressEvent('a', 65, 0, 0, 0);  // 'a' key
            Thread.sleep(100);
            link.sendKeyPressEvent('b', 66, 0, 0, 0);  // 'b' key
            Thread.sleep(100);

            // Ctrl+C (modifiers)
            link.sendKeyPressEvent('c', 67, 0, 2, 0);  // Ctrl=2
        }
    }
}
```

### Key Event Parameters

| Parameter | Description |
|:----------|:------------|
| `keychar` | The character value |
| `keycode` | Virtual key code |
| `keylocation` | Key location (0=standard, 3=numpad) |
| `keymodifiers` | Modifier mask (1=Shift, 2=Ctrl, 4=Alt) |
| `keymodifiersex` | Extended modifiers |

## Use Cases

- **Audio feedback** — Beep when a button is pressed, play melodies for alerts
- **HID emulation** — Simulate keyboard input to control connected displays or other devices
- **Interactive installations** — Sound and input in art/education projects
- **Game controllers** — Combine tones with button reads for interactive games

## Next Steps

- [Custom Messages]({{ '/documentation/custom-messages/' | relative_url }}) — Arbitrary data exchange
- [Reading Pins]({{ '/documentation/reading-pins/' | relative_url }}) — Input event handling
- [Sample: Smart Car Driver]({{ '/documentation/sample-smart-car/' | relative_url }}) — Full GUI example
