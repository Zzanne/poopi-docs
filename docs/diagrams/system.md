# System Diagram

```mermaid
graph TD
  GPIO --> chkgpio
  GPIO --> heartbeat
  chkgpio --> MQTT
  heartbeat --> JSON
  LevelSensor --> ADS1115 --> levelgem
  levelgem --> MQTT
```
