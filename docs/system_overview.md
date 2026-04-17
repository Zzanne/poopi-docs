# System Overview

## Description

Raspberry Pi–based septic monitoring appliance.

Monitors:
- Digital inputs (floats, pumps, alarms)
- Analog level sensor (4–20 mA via ADS1115)

Publishes:
- MQTT (events + level)
- Local files (logs + snapshots)

---

## Services

### gpio-heartbeat.service
- Periodic GPIO snapshot (5s)
- Output: /run/gpio_snapshot.json

### chkgpio.service
- GPIO change detection (50ms polling)
- Outputs:
  - MQTT events
  - local log file

### level-gem.service
- Analog level measurement (1s)
- Output:
  - MQTT JSON

---

## Outputs

### MQTT
- Digital events (per GPIO)
- System status
- Analog level data

### Files
- Snapshot: /run/gpio_snapshot.json
- Event log: /opt/poopi/app/gpio_events.log

## Node-RED Role

Node-RED acts as the central processing engine:

- Converts GPIO snapshots into system state
- Processes analog level data
- Performs smoothing and trend analysis
- Routes data to multiple MQTT brokers
- Provides dashboard visualization
- Implements watchdog recovery logic
