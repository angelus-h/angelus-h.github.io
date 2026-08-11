# Smart Power Monitoring: Shelly, Tasmota, and Home Assistant

**Goal:** Monitor computer power consumption, detect PSU degradation, and enable remote power control using smart plugs and home automation
**Level:** Beginner to Intermediate
**Language:** English

---

**Table of Contents:** Why Monitor Power | Hardware Options | Shelly Plug Setup | Tasmota Setup | Home Assistant Integration | Grafana + InfluxDB | Power Reference Values | Quick Reference

---

## Why Monitor Power Consumption?

This is the budget-friendly alternative to enterprise PDUs (Power Distribution Units) for home lab and desktop setups. For internal PSU voltage diagnostics, see [PSU Diagnostics](PSU_Diagnostics.md).

| Use Case | What It Reveals |
|----------|-----------------|
| **PSU health tracking** | Gradual increase in idle wattage suggests capacitor aging or efficiency loss |
| **Load profiling** | Know your actual power draw vs PSU rating — detect overloading |
| **Crash correlation** | Did the system crash at a power spike? Smart plug logs show the timeline |
| **Remote power control** | Hard-reset a frozen system from your phone, or power-cycle after a power outage |
| **Cost calculation** | Track actual electricity consumption for budgeting |
| **UPS sizing** | Measure real-world draw to correctly size a UPS |

---

## Hardware Options

### Smart Plugs with Power Monitoring

| Device | Price (~) | Metering | API | Local Control | Notes |
|--------|-----------|----------|-----|---------------|-------|
| **Shelly Plug S** | 20 EUR | W, V, A | REST, MQTT | Yes (no cloud required) | Best balance of features and price |
| **Shelly Plus Plug S** | 22 EUR | W, V, A | REST, MQTT, BLE | Yes | Gen2, ESP32-based |
| **Sonoff S31** (Tasmota) | 15 EUR | W, V, A | MQTT | Yes (after flash) | Requires Tasmota firmware flash |
| **TP-Link Kasa KP115** | 25 EUR | W, V, A | Local API | Yes (via python-kasa) | Works without cloud; proprietary protocol |
| **Athom Smart Plug** | 15 EUR | W, V, A | MQTT | Yes | Pre-flashed with Tasmota or ESPHome |

!!! tip "Choose Local-First Devices"
    For power monitoring a computer, avoid cloud-dependent plugs. If your internet goes down, you still want to be able to read power data and control the plug locally. **Shelly** and **Tasmota** devices work fully on local network without cloud.

### Maximum Load Rating

!!! danger "Check the Plug's Power Rating"
    Most smart plugs are rated for **2300-3600W** (10-16A at 230V EU). A typical desktop with a mid-range GPU draws 200-500W. A high-end workstation with multiple GPUs can draw 800W+. Always verify:

    - PSU wattage rating (the plug must handle at least this)
    - Actual measured draw under full load (use the plug itself to check)
    - Never daisy-chain smart plugs or use them with extension cords carrying high loads

---

## Shelly Plug Setup

### Initial Configuration

1. Plug in the Shelly device — it creates a WiFi access point (`ShellyPlugS-XXXX`)
2. Connect to that WiFi network from your phone/laptop
3. Open `http://192.168.33.1` in a browser
4. Go to **Internet & Security > WiFi Mode - Client**
5. Enter your home WiFi credentials
6. The device joins your network and gets a DHCP address

### Enable MQTT (for Home Assistant / Grafana)

1. Open the Shelly's web UI at its new IP address
2. Go to **Internet & Security > Advanced > MQTT**
3. Enable MQTT and set:
   - Broker: your MQTT broker IP (e.g., Mosquitto)
   - Username/Password if required
   - Topic prefix: `shelly/plug/desktop` (customize as needed)

### REST API for Scripting

Shelly devices have a built-in REST API — no additional software needed:

