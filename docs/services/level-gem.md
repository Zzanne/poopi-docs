# level-gem.service

## Purpose
Measure septic tank level using a 4–20 mA sensor via ADS1115 and publish calibrated depth readings to MQTT.

---

## Execution

- **Script**: `/opt/poopi/app/level-gem2.py`
- **Type**: Continuous loop (systemd-managed)
- **Interval**: 1 second

### Behavior
- Reads analog signal from ADS1115
- Converts to voltage → current (mA) → depth (inches)
- Publishes continuously (not event-driven)
- Skips publish if sensor current is outside expected range (3–22 mA)

---

## Hardware

- **ADC**: ADS1115 (I2C, address `0x48`)
- **Shunt resistor**: 150 Ω
- **Gain setting**: ±4.096 V

---

## Inputs

- Analog signal from 4–20 mA level sensor
- I2C read via `smbus2`

---

## Processing

- Convert raw ADC value → voltage
- Convert voltage → current (mA)
- Apply calibration:
  - `MA_DRY = 3.8`
  - `INCHES_PER_MA = 5.01`
- Calculate:
  - Inches above sensor
  - Total depth (adds fixed hang height)

### Derived Values

- **Above sensor**:
  ```
  (mA - MA_DRY) * INCHES_PER_MA
  ```

- **Total depth**:
  ```
  above_sensor + HANG_HEIGHT
  ```

- **Clamp**:
  - Negative depth values forced to 0

- **Fault handling**:
  - Ignore readings outside 3–22 mA

---

## Outputs

### MQTT (Primary Output)

- **Broker**: `localhost:1883`
- **Topic**:
  ```
  sensors/septic/level
  ```

- **Publish rate**:
  - Every 1 second (continuous stream)

- **Payload**:
  ```json
  {
    "volts": float,
    "ma": float,
    "above_sensor_in": float,
    "total_depth_in": float
  }
  ```

- **Example**:
  ```json
  {
    "volts": 1.503,
    "ma": 10.02,
    "above_sensor_in": 31.15,
    "total_depth_in": 36.65
  }
  ```

---

### Stdout (journald)

- Prints formatted readings every cycle:
  ```
  volts | mA | inches above sensor | total depth
  ```

- Fault condition:
  ```
  Sensor current out of expected range
  ```

- View:
  ```
  journalctl -u level-gem.service
  ```

---

## Timing Characteristics

- Fixed interval: 1 second
- Continuous publishing (not event-based)
- Provides smooth time-series data for Grafana
- Dependent on ADC read + small delay (~100 ms conversion + 1 s sleep)

---

## Notes

- Acts as the **continuous analog telemetry source** for tank level
- Complements:
  - `chkgpio.service` (event-based discrete states)
  - `gpio-heartbeat.service` (periodic full-state snapshot)
- Uses local MQTT broker, likely feeding:
  - Node-RED
  - InfluxDB
  - Grafana
