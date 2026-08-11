# Power Supply Diagnostics: Detecting PSU Problems Before They Cause Damage

**Goal:** Diagnose power supply issues on Windows and Linux using software analysis, voltage monitoring, and hardware testing
**Level:** Intermediate
**Language:** English

---

**Table of Contents:** Why PSU Problems Are Hard to Detect | Symptoms | Windows Diagnostics | Linux Diagnostics | ATX Voltage Specifications | Hardware Testing | Diagnostic Scripts | Quick Reference

---

## Why PSU Problems Are Hard to Detect

PSU failures are notoriously difficult to detect with software alone -- they often masquerade as disk errors, random crashes, or memory corruption. This section covers what software can reveal and when hardware testing is necessary.

Unlike disks (SMART), CPUs (thermal sensors), or RAM (ECC), power supplies have **no standardized self-reporting mechanism**. There is no "PSU health" attribute. Instead, we rely on:

- **Motherboard voltage sensors** — most boards monitor the main ATX rails
- **Crash pattern analysis** — the Windows Kernel-Power Event 41 contains clues
- **Symptom correlation** — certain failure patterns point to power issues
- **Stress testing** — pushing the PSU to its limits to trigger failures
- **Hardware measurement** — multimeter or dedicated PSU tester

---

## Symptoms That Point to PSU Problems

### Strong Indicators

| Symptom | Why It Points to PSU |
|---------|---------------------|
| System shuts off instantly (no BSOD, no warning) | CPU lost power — no time for a crash dump |
| Crashes only under heavy load (gaming, rendering) | PSU cannot sustain peak wattage |
| Multiple different BSOD codes (not the same one repeating) | Unstable voltages cause unpredictable failures |
| USB devices disconnect randomly | +5V rail sagging |
| SATA CRC errors on multiple drives simultaneously | +5V or +3.3V rail instability affects SATA signaling |
| System won't wake from sleep reliably | +5VSB (standby) rail is weak |
| Burning smell from the back of the case | Capacitor failure — shut down immediately |

### Weak Indicators (Could Be PSU, But Often Aren't)

| Symptom | More Likely Cause |
|---------|-------------------|
| Single BSOD code repeating | Driver or hardware fault (RAM, disk) |
| CRC errors on only one drive | SATA cable for that specific drive |
| System runs fine under load but crashes at idle | Sleep/wake driver bug |
| Fan noise changes | Normal thermal management |

---

## Windows: Software-Based PSU Diagnostics

### Step 1: Analyze Kernel-Power Event 41

Event 41 is logged every time Windows starts after an unclean shutdown. The **BugcheckCode** field inside the event XML reveals whether it was a software crash or a power loss.