```bash
# Get current power readings
curl http://SHELLY_IP/meter/0

# Response:
# {"power": 145.2, "is_valid": true, "timestamp": 1234567890,
#  "counters": [145.2, 143.8, 144.5], "total": 12345}

# Get voltage and current
curl http://SHELLY_IP/status

# Turn off (emergency shutdown)
curl http://SHELLY_IP/relay/0?turn=off

# Turn on (remote power-on)
curl http://SHELLY_IP/relay/0?turn=on

# Get energy consumption history
curl http://SHELLY_IP/meter/0
```

### PowerShell Monitoring Script

```powershell
# Read power data from Shelly Plug
param(
    [string]$ShellyIP = "192.168.1.100",
    [int]$IntervalSeconds = 10,
    [string]$LogFile = "power_log.csv"
)

# Create CSV header
if (-not (Test-Path $LogFile)) {
    "Timestamp,Power_W,Voltage_V,Current_A,Total_Wh" | Out-File $LogFile
}

Write-Host "Monitoring power from Shelly at $ShellyIP (Ctrl+C to stop)"

while ($true) {
    try {
        $status = Invoke-RestMethod -Uri "http://${ShellyIP}/status" -TimeoutSec 5
        $meter = $status.meters[0]
        $power = [math]::Round($meter.power, 1)
        $total = [math]::Round($meter.total / 60, 1)  # Watt-minutes to Wh

        # Voltage and current from emeters if available
        $voltage = if ($status.emeters) { [math]::Round($status.emeters[0].voltage, 1) } else { "N/A" }
        $current = if ($status.emeters) { [math]::Round($status.emeters[0].current, 3) } else { "N/A" }

        $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
        "$timestamp,$power,$voltage,$current,$total" | Out-File $LogFile -Append

        Write-Host "[$timestamp] Power: ${power}W | Voltage: ${voltage}V | Current: ${current}A | Total: ${total}Wh"
    } catch {
        Write-Host "  [Error reading Shelly: $($_.Exception.Message)]" -ForegroundColor Red
    }
    Start-Sleep -Seconds $IntervalSeconds
}
```

### Bash Monitoring Script

```bash
#!/bin/bash
# Read power data from Shelly Plug
SHELLY_IP="${1:-192.168.1.100}"
INTERVAL="${2:-10}"
LOGFILE="power_log.csv"

echo "Timestamp,Power_W,Total_Wh" > "$LOGFILE"
echo "Monitoring Shelly at $SHELLY_IP every ${INTERVAL}s (Ctrl+C to stop)"

while true; do
    data=$(curl -s "http://${SHELLY_IP}/meter/0" 2>/dev/null)
    if [ $? -eq 0 ]; then
        power=$(echo "$data" | jq -r '.power')
        total=$(echo "$data" | jq -r '.total')
        total_wh=$(echo "scale=1; $total / 60" | bc)
        ts=$(date '+%Y-%m-%d %H:%M:%S')
        echo "$ts,$power,$total_wh" >> "$LOGFILE"
        echo "[$ts] Power: ${power}W | Total: ${total_wh}Wh"
    else
        echo "  [Error connecting to Shelly]"
    fi
    sleep "$INTERVAL"
done
```

---

## Tasmota Smart Plug Setup

For devices flashed with Tasmota firmware (Sonoff S31, Athom plugs, or any ESP8266/ESP32 device).

### Initial Configuration

1. Connect to the Tasmota device's WiFi AP (`tasmota-XXXX`)
2. Open `http://192.168.4.1`
3. Enter your home WiFi credentials
4. Device restarts and joins your network

### Configure Power Monitoring

```
# In the Tasmota console (http://DEVICE_IP/cm)

# Set device type (for Sonoff S31 with CSE7766 energy monitor)
Module 41

# Enable MQTT
MqttHost 192.168.1.50
MqttUser your_user
MqttPassword your_pass
Topic desktop_power

# Set telemetry period (seconds)
TelePeriod 30

# Set timezone
Timezone +1
```

