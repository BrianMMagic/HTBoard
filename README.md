# HT Board

A web app to control a Huger Tech electric skateboard over Bluetooth, since the official app is gone.

## Use it

Open the GitHub Pages link for this repo in:

- **Android:** Chrome
- **Computer:** Chrome or Edge
- **iPhone:** the free Bluefy browser (Safari does not support Bluetooth)

Turn the board on, tap **Connect to board**, and pick the device named `BLE Device-...`.

## What it does

- Acceleration from 0 to 100%, with 5 presets (hold a preset to save the slider value to it)
- Board light color (off plus 7 colors)
- Horn sound (9 choices)
- Direction lights
- Live speed, battery, trip, odometer, and top speed

The board never reports its current settings, so the page remembers them in the browser. Use **Send all saved settings to board** if they get out of sync.

## Protocol

Taken from the original Android app (`com.idt.escooter` version 1.1.0).

| Item | UUID |
| --- | --- |
| Service | `0xFF12` |
| Write | `0xFF01` |
| Notify | `0xFF02` |

Device name starts with `BLE Device-`.

### Command (20 bytes)

```
AA 55 03 00 SS LL LL AA DD 00 00 00 00 00 00 00 85 14 00 FE
```

| Byte | Meaning |
| --- | --- |
| 4 | Horn sound, 0 to 8 |
| 5, 6 | Board light, 0 off, 1 red, 2 green, 3 yellow, 4 blue, 5 magenta, 6 cyan, 7 white |
| 7 | Acceleration, slider percent divided by 2 (0 to 50) |
| 8 | Direction light, 0 off, 1 left, 2 left top, 3 forward, 4 right top, 5 right, 6 back |

Every command sends all settings at once. The original app only set byte 8 when you tapped a direction, so any other command turns direction lights off.

### Data (20 bytes, notify)

If byte 16 is `A9`:

- bytes 4 and 5 (little endian): speed in km/h times 100
- bytes 6 to 9: odometer in km times 100 (high word in 6 and 7, low word in 8 and 9, each little endian)
- bytes 2 and 3: raw battery voltage

Otherwise:

- bytes 2 to 5: trip distance in km times 100 (same word layout)
- byte 12: battery percent