```powershell
$startDate = (Get-Date).AddDays(-30)
$kp41 = Get-WinEvent -FilterHashtable @{
    LogName      = 'System'
    ProviderName = 'Microsoft-Windows-Kernel-Power'
    Id           = 41
    StartTime    = $startDate
} -ErrorAction SilentlyContinue

if ($kp41) {
    Write-Host "Found $($kp41.Count) unexpected shutdown(s)`n"
    foreach ($ev in $kp41) {
        $xml = [xml]$ev.ToXml()
        $eventData = $xml.Event.EventData.Data
        $bugcheckCode = ($eventData | Where-Object { $_.Name -eq "BugcheckCode" }).'#text'
        $sleepInProgress = ($eventData | Where-Object { $_.Name -eq "SleepInProgress" }).'#text'

        Write-Host "  Time:            $($ev.TimeCreated)"
        Write-Host "  BugcheckCode:    $bugcheckCode"
        Write-Host "  SleepInProgress: $sleepInProgress"

        if ($bugcheckCode -eq "0") {
            Write-Host "  => POWER LOSS (no crash dump generated)" -ForegroundColor Red
        } else {
            Write-Host "  => Software crash (BSOD code: $bugcheckCode)"
        }
        Write-Host ""
    }
}
```

### Interpreting Event 41 Fields

| Field | Value | Meaning |
|-------|-------|---------|
| **BugcheckCode** | **0** | **No BSOD occurred** — the system lost power instantly. This is the strongest software indicator of a PSU problem (or external power loss). |
| **BugcheckCode** | nonzero | A BSOD occurred — this is a software/driver/hardware crash, not necessarily PSU-related. |
| **SleepInProgress** | 0 | System was fully running when the event occurred |
| **SleepInProgress** | nonzero | System was in or transitioning to/from a sleep state |

!!! warning "BugcheckCode = 0 + SleepInProgress = 6"
    This combination means the system lost power while sleeping. Common causes:

    - PSU's +5VSB (standby) rail is failing
    - Power strip or UPS cut power during sleep
    - BIOS sleep/wake settings are misconfigured
    - The PSU cannot maintain standby power for connected USB devices

!!! example "Real-World Example"
    In the scenario that prompted this guide, 3 out of 5 unexpected shutdowns had BugcheckCode=0 with SleepInProgress=6 — the system was losing power during sleep. Combined with SATA CRC errors on multiple drives, this pointed to either PSU instability or external power interruptions.

### Step 2: Read Motherboard Voltage Sensors

Windows has no built-in way to read voltage sensors. You need one of these tools:

#### Option A: HWiNFO64 (Recommended)

[Download HWiNFO64](https://www.hwinfo.com/download/){ target="_blank" } -- free, portable version available. For detailed remote monitoring and Grafana integration, see [HWiNFO Remote Monitoring](HWiNFO_Remote_Monitoring.md).

1. Run HWiNFO64 in **Sensors-only** mode
2. Find the motherboard chip section (typically Nuvoton, ITE, or Fintek)
3. Look for voltage readings: +3.3V, +5V, +12V, VBat, +5VSB
4. Leave it running for several hours and check **Min/Max** columns
5. Compare against the ATX specification (see reference table below)

HWiNFO64 can also log sensor data to CSV for long-term monitoring.

#### Option B: LibreHardwareMonitor (Scriptable)

[LibreHardwareMonitor](https://github.com/LibreHardwareMonitor/LibreHardwareMonitor){ target="_blank" } exposes a WMI namespace that PowerShell can query:

1. Download and run LibreHardwareMonitor
2. Enable **Options > Remote Web Server** or just leave it running
3. Use this PowerShell snippet to read voltages:

```powershell
Get-WmiObject -Namespace root\LibreHardwareMonitor -Class Sensor |
    Where-Object { $_.SensorType -eq "Voltage" } |
    ForEach-Object {
        $val = [math]::Round($_.Value, 3)
        $min = [math]::Round($_.Min, 3)
        $max = [math]::Round($_.Max, 3)
        Write-Host "$($_.Name): ${val}V  (min: ${min}V, max: ${max}V)"
    }
```

### Step 3: Stress Test the PSU

[OCCT](https://www.ocbase.com/){ target="_blank" } (free for personal use) has a dedicated PSU test mode:

1. Run OCCT and select the **Power Supply** test
2. This stresses CPU and GPU simultaneously to maximize power draw
3. Monitor voltages during the test (OCCT shows them in real-time)
4. If the system crashes during this test, the PSU is the most likely cause
5. Run for at least 30 minutes — some PSU failures only appear after thermal buildup

!!! danger "Before Running a PSU Stress Test"
    - Save all work and close all applications
    - A failing PSU under stress can damage components
    - If you hear clicking, buzzing, or smell burning — stop immediately
    - Have a fire extinguisher rated for electrical fires accessible (not water-based)

---

## Linux: Software-Based PSU Diagnostics

### Step 1: Read Voltage Sensors with lm-sensors

```bash
# Install
sudo apt install lm-sensors    # Debian/Ubuntu
sudo dnf install lm_sensors    # RHEL/Fedora

# Detect sensors
sudo sensors-detect
# Answer YES to all probes (or review each one)

# Read current values
sensors
```

Example output:

```
nct6776-isa-0290
Adapter: ISA adapter
Vcore:           +0.90 V  (min =  +0.00 V, max =  +1.74 V)
in1:             +1.02 V  (min =  +0.00 V, max =  +0.00 V)  ALARM
AVCC:            +3.34 V  (min =  +2.98 V, max =  +3.63 V)
+3.3V:           +3.38 V  (min =  +2.98 V, max =  +3.63 V)
in4:             +1.01 V  (min =  +0.00 V, max =  +0.00 V)  ALARM
in5:             +1.26 V  (min =  +0.00 V, max =  +0.00 V)  ALARM
in6:             +0.06 V  (min =  +0.00 V, max =  +0.00 V)  ALARM
3VSB:            +3.42 V  (min =  +2.98 V, max =  +3.63 V)
Vbat:            +3.26 V  (min =  +2.70 V, max =  +3.63 V)
in9:            +1.728 V  (min =  +0.00 V, max =  +0.00 V)  ALARM
```

!!! tip "Ignore the ALARM Labels on Unused Inputs"
    Many motherboard sensor chips have inputs that are not connected to anything. These show 0V with ALARM status. Only pay attention to the labeled rails (+3.3V, +5V, +12V, VBat, 3VSB).

### Step 2: Continuous Voltage Monitoring

```bash
# Log voltage readings every 60 seconds
while true; do
    echo "$(date '+%Y-%m-%d %H:%M:%S')" >> /tmp/voltage_log.txt
    sensors | grep -E '\+3\.3V|\+5V|\+12V|3VSB|Vbat' >> /tmp/voltage_log.txt
    echo "---" >> /tmp/voltage_log.txt
    sleep 60
