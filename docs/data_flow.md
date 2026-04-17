# Data Flow

## Digital Path

GPIO
 → chkgpio.py
 → MQTT
 → Node-RED
 → InfluxDB
 → Grafana

## Snapshot Path

GPIO
 → gpio-heartbeat.sh
 → /run/gpio_snapshot.json
 → (consumer TBD)

## Digital State Data Flow
```mermaid
graph TD
    A[GPIO Pins Hardware] --> B[gpio_heartbeat.sh]
    B --> C[gpio_snapshot.json - raw]
    C --> D[Node-RED]

    D --> E[Normalize + Inversion]
    D --> F[Bitmask Construction]
    D --> G[RBE change detection]

    G --> H[Local MQTT system/state]
    G --> I[Adafruit IO]
    G --> J[External systems AIQSO]
```

## Analog Path

Level Sensor (4–20mA)
 → ADS1115
 → level-gem2.py
 → MQTT
 → Node-RED
 → InfluxDB
 → Grafana
