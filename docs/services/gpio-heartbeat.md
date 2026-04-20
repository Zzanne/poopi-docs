# gpio-heartbeat.service

## Purpose
Generate a periodic snapshot of all GPIO states as a single JSON object for downstream consumers that require full-state visibility instead of edge-triggered events.

---

## Execution

- **Script**: `/opt/poopi/app/gpio_heartbeat.sh`
- **Type**: Continuous loop (systemd-managed)
- **Interval**: 5 seconds

### Behavior
- Captures **full GPIO state** at a fixed interval
- Writes snapshot atomically (temp file → rename)
- Independent of state changes (runs regardless of activity)

---

## Inputs

- **GPIO pins (BCM)**:
  - 17
  - 22
  - 5
  - 27
  - 26
  - 20
  - 19
  - 21

- **Read method**:
  - `pinctrl get <pin>`
  - Extracts `hi` / `lo` and converts to:
    - `1` = high
    - `0` = low

---

## Processing

- Poll all pins every 5 seconds
- Normalize values to binary (0/1)
- Build a single JSON object containing:
  - Timestamp (UTC)
  - Current state of all pins
- Write snapshot using atomic file replacement:
  - Write to temp file
  - Move to final path

---

## Outputs

### File (Primary Output)

- **Path**:
  ```
  /run/gpio_snapshot.json
  ```

- **Write method**:
  - Atomic (`.tmp` → rename)
  - Prevents partial reads by consumers

- **Format**:
  ```json
  {
    "ts": "ISO8601",
    "gpio17": 0,
    "gpio22": 1,
    "gpio5": 0,
    "gpio27": 0,
    "gpio26": 1,
    "gpio20": 1,
    "gpio19": 0,
    "gpio21": 0
  }
  ```

- **Timestamp format**:
  - UTC (`YYYY-MM-DDTHH:MM:SSZ`)

---

## Timing Characteristics

- Fixed interval: every 5 seconds
- Not event-driven
- Provides **state continuity** even if no changes occur
- Suitable for:
  - Polling systems
  - Recovery after missed events
  - Cross-checking event-based logs

---

## Relationship to chkgpio.service

- **chkgpio.service** → event-based (edge detection)
- **gpio-heartbeat.service** → state-based (full snapshot)

Together they provide:
- High-resolution event tracking
- Periodic full-state verification

---

## Notes

- No MQTT output; file-based only
- Consumers must read `/run/gpio_snapshot.json`
- `/run` is typically tmpfs (non-persistent across reboot)
