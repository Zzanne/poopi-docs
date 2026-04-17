# Node-RED Overview

## Role

Node-RED acts as the **central processing and routing layer** for:

- GPIO state (via snapshot file)
- Analog level data (via MQTT)
- System telemetry
- External MQTT distribution

---

## Core Functions

1. Read GPIO snapshot file
2. Build system state (bitmask)
3. Process level sensor data
4. Perform smoothing and trend analysis
5. Route data to multiple MQTT endpoints
6. Provide dashboard visualization
7. Monitor system health (watchdog)

---

## Data Sources

| Source | Method |
|------|--------|
| GPIO snapshot | file read (`/run/gpio_snapshot.json`) |
| Level sensor | MQTT (`sensors/septic/level`) |
| CPU temp | local command |
| Enclosure temp | AHT20 sensor |

---

## Outputs

- MQTT (multiple brokers)
- Dashboard (UI)
- Debug/logging
