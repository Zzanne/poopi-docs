# MQTT Topics

## Digital (GPIO Events)

zmcnair/feeds/bkh.<label>  
- payload: 0 or 1

---

## Status

zmcnair/feeds/bkh.chkgpio-status  
- payload:  
  - 1 = online  
  - 0 = offline  

---

## Analog (Level Sensor)

sensors/septic/level  
- payload (JSON):

```json
{
  "volts": float,
  "ma": float,
  "above_sensor_in": float,
  "total_depth_in": float
}