### Tasmota REST API

```bash
# Get current power readings
curl "http://TASMOTA_IP/cm?cmnd=Status%208"

# Response includes:
# {"StatusSNS":{"Time":"2026-08-10T01:00:00",
#   "ENERGY":{"TotalStartTime":"2026-01-01T00:00:00",
#     "Total":123.456, "Yesterday":2.345, "Today":1.234,
#     "Power":145, "ApparentPower":150, "ReactivePower":30,
#     "Factor":0.97, "Voltage":232, "Current":0.625}}}

# Turn off
curl "http://TASMOTA_IP/cm?cmnd=Power%20Off"

# Turn on
curl "http://TASMOTA_IP/cm?cmnd=Power%20On"
```

---

## Home Assistant Integration

Home Assistant provides a dashboard for all your power monitoring data with history graphs, automations, and alerts.

### Install Home Assistant

```bash
# Docker (recommended for existing Linux server)
docker run -d \
    --name homeassistant \
    --restart=unless-stopped \
    -v /opt/homeassistant/config:/config \
    -e TZ=Europe/Budapest \
    --network=host \
    ghcr.io/home-assistant/home-assistant:stable
```

Access at `http://your-server:8123`.

### Add Shelly Integration

1. Go to **Settings > Devices & Services > Add Integration**
2. Search for **Shelly**
3. Enter the Shelly device's IP address
4. The plug appears with power, voltage, current, and energy entities

### Add Tasmota via MQTT

1. Install the **Mosquitto MQTT broker** add-on (or run separately)
2. Go to **Settings > Devices & Services > Add Integration > MQTT**
3. Configure the broker connection
4. Tasmota devices auto-discover via MQTT

### Create a Power Dashboard

```yaml
# In your Home Assistant dashboard configuration
type: entities
title: Desktop Power Monitor
entities:
  - entity: sensor.shelly_plug_desktop_power
    name: Current Power
    icon: mdi:lightning-bolt
  - entity: sensor.shelly_plug_desktop_voltage
    name: Voltage
    icon: mdi:sine-wave
  - entity: sensor.shelly_plug_desktop_current
    name: Current
    icon: mdi:current-ac
  - entity: sensor.shelly_plug_desktop_energy
    name: Total Energy
    icon: mdi:counter
```

### Automation: Alert on Power Anomalies

```yaml
# In automations.yaml
- alias: "Desktop power anomaly alert"
  trigger:
    - platform: numeric_state
      entity_id: sensor.shelly_plug_desktop_power
      above: 500
      for:
        minutes: 5
    - platform: numeric_state
      entity_id: sensor.shelly_plug_desktop_power
      below: 5
      for:
        minutes: 2
  condition:
    - condition: state
      entity_id: switch.shelly_plug_desktop
      state: "on"
  action:
    - service: notify.mobile_app
      data:
        title: "Desktop Power Alert"
        message: >
          Power reading: {{ states('sensor.shelly_plug_desktop_power') }}W.
          This may indicate a PSU problem or unexpected shutdown.
```

### Automation: Auto Power-Cycle on Crash

```yaml
- alias: "Desktop crash recovery - power cycle"
  trigger:
    - platform: numeric_state
      entity_id: sensor.shelly_plug_desktop_power
      below: 5
      for:
        minutes: 5
  condition:
    - condition: state
      entity_id: switch.shelly_plug_desktop
      state: "on"
    - condition: template
      value_template: "{{ now().hour >= 8 and now().hour <= 22 }}"
  action:
    - service: switch.turn_off
      entity_id: switch.shelly_plug_desktop
    - delay: "00:00:10"
    - service: switch.turn_on
      entity_id: switch.shelly_plug_desktop
    - service: notify.mobile_app
      data:
        message: "Desktop was power-cycled due to suspected crash."
```

---

## Grafana + InfluxDB Monitoring

For long-term power data visualization and analysis.

### Architecture

