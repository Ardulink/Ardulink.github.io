---
layout: default
title: Setting Up the Arduino Firmware
parent: Documentation
nav_order: 4
permalink: /documentation/arduino-firmware/
---

# Setting Up the Arduino Firmware

Before using Ardulink-2 from Java, your Arduino board needs the Ardulink firmware. This firmware implements the Ardulink protocol, enabling communication between the Java library and the Arduino hardware.

## Quick Upload: WebSerial Flasher

The fastest way to flash the firmware is the browser-based [WebSerial Flasher](https://ardulink.github.io/Firmware/webflasher.html) — no software installation required.

1. Open the [WebSerial Flasher](https://ardulink.github.io/Firmware/webflasher.html) in **Chrome** or **Edge** (WebSerial support required)
2. Click **↻ Latest** to automatically fetch the latest firmware release
3. Click **Program**
4. When prompted, select your Arduino's serial port
5. Wait for the upload to complete — you'll see a success message

{: .note }
If the WebSerial Flasher doesn't work for your board, use one of the alternative methods below.

## Alternative: Pre-compiled HEX Files

Download ready-to-flash `.hex` files from the [Firmware Releases](https://github.com/Ardulink/Firmware/releases).

Available board variants:
- **Arduino Uno**
- **Arduino Nano**
- **Arduino Mega**

Upload using one of these tools:

| Tool | Platform | Notes |
|:-----|:---------|:------|
| [avrdude](https://github.com/avrdudes/avrdude) | macOS/Linux/Windows | Command-line, most reliable |
| [XLoader](http://rushemotto.com/xloader/) | Windows | Simple GUI |
| [Arduino IDE](https://www.arduino.cc/en/software/) | macOS/Linux/Windows | Via sketch import |

### Using avrdude

```bash
# For Arduino Uno
avrdude -p atmega328p -c arduino -P /dev/ttyACM0 -b 115200 -U flash:w:ArdulinkProtocol_uno.hex

# For Arduino Mega
avrdude -p atmega2560 -c wiring -P /dev/ttyACM0 -b 115200 -U flash:w:ArdulinkProtocol_mega.hex
```

{: .note }
Replace `/dev/ttyACM0` with your actual serial port. On macOS it's typically `/dev/cu.usbmodem*`, on Windows it's `COM3`, `COM4`, etc.

## Alternative: Build from Source

If you want to customize the firmware, build it from source:

1. Install the [Arduino IDE](https://www.arduino.cc/en/software/)
2. Clone the firmware repository:

   ```bash
   git clone https://github.com/Ardulink/Firmware.git
   ```

3. Open `Firmware/ArdulinkProtocol/ArdulinkProtocol.ino` in the Arduino IDE
4. Select your board under **Tools > Board**
5. Select your serial port under **Tools > Port**
6. Click **Upload**

## Firmware Variants

The firmware repository includes different variants in the `variants/` directory and pre-compiled in the `examples/` directory. These may include:

- **Standard Ardulink Protocol** — Default firmware for serial communication
- **NodeMCU variants** — For ESP8266-based boards with Lua scripting
- **Custom examples** — Demonstration sketches with additional functionality

## What the Firmware Does

Once uploaded, the firmware:

1. Listens for commands on the serial port (or other configured transport)
2. Parses the Ardulink protocol messages
3. Executes commands (set pins, read pins, play tones)
4. Sends responses and events back to the Java side

The protocol supports:
- Digital pin read/write
- Analog pin read/write
- PWM control
- Tone generation
- Custom message exchange
- Pin change subscriptions

## Protocol Specification

For full details on the communication protocol, see the [Ardulink2 Specification](https://github.com/Ardulink/Firmware/blob/main/Ardulink2-Specification.md).

## Firmware Version

The current firmware release is **v1.2.0** (February 2025).

## Troubleshooting

### Upload fails

- Try a different USB cable (some are charge-only)
- Make sure the correct board and port are selected
- On Linux, check serial port permissions: `sudo usermod -a -G dialout $USER`
- Press the reset button on the Arduino just before uploading

### Firmware uploads but Java can't connect

- Verify the firmware is the Ardulink protocol firmware (not a blank sketch)
- Check that the baud rate matches (default: 115200)
- Ensure no other application is using the serial port

### WebSerial Flasher not available

- WebSerial requires Chrome 89+ or Edge 89+
- Must be served over HTTPS (the GitHub Pages URL is fine)
- Try a different browser if issues persist

## Next Steps

- [Getting Started]({{ '/documentation/getting-started/' | relative_url }}) — Set up the Java side
- [Link Types]({{ '/documentation/link-types/' | relative_url }}) — All available transports
- [Ardulink-2 Architecture]({{ '/documentation/architecture/' | relative_url }}) — How the protocol works
