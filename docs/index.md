# Poopi Septic Monitoring System

## Overview

Raspberry Pi–based septic monitoring system using GPIO inputs and Node-RED for state processing.

---

## What This Does

- Reads float switches and pump states
- Normalizes and interprets signals in Node-RED
- Publishes system state locally and remotely

---

## How It Works

1. GPIO state is captured and written to `/run/gpio_snapshot.json`
2. Node-RED processes the data
3. System state is encoded as a bitmask
4. Data is published to MQTT, dashboards, and external services

---

## Documentation

Use the navigation menu to explore:

- **System** → architecture and data flow  
- **Services** → scripts and systemd services  
- **Node-RED** → processing logic and state handling  
- **Interfaces** → GPIO, MQTT, and file structures  
- **Diagrams** → visual system layout  

---

## Status

System is operational and documentation is in progress.
