# level-gem.service

## Purpose
Measure septic level using 4–20mA sensor via ADS1115.

## Execution
- Script: /opt/poopi/app/level-gem2.py
- Interval: 1 second

## Hardware
- ADS1115 (I2C)
- 150 ohm shunt resistor

## Processing
- ADC → voltage → mA → inches
- Applies calibration constants

## Output

### MQTT
Topic: sensors/septic/level

Payload:
```json
{
  "volts": float,
  "ma": float,
  "above_sensor_in": float,
  "total_depth_in": float
}
