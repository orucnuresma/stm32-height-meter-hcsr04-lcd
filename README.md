# STM32 Height Meter (HC-SR04 + 16x2 LCD)

A simple **height measurement system** implemented on **STM32F031K6** using:
- **HC-SR04 ultrasonic sensor** for distance measurement
- **16x2 LCD (HD44780, 4-bit mode)** for display output

The system measures the distance from the sensor to the object (head), then computes height as:

**height_cm = SENSOR_HEIGHT_CM - measured_distance_cm**

Multiple samples are taken each cycle and the minimum distance is used for stability.

---

## Features

- Ultrasonic distance measurement using HC-SR04
- Microsecond delay + pulse measurement with **TIM3 (1 MHz)**
- 4-bit LCD driver (RS/EN/D4-D7)
- Multi-sampling per update cycle (default: 5 samples)
- Floor echo filtering using a margin threshold
- Live height display on 16x2 LCD

---

## Hardware

### MCU
- STM32F031K6 (STM32CubeIDE project)

### Sensor
- HC-SR04
  - TRIG: output from MCU
  - ECHO: input to MCU

### Display
- 16x2 LCD (HD44780 compatible), 4-bit mode

---

## Pin Mapping

### HC-SR04
- **TRIG** -> `PA6` (GPIO Output)
- **ECHO** -> `PA5` (GPIO Input)

### LCD (4-bit)
Defined in `Core/Inc/lcd.h`:
- **RS** -> `PF0`
- **EN** -> `PB1`
- **D4** -> `PA0`
- **D5** -> `PA1`
- **D6** -> `PA2`
- **D7** -> `PA3`

---

## How It Works

1. MCU sends a ≥10 µs pulse on TRIG.
2. HC-SR04 sets ECHO high for a duration proportional to distance.
3. TIM3 counts microseconds while ECHO is high.
4. Distance is computed using:
   - `distance_cm = echo_us / 58`
5. Height is computed as:
   - `height_cm = SENSOR_HEIGHT_CM - distance_cm`
6. LCD displays: `Boyunuz: XXX cm`

---

## Configuration

These constants are defined in `main.c`:

- `SENSOR_HEIGHT_CM`  
  Height of the sensor above the floor (cm).  
- `FLOOR_MARGIN_CM`  
  Small echo distances below this margin are ignored.
- `SAMPLES`  
  Number of measurements taken per cycle (minimum distance is used).

Example logic:
- If measurement is valid and not a floor echo:
  - `height_cm = SENSOR_HEIGHT_CM - min_distance_cm`
- Otherwise:
  - height is shown as `0 cm` (can be improved)

---

## Build & Flash (STM32CubeIDE)

1. Open the project folder in **STM32CubeIDE**
2. Ensure the `.ioc` file is recognized
3. Build the project
4. Flash to the board using ST-LINK

---

## Files

- `Core/Src/main.c`  
  Main loop + HC-SR04 measurement + height calculation + LCD updates
- `Core/Src/lcd.c` and `Core/Inc/lcd.h`  
  LCD driver (4-bit mode)

---

## Possible Improvements

- Show "No object" instead of `0 cm` when no valid echo is detected
- Add moving average filtering
- Add calibration mode for `SENSOR_HEIGHT_CM`
- Add buzzer/LED indication for valid measurement

---

## Author

Esmanur Oruç
