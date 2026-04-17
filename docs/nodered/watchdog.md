# Watchdog System

## Purpose

Detect loss of telemetry and recover automatically.

---

## Input

**Monitored Topic:**
zmcnair/feeds/sensors.burlesonkh-septic-cpu

**Purpose:**
- Confirms system is alive and publishing data
- Used as heartbeat signal

---

## Processing

### 1. Track Last Update
- Store timestamp of last received message

---

### 2. Timeout Detection
Trigger if:
- No data received for ~180 seconds

---

### 3. Failure Counter
- Increment on each missed interval
- Reset when data resumes

---

### 4. Escalation Logic

Conditions:
- Multiple consecutive failures
- System uptime exceeds threshold
- Cooldown period has elapsed

---

## Action

**Command:**
```bash
sudo systemctl restart NetworkManager

---

## Outputs
**MQTT**
Topic:
- zmcnair/feeds/bkh.bkh-watchdog

**Payload**
- Status or alert message indicating watchdog activity

## Watchdog Interpretation (Work in Progress)

| State       | Meaning                               |
|------------|----------------------------------------|
| Normal     | Telemetry received regularly           |
| Timeout    | No data within expected interval       |
| Recovering | Restart action triggered               |
| Recovered  | Data flow restored                     |

## Notes
- Prevents system lockups due to network failure
- Includes cooldown to avoid repeated restart loops
- Acts as a last-resort recovery mechanism
