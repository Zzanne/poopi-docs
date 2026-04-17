# gpio-heartbeat.service

## Purpose
Generate periodic snapshot of all GPIO states.

## Execution
- Script: /opt/poopi/app/gpio_heartbeat.sh
- Interval: 5 seconds
- Output: /run/gpio_snapshot.json

## Inputs
- GPIO pins: 17, 22, 5, 27, 26, 20, 19, 21

## Output Format
```json
{
  "ts": "ISO8601",
  "gpio17": 0,
  ...
}
