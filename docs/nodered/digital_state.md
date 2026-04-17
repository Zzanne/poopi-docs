# Digital State Processing

## Purpose
Convert raw GPIO snapshot data into a structured system state and distribute it via MQTT.

---

## Input

Source:
/run/gpio_snapshot.json


- Updated every 5 seconds by `gpio-heartbeat`
- Contains full GPIO state snapshot

---

## Processing Steps

### 1. Parse JSON
Extract:
- gpio17
- gpio22
- gpio5
- etc.

---

### 2. Build System State Bitmask

Each input is mapped to a bit position:

| Bit | Signal |
|-----|--------|
| 7 | surge pump |
| 6 | surge high water |
| 5 | surge ops float |
| 4 | surge override |
| 3 | effluent pump |
| 2 | effluent high water |
| 1 | effluent ops float |
| 0 | effluent override |

Outputs:
- Decimal value
- Binary string
- Hex representation

---

### 3. Change Detection (RBE)

- Prevents duplicate messages
- Only forwards when state changes

---

## Outputs

### MQTT (Local)
**Topic:**
system/state


**Payload:**
- Integer bitmask
- Each bit represents a specific float or pump state

---

## Inversion Strategy

GPIO values are read as raw electrical states from:
/run/gpio_snapshot.json
These values are not modified by upstream scripts.

---

### Logical Interpretation

Some inputs are wired as active-low and require inversion to reflect system meaning.

Inversion is applied in Node-RED prior to bitmask construction.

---

### Example

| Signal | GPIO | Raw | Logical |
|--------|------|-----|--------|
| Effluent Ops Float | GPIO20 | 1 | 0 |

---

## Bitmask Mapping

The system state is encoded as an 8-bit mask where each bit represents a GPIO input.

| Bit | Weight | GPIO | Signal |
|-----|--------|------|--------|
| 7   | 128    | 17   | Surge Pump |
| 6   | 64     | 27   | Surge High Water |
| 5   | 32     | 22   | Surge Ops Float |
| 4   | 16     | 5    | Surge Override |
| 3   | 8      | 26   | Effluent Pump |
| 2   | 4      | 21   | Effluent High Water |
| 1   | 2      | 20   | Effluent Ops Float |
| 0   | 1      | 19   | Effluent Override |

---

## Bitmask Calculation

Each active signal contributes its weight to the total mask value.

---

## Notes

- Bit positions are fixed and must match Node-RED implementation
- Interpretation depends on correct pin polarity (inversion settings)
- This table should be updated as system behavior is validated
- Raw values represent electrical state only and should not be used directly for interpretation
- Logical values represent system behavior
- All system state calculations use logical (post-inversion) values
- System behavior should be interpreted using combinations of signals rather than individual bits
