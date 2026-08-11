# HWiNFO64 Remote Sensor Monitoring

**Goal:** Set up real-time hardware sensor monitoring on Windows with remote access and Grafana integration for long-term trend analysis
**Level:** Intermediate
**Language:** English

---

**Table of Contents:** Why HWiNFO64 | Installation and Setup | Logging to CSV | Remote Access Methods | Grafana Integration | Alerting | Health Report Script | Linux Equivalent | Quick Reference

---

## Why HWiNFO64?

HWiNFO64 is the most practical way to monitor voltages, temperatures, and fan speeds on desktop hardware that lacks IPMI. For ATX voltage specifications and PSU failure analysis, see [PSU Diagnostics](PSU_Diagnostics.md).

| Feature | HWiNFO64 | LibreHardwareMonitor | GPU-Z |
|---------|----------|---------------------|-------|
| **Voltage sensors** | All motherboard rails | Most | GPU only |
| **CPU power/VRM** | Detailed VRM monitoring | Basic | No |
| **GPU monitoring** | Full (NVIDIA + AMD) | Full | Full |
| **NVMe health** | SMART + temperature | Limited | No |
| **Logging to CSV** | Built-in | No | No |
| **Remote access** | Remote Sensor Monitor plugin | WMI namespace | No |
| **Shared memory** | Yes (for third-party tools) | No | No |
| **Portable** | Yes (no install needed) | Yes | Yes |
| **Free** | Yes (personal use) | Yes (open source) | Yes |

---

## Installation and Basic Setup

### Download

