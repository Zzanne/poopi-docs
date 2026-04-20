# chkgpio.service

## Purpose
Detect GPIO state changes and publish events via MQTT while maintaining a durable local event log.

---

## Execution

- **Script**: `/opt/poopi/app/chkgpio.py`
- **Type**: Continuous loop (systemd-managed)
- **Poll interval**: 50 ms (~20 scans/sec)

### Behavior
- Monitors GPIO continuously
- **Triggers only on state change** (edge-based, not periodic)
- Publishes **online status on startup**
- Publishes **offline status via MQTT Last Will** if service stops unexpectedly

---

## Inputs

- **GPIO pins (BCM mode)**:
  - 17 → surge-pump-cycle
  - 22 → surge-ops-float
  - 5  → surge-override-float
  - 27 → surge-hwa
  - 26 → effluent-pump-cycle
  - 20 → effluent-ops-float
  - 19 → effluent-override-float
  - 21 → effluent-hwa

---

## Processing

- Compare current GPIO state against last known state
- Trigger event only on change
- Apply **logical inversion** for configured pins
- Convert to normalized binary output (0/1)

---

## Outputs

### MQTT (Primary Data Path)

- **Broker**: `io.adafruit.com:1883`
- **Topic format**:
  ```
  zmcnair/feeds/bkh.<label>
  ```

- **Example**:
  ```
  zmcnair/feeds/bkh.surge-ops-float
  ```

- **Payload**:
  ```
  0 or 1
  ```

- **Publish behavior**:
  - Event-driven (on change only)
  - Non-blocking (background MQTT loop)

---

### MQTT (Status Topic)

- **Topic**:
  ```
  zmcnair/feeds/bkh.chkgpio-status
  ```

- **Payloads**:
  - `1` → running
  - `0` → offline (Last Will)

- **Retained**: Yes

---

### File Logging (Local)

- **Path**:
  ```
  /opt/poopi/app/gpio_events.log
  ```

- **Format**:
  ```
  timestamp,pin,label,state
  ```

- **Example**:
  ```
  2026-04-20T15:02:11.123456+00:00,22,surge-ops-float,1
  ```

- **Behavior**:
  - Append-only
  - Immediate flush + fsync (write-through, no buffering loss)

- **Startup entry**:
  ```
  timestamp,SYSTEM,chkgpio_start,1
  ```

---

### Stdout (journald)

- Mirrors each event line:
  ```
  timestamp,pin,label,state
  ```

- View:
  ```
  journalctl -u chkgpio.service
  ```

---

## Timing Characteristics

- ~50 ms detection resolution
- No periodic heartbeat per signal
- Output occurs only on transitions
- Suitable for precise event sequencing (float changes, pump cycles)

---

## Notes

- Acts as the **primary event source** for downstream systems (MQTT → processing → Grafana)
- Downstream consumers must handle:
  - State persistence
  - Missed events if disconnected