```
Shelly/Tasmota → MQTT → Telegraf → InfluxDB → Grafana
```

### Telegraf Configuration

```toml
# /etc/telegraf/telegraf.d/shelly.conf

[[inputs.mqtt_consumer]]
  servers = ["tcp://localhost:1883"]
  topics = ["shellies/+/relay/0/power", "shellies/+/relay/0/energy"]
  data_format = "value"
  data_type = "float"

  [inputs.mqtt_consumer.tags]
    source = "shelly"

[[outputs.influxdb_v2]]
  urls = ["http://localhost:8086"]
  token = "your-token"
  organization = "home"
  bucket = "power_monitoring"
```

### Grafana Dashboard Queries (InfluxDB/Flux)

```flux
// Current power draw
from(bucket: "power_monitoring")
  |> range(start: -24h)
  |> filter(fn: (r) => r._measurement == "mqtt_consumer")
  |> filter(fn: (r) => r.topic =~ /power/)
  |> aggregateWindow(every: 1m, fn: mean)

// Daily energy consumption
from(bucket: "power_monitoring")
  |> range(start: -30d)
  |> filter(fn: (r) => r._measurement == "mqtt_consumer")
  |> filter(fn: (r) => r.topic =~ /energy/)
  |> aggregateWindow(every: 1d, fn: spread)
```

---

## Power Consumption Reference Values

### Typical Desktop Power Draw

| State | Expected Range | Notes |
|-------|---------------|-------|
| **Off (standby)** | 1-5W | PSU vampire draw + USB standby |
| **BIOS/POST** | 60-120W | Brief spike during initialization |
| **Idle (desktop)** | 50-150W | Depends on components |
| **Light use** (browsing, coding) | 80-200W | CPU boost varies |
| **Heavy CPU load** | 150-300W | Sustained all-core load |
| **GPU gaming** | 250-500W | GPU is the biggest consumer |
| **CPU + GPU stress** | 350-700W | Maximum sustained draw |
| **Startup surge** | +50-100W spike | Motors spinning up, capacitors charging |

### Warning Signs in Power Data

| Pattern | Possible Issue |
|---------|---------------|
| Idle power 20%+ higher than baseline | PSU efficiency degradation; check capacitors |
| Sudden drops to 0W then recovery | Intermittent power connection; check cable/outlet |
| Voltage reading below 220V (EU) consistently | Mains wiring issue; contact electrician |
| Power oscillating rapidly (100W swings at idle) | PSU instability; failing voltage regulation |
| System draws max wattage then drops to 0W | PSU overload shutdown; undersized PSU |

---

## Quick Reference: API Commands

### Shelly

| Action | Command |
|--------|---------|
| Get power | `curl http://IP/meter/0` |
| Get full status | `curl http://IP/status` |
| Turn on | `curl http://IP/relay/0?turn=on` |
| Turn off | `curl http://IP/relay/0?turn=off` |
| Toggle | `curl http://IP/relay/0?turn=toggle` |
| Get settings | `curl http://IP/settings` |
| Reboot plug | `curl http://IP/reboot` |

### Tasmota

| Action | Command |
|--------|---------|
| Get power | `curl "http://IP/cm?cmnd=Status%208"` |
| Turn on | `curl "http://IP/cm?cmnd=Power%20On"` |
| Turn off | `curl "http://IP/cm?cmnd=Power%20Off"` |
| Get full status | `curl "http://IP/cm?cmnd=Status%200"` |
| Restart device | `curl "http://IP/cm?cmnd=Restart%201"` |

---

## See Also

- [PSU Diagnostics](PSU_Diagnostics.md) -- Internal PSU voltage diagnostics, crash analysis, and ATX specifications
- [HWiNFO Remote Monitoring](HWiNFO_Remote_Monitoring.md) -- Software-based sensor monitoring with Grafana integration
- [PiKVM Setup](PiKVM_Setup.md) -- Hardware-level remote access with power button control via GPIO