[HWiNFO64](https://www.hwinfo.com/download/){ target="_blank" } — choose the **Portable** version for zero-install operation.

### Sensors-Only Mode

For monitoring, you only need the sensor readouts:

1. Launch HWiNFO64
2. Check **Sensors-only** at the startup dialog
3. Click **Start**

The sensor window shows every hardware sensor on your system, organized by component.

### Key Sensors to Watch

#### Motherboard Voltages (PSU Health)

Look for the motherboard sensor chip section (Nuvoton NCT67xx, ITE IT87xx, or Fintek F718xx):

| Sensor | Expected | Warning |
|--------|----------|---------|
| **+3.3V** / **VCC3V** | 3.2-3.4V | Outside 3.1-3.5V |
| **+5V** | 4.9-5.1V | Outside 4.7-5.3V |
| **+12V** | 11.8-12.2V | Outside 11.4-12.6V |
| **VBAT** (CMOS battery) | 2.8-3.3V | Below 2.7V (replace battery) |
| **+5VSB** (standby) | 4.9-5.1V | Outside 4.7-5.3V |
| **VCore** (CPU) | Varies by load | Check for excessive droop under load |

#### CPU / VRM

| Sensor | What to Watch |
|--------|---------------|
| **VRM Temperature** | Should stay below 100C under load |
| **CPU Package Power** | Compare against TDP rating |
| **CPU VCore** | Stability — should not fluctuate wildly |

#### Storage

| Sensor | What to Watch |
|--------|---------------|
| **NVMe Temperature** | Below 70C normal; throttles at 80C+ |
| **HDD Temperature** | Below 45C ideal; above 55C is dangerous |
| **Drive Remaining Life** | NVMe wear indicator |

### Min/Max/Average Tracking

HWiNFO64 tracks **minimum**, **maximum**, and **average** values since the sensor window was opened. This is extremely useful:

1. Open the sensor window
2. Leave HWiNFO64 running during normal use (hours)
3. Run a stress test or heavy workload
4. Check the **Min** and **Max** columns

!!! warning "Voltage Out of Spec"
    If the Min/Max voltage readings fall outside ATX specification, the PSU is suspect. See the [ATX voltage tolerance table](PSU_Diagnostics.md#tolerance-table) for reference values.

To reset the tracking: click the clock icon in the toolbar.

---

## Logging to CSV

For long-term data collection:

1. In the sensor window, click the **Logging** button (or press `Ctrl+L`)
2. Choose a file location and name (e.g., `hwinfo_log.csv`)
3. Set the logging interval (recommended: 10-30 seconds for general monitoring)
4. Click **OK** — logging starts immediately

The CSV file contains all sensor values at each interval. Open in Excel or import into a database for analysis.

### Selective Logging

!!! tip "Reduce CSV File Size"
    To avoid huge CSV files, disable sensors you don't need:

    1. Right-click any sensor in the list
    2. Select **Hide** to remove it from the display and log
    3. Or use **Settings > Sensor Layout** to create a custom view

---

## Remote Access Methods

### Method 1: HWiNFO Remote Sensor Monitor (Built-in Web Server)

HWiNFO64 includes a plugin that serves sensor data as a web page.

#### Setup

1. In HWiNFO64 sensor window, go to **Settings** (gear icon)
2. Navigate to the **Remote Sensor Monitor** section
3. Enable **Active Remote Sensor Monitor**
4. Set the port (default: 55555)
5. Optionally set a password
6. Click **OK**

#### Access

Open a browser on any device on your network:

```
http://YOUR_PC_IP:55555
```

The web interface shows all sensor values in real-time.

### Method 2: Shared Memory Interface

HWiNFO64 exposes sensor data via Windows shared memory, which third-party tools can read:

| Tool | Purpose |
|------|---------|
| **Rainmeter** | Desktop widget overlays |
| **RTSS** (RivaTuner) | In-game OSD overlay |
| **SidebarDiagnostics** | Desktop sidebar |
| **HWiNFO Gadget** | Windows gadget-style display |

Enable in: **Settings > Shared Memory Support > Enable**

### Method 3: LibreHardwareMonitor WMI (for PowerShell)

If you prefer scriptable access, run **LibreHardwareMonitor** alongside or instead of HWiNFO64:

```powershell
# Read all voltage sensors
Get-WmiObject -Namespace root\LibreHardwareMonitor -Class Sensor |
    Where-Object { $_.SensorType -eq "Voltage" } |
    ForEach-Object {
        $val = [math]::Round($_.Value, 3)
        Write-Host "$($_.Name): ${val}V"
    }
```

---

## Grafana Integration

For professional-grade dashboards with alerting.

### Architecture

```
HWiNFO64 → Remote Sensor Monitor → hwinfo-telegraf plugin → InfluxDB → Grafana
```

Or:

```
LibreHardwareMonitor → WMI → Custom PowerShell script → InfluxDB → Grafana
```

### Option A: Telegraf + HWiNFO Shared Memory

Use the `win_perf_counters` input or a custom script:

```toml
# /etc/telegraf/telegraf.d/hwinfo.conf (on the Windows machine)

[[inputs.exec]]
  commands = ["powershell -File C:/scripts/hwinfo_to_telegraf.ps1"]
  timeout = "10s"
  interval = "30s"
  data_format = "influx"
```

PowerShell script (`hwinfo_to_telegraf.ps1`):

```powershell
# Read from LibreHardwareMonitor WMI and output in InfluxDB line protocol
$sensors = Get-WmiObject -Namespace root\LibreHardwareMonitor -Class Sensor -ErrorAction SilentlyContinue
if ($sensors) {
    foreach ($s in $sensors) {
        $measurement = "hardware_sensors"
        $name = $s.Name -replace ' ', '_' -replace '[^a-zA-Z0-9_]', ''
        $type = $s.SensorType
        $value = $s.Value
        $hw = $s.Parent -replace ' ', '_' -replace '[^a-zA-Z0-9_/]', ''
        Write-Output "${measurement},sensor=${name},type=${type},hardware=${hw} value=${value}"
    }
}
```

### Option B: Direct HTTP Scraping from Remote Sensor Monitor

```python
#!/usr/bin/env python3
"""Scrape HWiNFO Remote Sensor Monitor and push to InfluxDB."""

import requests
import re
import time
from influxdb_client import InfluxDBClient, Point

HWINFO_URL = "http://localhost:55555"
INFLUX_URL = "http://localhost:8086"
INFLUX_TOKEN = "your-token"
INFLUX_ORG = "home"
INFLUX_BUCKET = "hardware_monitoring"

def scrape_hwinfo():
    """Parse the Remote Sensor Monitor HTML page."""
    resp = requests.get(HWINFO_URL, timeout=5)
    # Parse sensor values from HTML table
    # (Implementation depends on RSM output format version)
    return parse_sensors(resp.text)

def push_to_influx(sensors):
    with InfluxDBClient(url=INFLUX_URL, token=INFLUX_TOKEN, org=INFLUX_ORG) as client:
        write_api = client.write_api()
        for name, value, unit in sensors:
            point = Point("hwinfo") \
                .tag("sensor", name) \
                .tag("unit", unit) \
                .field("value", float(value))
            write_api.write(bucket=INFLUX_BUCKET, record=point)

while True:
    try:
        sensors = scrape_hwinfo()
        push_to_influx(sensors)
    except Exception as e:
        print(f"Error: {e}")
    time.sleep(30)
```

### Grafana Dashboard Panels

Recommended panels for a hardware monitoring dashboard:

| Panel | Query | Visualization |
|-------|-------|---------------|
| **Voltage Rails** | +3.3V, +5V, +12V over time | Time series with threshold bands |
| **CPU Temperature** | Package temp, per-core temps | Time series with max line at TjMax |
| **GPU Stats** | Temp, clock, power, utilization | Multi-axis time series |
| **Fan Speeds** | All fans RPM | Time series |
| **Storage Temps** | NVMe + HDD temperatures | Time series with warning threshold |
| **Power Draw** | CPU package power, GPU power | Stacked area chart |
| **Voltage Gauge** | Current +12V reading | Gauge with green/yellow/red zones |

---

## Alerting

### HWiNFO64 Built-in Alerts

1. In the sensor window, double-click any sensor value
2. In the properties dialog, set warning thresholds
3. Choose alert action: system tray notification, sound, or run a program
4. Example: alert if +12V drops below 11.4V

### Grafana Alerts

```yaml
# Example Grafana alert rule (provisioning format)
groups:
  - name: hardware_alerts
    rules:
      - alert: PSU_Voltage_12V_Low
        expr: hwinfo_value{sensor="12V"} < 11.4
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "+12V rail below ATX spec ({{ $value }}V)"

      - alert: CPU_Temperature_High
        expr: hwinfo_value{sensor="CPU_Package", type="Temperature"} > 90
        for: 2m
        labels:
          severity: warning
        annotations:
          summary: "CPU temperature critical ({{ $value }}C)"
```

---

## Automated Health Report Script

A PowerShell script that reads sensor data and generates a health report:

??? note "sensor_report.ps1 - Click to expand"

    ```powershell
    # Hardware Sensor Health Report
    # Requires LibreHardwareMonitor running (for WMI access)

    Write-Host "============================================"
    Write-Host "  HARDWARE SENSOR HEALTH REPORT"
    Write-Host "  $(Get-Date)"
    Write-Host "============================================"

    $sensors = Get-WmiObject -Namespace root\LibreHardwareMonitor -Class Sensor -ErrorAction SilentlyContinue

    if (-not $sensors) {
        Write-Host "  LibreHardwareMonitor not running. Start it first."
        Write-Host "  Download: https://github.com/LibreHardwareMonitor/LibreHardwareMonitor"
        exit
    }

    # Voltages
    Write-Host "`n--- VOLTAGE READINGS ---"
    $voltages = $sensors | Where-Object { $_.SensorType -eq "Voltage" }
    foreach ($v in $voltages) {
        $val = [math]::Round($v.Value, 3)
        $min = [math]::Round($v.Min, 3)
        $max = [math]::Round($v.Max, 3)
        $status = "OK"

        # Check ATX spec for known rails
        if ($v.Name -match "3\.3V|VCC3") {
            if ($val -lt 3.135 -or $val -gt 3.465) { $status = "!! OUT OF SPEC !!" }
        } elseif ($v.Name -match "^\+?5V$|^5V$") {
            if ($val -lt 4.75 -or $val -gt 5.25) { $status = "!! OUT OF SPEC !!" }
        } elseif ($v.Name -match "12V") {
            if ($val -lt 11.4 -or $val -gt 12.6) { $status = "!! OUT OF SPEC !!" }
        }

        Write-Host "  $($v.Name): ${val}V (min: ${min}V, max: ${max}V) [$status]"
    }

    # Temperatures
    Write-Host "`n--- TEMPERATURES ---"
    $temps = $sensors | Where-Object { $_.SensorType -eq "Temperature" }
    foreach ($t in $temps) {
        $val = [math]::Round($t.Value, 1)
        $max = [math]::Round($t.Max, 1)
        $status = if ($val -gt 85) { "!! HOT !!" } elseif ($val -gt 70) { "WARM" } else { "OK" }
        Write-Host "  $($t.Name): ${val}C (max: ${max}C) [$status]"
    }

    # Fan Speeds
    Write-Host "`n--- FAN SPEEDS ---"
    $fans = $sensors | Where-Object { $_.SensorType -eq "Fan" }
    foreach ($f in $fans) {
        $val = [math]::Round($f.Value, 0)
        $status = if ($val -eq 0) { "!! STOPPED !!" } else { "OK" }
        Write-Host "  $($f.Name): ${val} RPM [$status]"
    }

    # Power
    Write-Host "`n--- POWER ---"
    $power = $sensors | Where-Object { $_.SensorType -eq "Power" }
    foreach ($p in $power) {
        $val = [math]::Round($p.Value, 1)
        Write-Host "  $($p.Name): ${val}W"
    }
    ```

---

## Linux Equivalent Setup

On Linux, the equivalent monitoring stack uses `lm-sensors` and `collectd` or `telegraf`:

```bash
# Install lm-sensors
sudo apt install lm-sensors
sudo sensors-detect

# Continuous monitoring with telegraf
# /etc/telegraf/telegraf.d/sensors.conf
# [[inputs.sensors]]
#   (reads from lm-sensors automatically)

# Or manual monitoring
watch -n 5 sensors
```

For GPU monitoring on Linux, use `nvidia-smi` (NVIDIA) or `radeontop` (AMD).

---

## Quick Reference

| Task | Tool | Command/Action |
|------|------|----------------|
| Read all sensors | HWiNFO64 | Sensors-only mode |
| Log sensor data | HWiNFO64 | Ctrl+L, set interval |
| Remote web view | HWiNFO64 RSM | Enable in Settings, port 55555 |
| Script access (WMI) | LibreHardwareMonitor | `Get-WmiObject -Namespace root\LibreHardwareMonitor` |
| Long-term graphs | Grafana + InfluxDB | Telegraf or custom scraper |
| Voltage check | HWiNFO64 | Look at Min/Max after running for hours |
| Temperature alerts | HWiNFO64 | Double-click sensor, set thresholds |
| Linux sensors | lm-sensors | `sensors` command |

---

## See Also

- [PSU Diagnostics](PSU_Diagnostics.md) -- ATX voltage specifications, crash analysis, and PSU stress testing
- [Smart Power Monitoring](Smart_Power_Monitoring.md) -- External power monitoring with Shelly/Tasmota smart plugs
- [PiKVM Setup](PiKVM_Setup.md) -- Hardware-level remote access for BIOS/UEFI management