done
```

Or use `collectd` or `telegraf` for proper time-series monitoring.

### Step 3: Check for Power Loss Events

```bash
# Unexpected shutdowns (no clean shutdown logged before reboot)
journalctl --list-boots | head -20

# Look for boots where the previous session has no shutdown entry
# A missing "Stopped target" or "Reached target Shutdown" before a new boot = power loss

# Check for ACPI power events
journalctl -k | grep -iE 'ACPI.*power|power.supply|battery|critical'

# Check for hardware watchdog resets
journalctl -k | grep -i watchdog
```

### Step 4: Kernel Messages Related to Power

```bash
# Voltage-related kernel messages
dmesg | grep -iE 'voltage|power|acpi|thermal|throttl'

# CPU throttling due to power issues
grep -r "cpu_frequency" /sys/devices/system/cpu/ 2>/dev/null
```

### Step 5: Stress Test with stress-ng

```bash
# Install
sudo apt install stress-ng

# CPU + memory stress (maximizes power draw)
stress-ng --cpu $(nproc) --vm 2 --vm-bytes 1G --timeout 600 --metrics-brief

# Monitor voltages during the test in another terminal
watch -n 2 sensors
```

For GPU stress, use `gpu-burn` (NVIDIA) or `furmark` under Wine.

---

## ATX Power Supply Voltage Specifications

### Tolerance Table

| Rail | Nominal | Min (-5%) | Max (+5%) | Danger Zone |
|------|---------|-----------|-----------|-------------|
| **+3.3V** | 3.300V | 3.135V | 3.465V | Below 3.1V or above 3.5V |
| **+5V** | 5.000V | 4.750V | 5.250V | Below 4.7V or above 5.3V |
| **+12V** | 12.000V | 11.400V | 12.600V | Below 11.0V or above 13.0V |
| **-12V** | -12.000V | -12.600V | -11.400V | Rarely monitored; legacy rail |
| **+5VSB** | 5.000V | 4.750V | 5.250V | Standby rail — powers wake-on-LAN, USB charging |

### What Each Rail Powers

| Rail | Components |
|------|------------|
| **+12V** | CPU (via VRM), GPU (via PCIe power), fans, HDDs (motor), optical drives |
| **+5V** | SATA signaling, USB ports, some logic chips |
| **+3.3V** | RAM, chipset, SATA controller logic, some SSD internals |
| **+5VSB** | Wake-on-LAN, USB power in sleep, BIOS RTC, power button circuit |

### Voltage Deviations and Their Symptoms

| Deviation | Typical Symptoms |
|-----------|-----------------|
| +12V sag under load | System shuts off during gaming/rendering; GPU crashes |
| +12V ripple (fluctuation) | Random BSODs, memory errors, CPU throttling |
| +5V sag | USB device disconnections, SATA CRC errors, disk I/O failures |
| +3.3V sag | Memory errors, chipset instability, boot failures |
| +5VSB drops during sleep | System won't wake from sleep; power loss during standby |

!!! warning "SATA CRC Errors and the +5V Rail"
    SATA data signaling operates on the +5V rail. If the PSU's +5V output is unstable or sagging under load, it can cause **CRC errors on multiple SATA drives simultaneously** — mimicking cable failures. If CRC errors appear on drives connected to different cables and different SATA ports, suspect the PSU's +5V rail before replacing cables.

---

## Hardware Testing

When software diagnostics are inconclusive, measure voltages directly.

### PSU Tester (Simple, ~10-15 EUR)

A dedicated PSU tester plugs into the 24-pin ATX connector and displays voltages on an LCD. Quick and easy, but only tests at zero load (no components drawing power).

### Multimeter (More Accurate)

Measure voltages at the Molex or SATA power connectors while the system is running:

| Pin | Wire Color | Expected Voltage |
|-----|------------|------------------|
| 1 (Molex) | Yellow | +12V |
| 2 (Molex) | Black | Ground |
| 3 (Molex) | Black | Ground |
| 4 (Molex) | Red | +5V |

**SATA Power Connector (15-pin):**

| Pins | Wire Color | Voltage |
|------|------------|---------|
| 1-3 | Orange | +3.3V |
| 4-6 | Black | Ground |
| 7-9 | Red | +5V |
| 10-12 | Black | Ground |
| 13-15 | Yellow | +12V |

!!! danger "Safety"
    - Never open the PSU case — capacitors inside can hold lethal charge even when unplugged
    - Measure at the connectors, never inside the unit
    - Use one hand when probing, keep the other hand away from the case
    - Set the multimeter to DC voltage, 20V range

### When to Replace the PSU

| Condition | Action |
|-----------|--------|
| Any voltage outside the +/-5% ATX specification under load | Replace |
| BugcheckCode=0 crashes that stop after PSU replacement | Confirmed PSU failure |
| Visible capacitor bulging or leaking on the PSU's PCB (if visible through the fan grill) | Replace immediately |
| PSU is 7+ years old with any power-related symptoms | Preventive replacement recommended |
| System crashes only during high power draw (stress test, gaming) | PSU likely cannot deliver rated wattage |
| Burning smell | Shut down and replace immediately |

---

## The Complete PSU Diagnostic Script (Windows)

??? note "psu_check.ps1 - Click to expand"

    ```powershell
    # PSU / Voltage Health Check
    # Run from an elevated (Administrator) PowerShell session

    Write-Host "============================================"
    Write-Host "  PSU / VOLTAGE DIAGNOSTICS"
    Write-Host "  $(Get-Date)"
    Write-Host "============================================"

    # 1. Hardware Monitor Sensors (requires LibreHardwareMonitor running)
    Write-Host "`n--- VOLTAGE SENSORS ---"
    $found = $false
    foreach ($ns in @("root\OpenHardwareMonitor", "root\LibreHardwareMonitor")) {
        try {
            $sensors = Get-WmiObject -Namespace $ns -Class Sensor -ErrorAction Stop |
                Where-Object { $_.SensorType -eq "Voltage" }
            if ($sensors) {
                $found = $true
                $sensors | ForEach-Object {
                    $val = [math]::Round($_.Value, 3)
                    $min = [math]::Round($_.Min, 3)
                    $max = [math]::Round($_.Max, 3)
                    Write-Host "  $($_.Name): ${val}V (min: ${min}V, max: ${max}V)"
                }
            }
            break
        } catch { continue }
    }
    if (-not $found) {
        Write-Host "  No hardware monitor found. Run LibreHardwareMonitor for voltage data."
    }

    # 2. Kernel-Power Event 41 Analysis
    Write-Host "`n--- UNEXPECTED SHUTDOWN ANALYSIS (last 30 days) ---"
    $kp41 = Get-WinEvent -FilterHashtable @{
        LogName      = 'System'
        ProviderName = 'Microsoft-Windows-Kernel-Power'
        Id           = 41
        StartTime    = (Get-Date).AddDays(-30)
    } -ErrorAction SilentlyContinue

    $powerLossCount = 0
    $bsodCount = 0

    if ($kp41) {
        foreach ($ev in $kp41) {
            $xml = [xml]$ev.ToXml()
            $eventData = $xml.Event.EventData.Data
            $bugcheckCode = ($eventData | Where-Object { $_.Name -eq "BugcheckCode" }).'#text'
            $sleepInProgress = ($eventData | Where-Object { $_.Name -eq "SleepInProgress" }).'#text'

            if ($bugcheckCode -eq "0") {
                $powerLossCount++
                $type = "POWER LOSS"
            } else {
                $bsodCount++
                $type = "BSOD (code: $bugcheckCode)"
            }

            $sleepText = if ($sleepInProgress -ne "0") { " [during sleep]" } else { "" }
            Write-Host "  $($ev.TimeCreated) - $type$sleepText"
        }
        Write-Host ""
        Write-Host "  Summary: $powerLossCount power loss, $bsodCount BSOD events"
        if ($powerLossCount -gt $bsodCount) {
            Write-Host "  => Power loss events dominate - investigate PSU and mains power" -ForegroundColor Red
        }
    } else {
        Write-Host "  No unexpected shutdowns found."
    }

    # 3. ATX Voltage Reference
    Write-Host "`n--- ATX VOLTAGE REFERENCE ---"
    Write-Host "  Rail      Nominal    Tolerance    Danger Zone"
    Write-Host "  +3.3V     3.300V     3.135-3.465  below 3.1 / above 3.5"
    Write-Host "  +5V       5.000V     4.750-5.250  below 4.7 / above 5.3"
    Write-Host "  +12V     12.000V    11.400-12.600 below 11.0 / above 13.0"
    Write-Host "  +5VSB     5.000V     4.750-5.250  standby rail"
    ```

---

## The Complete PSU Diagnostic Script (Linux)

??? note "psu_check.sh - Click to expand"

    ```bash
    #!/bin/bash
    # PSU / Voltage Health Check for Linux
    # Usage: sudo ./psu_check.sh

    echo "============================================"
    echo "  PSU / VOLTAGE DIAGNOSTICS"
    echo "  $(date)"
    echo "============================================"

    # 1. Voltage Sensors
    echo ""
    echo "--- VOLTAGE SENSORS ---"
    if command -v sensors &>/dev/null; then
        sensors | grep -iE 'volt|V$|in[0-9]|VCore|AVCC|3VSB|Vbat|\+3\.3|\+5\.|\+12\.'
    else
        echo "  lm-sensors not installed. Run: sudo apt install lm-sensors && sudo sensors-detect"
    fi

    # 2. ACPI / Power Events
    echo ""
    echo "--- POWER-RELATED KERNEL MESSAGES ---"
    dmesg -T 2>/dev/null | grep -iE 'ACPI.*power|voltage|over.?current|under.?voltage|power.supply' | tail -10
    if [ $? -ne 0 ]; then
        echo "  No power-related kernel messages found."
    fi

    # 3. Boot History (look for unclean shutdowns)
    echo ""
    echo "--- RECENT BOOTS ---"
    journalctl --list-boots 2>/dev/null | tail -10

    echo ""
    echo "--- UNCLEAN SHUTDOWNS (last 30 days) ---"
    power_loss=0
    clean_shutdown=0

    boot_ids=$(journalctl --list-boots --no-pager 2>/dev/null | awk '{print $2}')
    for bid in $boot_ids; do
        last_line=$(journalctl -b "$bid" --no-pager 2>/dev/null | tail -5 | grep -c "Reached target.*Shutdown\|System halted\|Power down")
        if [ "$last_line" -eq 0 ]; then
            power_loss=$((power_loss + 1))
        else
            clean_shutdown=$((clean_shutdown + 1))
        fi
    done
    echo "  Clean shutdowns: $clean_shutdown"
    echo "  Unclean/power loss: $power_loss"

    # 4. Reference
    echo ""
    echo "--- ATX VOLTAGE REFERENCE ---"
    echo "  Rail      Nominal    Tolerance        Danger Zone"
    echo "  +3.3V     3.300V     3.135-3.465V     below 3.1 / above 3.5"
    echo "  +5V       5.000V     4.750-5.250V     below 4.7 / above 5.3"
    echo "  +12V     12.000V    11.400-12.600V    below 11.0 / above 13.0"
    echo "  +5VSB     5.000V     4.750-5.250V     standby rail"

    echo ""
    echo "--- RECOMMENDATIONS ---"
    echo "  1. Run 'sensors' and compare voltages against the reference table"
    echo "  2. Use 'stress-ng' to load the system and watch for voltage drops"
    echo "  3. If voltages sag more than 5% under load, consider PSU replacement"
    echo "  4. For definitive testing, use a multimeter on Molex/SATA connectors"
    ```

---

## Quick Reference

| Task | Windows | Linux |
|------|---------|-------|
| Read voltage sensors | HWiNFO64 / LibreHardwareMonitor | `sensors` (lm-sensors) |
| Continuous monitoring | HWiNFO64 CSV logging | `collectd` / `telegraf` / script loop |
| Crash analysis | Event 41 BugcheckCode parsing | `journalctl --list-boots` + shutdown analysis |
| Stress test PSU | OCCT (PSU mode) | `stress-ng` + `gpu-burn` |
| Measure voltages | Multimeter at Molex/SATA | Multimeter at Molex/SATA |
| Check standby rail | Event 41 SleepInProgress field | Wake-from-sleep failure logs |

---

## See Also

- [HWiNFO Remote Monitoring](HWiNFO_Remote_Monitoring.md) -- Real-time sensor monitoring with remote access and Grafana dashboards
- [Smart Power Monitoring](Smart_Power_Monitoring.md) -- External power monitoring with smart plugs, MQTT, and Home Assistant
- [PiKVM Setup](PiKVM_Setup.md) -- Hardware-level remote access for BIOS/UEFI and power control
