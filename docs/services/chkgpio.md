# chkgpio.service

## Purpose
Detect GPIO state changes and publish events via MQTT.

## Execution
- Script: /opt/poopi/app/chkgpio.py
- Poll interval: 50 ms

## Inputs
- GPIO pins (BCM)

## Processing
- Compare against last state
- Trigger on change only
- Apply inversion where configured

## Outputs

### MQTT
- Topic: zmcnair/feeds/bkh.<label>
- Payload: 0 or 1

### File
- /opt/poopi/app/gpio_events.log
- Format: timestamp,pin,label,state

## Status Topic
- zmcnair/feeds/bkh.chkgpio-status
