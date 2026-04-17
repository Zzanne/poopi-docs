# Tank Level Processing

## Purpose
Process analog level sensor data into usable values, trends, and publish them via MQTT.

---

## Input

**MQTT Topic:**
sensors/septic/level


**Payload (JSON):**
```json
{
  "volts": float,
  "ma": float,
  "above_sensor_in": float,
  "total_depth_in": float
}

## Processing Pipeline

### 1. Smoothing
- Moving average (~5 samples)
- Reduces noise and jitter

---

### 2. Unit Conversion

**Constant:**
- ~42.56 gallons per inch

**Derived:**
- gallons = inches × constant

---

### 3. Derived Values
- Total depth (inches)
- Volume (gallons)
- Fill level (relative)

---

### 4. Trend Analysis

**Short-Term (~minutes):**
- Detect pump activity
- Rapid changes

**Long-Term (~hours):**
- Detect filling/draining behavior

**Outputs:**
- rising
- falling
- steady
- rate (gallons/hour)

## Outputs

### MQTT (Local)

**Topic:**
surge/tank/gallons


**Payload:**
- Float (gallons)

---

### MQTT (External)

**Adafruit IO Topics:**
zmcnair/feeds/bkh.surge-level
zmcnair/feeds/bkh.surge-inches

## Level Interpretation (Work in Progress)

| Gallons | Inches | Condition |
|--------|--------|----------|
| TBD    | TBD    | Normal idle level |
| TBD    | TBD    | Pump active (rapid drop) |
| TBD    | TBD    | Rising level (inflow) |
| TBD    | TBD    | High level / alert condition |

## Notes

- Values are smoothed before publishing
- External publishing is rate-limited
- Calibration depends on sensor accuracy and constants
