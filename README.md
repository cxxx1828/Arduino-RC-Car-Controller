# RC Car Controller – Custom PCB + Arduino Nano Firmware

A complete open-hardware/firmware solution to turn a cheap brushed or brushless (ESC-equipped) RC car chassis into a robust, button-controlled platform. Perfect for learning embedded systems, as a base for autonomous robot projects, or just for having fun with a tank-style wired controller.


## Features

- **Four tactile push-buttons** for intuitive control  
  - SW1 → Increase speed  
  - SW2 → Decrease speed / Brake (hold for emergency stop)  
  - SW3 → Steer left  
  - SW4 → Steer right  
- Arduino Nano as the brain (ATMega328P)
- On-board **LM7805** 5 V regulator (accepts 7–18 V input, 12 V nominal from barrel jack)
- Proper **BLDC/ESC** control outputs  
  - PWM speed → D3 (Timer2)  
  - Direction → D4  
  - Brake → D5  
- Standard **RC servo** output on D8 for steering
- Software-debounced buttons with rising-edge detection
- Configurable steering limits (easily changed in code)
- Safety features  
  - Emergency stop when SW2 is held >1 s  
  - Automatic brake when speed = 0  
  - Timeout kill-switch if no button pressed for 30 s (configurable)
- Real-time serial monitor output (115200 baud) for debugging and telemetry
- Expansion header (unsoldered) with +5 V, GND, A0–A7, D9–D13 for ultrasonic sensors, line-following, etc.

## Schematic & PCB

Designed in KiCad 7/8.

- `kicad/rc_car_controller.kicad_pro` – main project  
- `kicad/pcb/` – Gerber files ready for JLCPCB/LPKF/etc.  
- `kicad/schematic.pdf` – printable schematic

The board is **single-sided 100 × 50 mm** (fits perfectly in the classic Tamiya/TT-02 style chassis battery compartment).

## Bill of Materials (BOM)

| Qty | Designator       | Part                          | Notes                  |
|-----|------------------|-------------------------------|------------------------|
| 1   | U1               | Arduino Nano (old bootloader) | Any clone works        |
| 1   | U2               | LM7805 + heatsink             | TO-220                 |
| 4   | SW1–SW4          | 12×12 mm tactile button       | 6×6 also fits          |
| 1   | J1               | DC barrel jack 5.5×2.1 mm     |                        |
| 1   | J2               | 3-pin 2.54 mm for ESC         | PWM, DIR, BRK          |
| 1   | J3               | 3-pin servo header            | Signal on D8           |
| 2   | C1, C2           | 100 nF ceramic                | Decoupling             |
| 2   | C3, C4           | 47 µF / 25 V electrolytic     | Input/output of 7805   |
| 1   | D1               | 1N4007                        | Reverse polarity       |
| …   | LEDs, resistors  | 1 kΩ–10 kΩ as per schematic   |                        |

Full interactive BOM exported in `bom/ibom.html`.

## Firmware

Located in `firmware/rc_car_controller.ino`

### How to flash
1. Plug the Arduino Nano via USB
2. Open with Arduino IDE 2.x
3. Board → “Arduino Nano”, Processor → “ATmega328P (Old Bootloader)” if needed
4. Upload!

### Tuning parameters (top of the sketch)

```cpp
#define MAX_SPEED_PWM      200     // 0-255
#define SPEED_STEP         15      // How much speed changes per button press
#define STEERING_CENTER    90      // Servo middle position in degrees
#define STEERING_MAX_LEFT  45      // Max left angle
#define STEERING_MAX_RIGHT 135     // Max right angle
#define BUTTON_DEBOUNCE_MS 20
#define EMERGENCY_BRAKE_HOLD_MS 1000
```

## Button Behavior Summary

| Button | Short press          | Long press (>1 s)   |
|--------|----------------------|---------------------|
| SW1    | Speed +              | –                   |
| SW2    | Speed –              | Emergency stop + brake |
| SW3    | Steer left (while held) | –                |
| SW4    | Steer right (while held) | –               |

Releasing SW3/SW4 returns servo to center (spring-loaded steering feel).

## Serial Monitor Output (115200 baud)

```
Speed: 120/255  Dir: FWD  Steering: 92°  [RUNNING]
```

Great for live debugging or feeding data to a second microcontroller (e.g. Raspberry Pi for autonomy).

## Future Expansion Ideas

- Add HC-SR04 ultrasonic on pins A4/A5 → obstacle avoidance
- Add I2C OLED on expansion header → on-board dashboard
- Replace buttons with 2-axis joystick (ADC reading)
- Add nRF24L01 for wireless control
- Port to ESP32 for WiFi/Bluetooth control

## License

This project is released under the **CERN-OHL-S v2** (hardware) and **MIT License** (firmware/documentation).  
Do whatever you want with it – modify, sell, use in classes – just keep the attribution if you redistribute the design files.


## Author
Nina Dragicevic
