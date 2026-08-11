# PiKVM: Build Your Own IP KVM for Remote Server Management

**Goal:** Build an open-source, Raspberry Pi-based IP KVM for hardware-level remote access to any computer
**Level:** Intermediate to Advanced
**Language:** English

---

**Table of Contents:** What PiKVM Can Do | Hardware Requirements | Software Installation | Hardware Wiring | Web Interface | Network Security | Advanced Configuration | Troubleshooting | Cost Comparison

---

## What PiKVM Can Do

Unlike software-based remote desktop, PiKVM works before the OS boots, in BIOS/UEFI, during OS installation, and even when the system is crashed.

| Feature | Description |
|---------|-------------|
| **Remote console** | See the screen, type, and move the mouse from any browser |
| **BIOS/UEFI access** | Enter BIOS setup remotely — no physical monitor needed |
| **Virtual media** | Mount ISO files as virtual USB drives for OS installation |
| **Power control** | Press power/reset buttons remotely via GPIO or smart plug |
| **ATX control** | Monitor power LED and HDD LED status |
| **Serial console** | Connect to serial ports for headless servers |
| **Multi-user** | Multiple users can view simultaneously |
| **API** | Full REST API for automation and scripting |
| **Wake-on-LAN** | Send WoL packets to start the target machine |

---

## Hardware Requirements

### Minimum Build (~60-80 EUR)

| Component | Purpose | Notes |
|-----------|---------|-------|
| **Raspberry Pi 4** (2GB+) | Main computer | Pi 4B recommended; Pi 5 also supported |
| **HDMI capture dongle** | Captures target's video output | Must be USB UVC compatible; MS2109 chipset recommended |
| **USB-A to USB-A cable** or **USB-C OTG cable** | Keyboard/mouse emulation | Pi connects as USB HID device to the target |
| **MicroSD card** (16GB+) | PiKVM OS storage | Class 10 or better |
| **Power supply** (USB-C, 5V/3A) | Powers the Pi | Official Raspberry Pi PSU recommended |
| **Ethernet cable** | Network connection | WiFi works but wired is far more reliable |

### Recommended Additions

| Component | Purpose | Notes |
|-----------|---------|-------|
| **GPIO relay board** or **ATX adapter board** | Power/reset button control | Directly wires to the target's front panel header |
| **HDMI CSI-2 bridge** (Geekworm C779/C790) | Better video capture | Lower latency, higher quality than USB dongles |
| **Pi case with fan** | Thermal management | PiKVM under load generates heat |
| **OLED display** (SSD1306, 128x32) | Status display | Shows IP address, target status |

---

## Software Installation

### Option 1: Pre-built PiKVM OS Image (Recommended)

```bash
# Download the latest image for your Pi model
# Visit: https://pikvm.org/download/

# Write to SD card (Linux/macOS)
sudo dd if=pikvm-image.img of=/dev/sdX bs=4M status=progress

# Or use Raspberry Pi Imager (Windows/macOS/Linux)
# Select "Use custom" and choose the PiKVM image
```

### Option 2: Install on Existing Raspberry Pi OS

```bash
# This is more complex and not recommended for first-time setup
# See: https://docs.pikvm.org/
```

### First Boot Configuration

1. Insert the SD card, connect Ethernet, and power on the Pi
2. Find the Pi's IP address (check your router's DHCP leases or use `nmap`)
3. Open a browser and navigate to `https://<pi-ip-address>`
4. Default credentials: `admin` / `admin`
5. **Change the password immediately**

```bash
# SSH into the Pi (default: root/root)
ssh root@<pi-ip-address>

# Change web interface password
kvmd-htpasswd set admin

# Change system root password
passwd

# Update PiKVM
rw    # Switch filesystem to read-write
pacman -Syu
ro    # Switch back to read-only
```

---

## Hardware Wiring

### Video Capture Connection

```
Target PC                    PiKVM (Raspberry Pi 4)
┌──────────┐                 ┌──────────┐
│          │    HDMI cable    │          │
│  GPU     ├─────────────────┤ USB HDMI │
│  HDMI    │                 │ Capture  │
│  Output  │                 │ Dongle   │
│          │                 │          │
│  USB     │   USB A-to-A    │ USB-C    │
│  Port    ├─────────────────┤ OTG Port │
│          │  (data only!)   │          │
└──────────┘                 └──────────┘
```

!!! warning "USB Cable for HID Emulation"
    Use a **data-capable** USB cable. The Pi's **USB-C port** (OTG) connects to any **USB-A port** on the target. This single cable provides keyboard, mouse, and virtual mass storage emulation.

### ATX Power Control Wiring (GPIO)

To control the target's power and reset buttons remotely, connect GPIO pins to the motherboard's front panel header:

```
Raspberry Pi GPIO             Target Motherboard
                               Front Panel Header
┌───────────────┐             ┌──────────────────┐
│ GPIO 24 ──────┼─── wire ───┤ PWR_BTN+         │
│ GND    ──────┼─── wire ───┤ PWR_BTN-         │
│               │             │                  │
│ GPIO 25 ──────┼─── wire ───┤ RST_BTN+         │
│ GND    ──────┼─── wire ───┤ RST_BTN-         │
│               │             │                  │
│ GPIO 21 ──────┼─── wire ───┤ PWR_LED+ (sense) │
│ GND    ──────┼─── wire ───┤ PWR_LED-         │
│               │             │                  │
│ GPIO 20 ──────┼─── wire ───┤ HDD_LED+ (sense) │
│ GND    ──────┼─── wire ───┤ HDD_LED-         │
└───────────────┘             └──────────────────┘
```

!!! tip "Use a Relay Board for Safety"
    Connecting GPIO directly to the motherboard header works but risks damage if wired incorrectly. A **relay board** or the **official PiKVM ATX adapter** provides electrical isolation. The adapter is specifically designed for this purpose and costs ~15 EUR.

### Configuration for ATX Control

Edit `/etc/kvmd/override.yaml`:

```yaml
kvmd:
    gpio:
        drivers:
            power:
                type: gpio
                pin: 24
            reset:
                type: gpio
                pin: 25
            power_led:
                type: gpio
                pin: 21
                mode: input
            hdd_led:
                type: gpio
                pin: 20
                mode: input
        scheme:
            power_button:
                driver: power
                pin: 24
                mode: output
                switch: false
                pulse:
                    delay: 0.5
            reset_button:
                driver: reset
                pin: 25
                mode: output
                switch: false
                pulse:
                    delay: 0.5
            power_led:
                driver: power_led
                pin: 21
                mode: input
            hdd_led:
                driver: hdd_led
                pin: 20
                mode: input
```

Restart the service:

```bash
systemctl restart kvmd
```

---

## Web Interface Features

### Remote Console

The main screen shows the target's video output in real-time. Controls:

| Action | Method |
|--------|--------|
| **Keyboard input** | Just type — keys are forwarded to the target |
| **Mouse control** | Move the mouse — relative or absolute mode |
| **Clipboard** | Paste text via the clipboard button (types it out as keystrokes) |
| **Screenshot** | Capture the current frame |
| **Fullscreen** | F11 or the fullscreen button |
| **Ctrl+Alt+Del** | Dedicated button in the toolbar |

### Virtual Media (ISO Mounting)

Mount ISO files as virtual USB drives — essential for remote OS installation:

1. Click the **Drive** icon in the toolbar
2. Upload an ISO file or provide a URL
3. Select **CD-ROM** or **Flash Drive** mode
4. Click **Connect**

The target sees a USB drive or CD-ROM with the ISO contents.

### Power Control

!!! note "Alternative: Smart Plug Power Control"
    If GPIO wiring to the motherboard header is not practical, a [smart plug with REST API](Smart_Power_Monitoring.md) (Shelly, Tasmota) can provide remote power cycling as a simpler alternative, though without soft power-on/off or reset button emulation.

If ATX wiring is configured:

- **Power** button (short press = power on/off, long press = force off)
- **Reset** button
- LED status indicators (power on, HDD activity)

---

## Network Security

PiKVM provides hardware-level access — securing it is critical.

### Essential Security Steps

```bash
# 1. Change default passwords (done during first boot)
kvmd-htpasswd set admin
passwd

# 2. Enable HTTPS (enabled by default, uses self-signed cert)
# For a proper certificate:
rw
kvmd-certbot certonly --webroot -w /var/lib/kvmd/htdocs -d pikvm.yourdomain.com
ro

# 3. Restrict network access
# Put PiKVM on a management VLAN or restrict by firewall rules
# Never expose PiKVM directly to the internet

# 4. Enable two-factor authentication
# Edit /etc/kvmd/override.yaml
```

```yaml
kvmd:
    auth:
        totp:
            enabled: true
            secret: YOUR_BASE32_SECRET
```

!!! danger "Never Expose PiKVM to the Public Internet"
    PiKVM provides pre-boot, BIOS-level access. If compromised, an attacker has complete control. Access it only via VPN or a secured management network.

### Remote Access via VPN

For accessing PiKVM from outside the local network:

| Method | Complexity | Notes |
|--------|-----------|-------|
| **WireGuard** on the Pi | Low | Lightweight VPN, easy to set up |
| **Tailscale** | Very Low | Zero-config mesh VPN, works behind NAT |
| **OpenVPN** on router | Medium | Route-based, works with any client |
| **SSH tunnel** | Low | `ssh -L 8443:localhost:443 root@pikvm-ip` |

---

## Advanced Configuration

### Multiple Target Machines

One PiKVM can manage multiple machines with a **KVM switch**:

1. Connect an HDMI KVM switch between the PiKVM and multiple targets
2. Control the switch via USB (if supported) or GPIO relays
3. Configure switching in PiKVM's override.yaml

### Automation via API

PiKVM has a REST API for scripting:

```bash
# Check target power status
curl -k -u admin:password https://pikvm-ip/api/atx

# Press power button
curl -k -u admin:password -X POST https://pikvm-ip/api/atx/power/action?action=on

# Type text on the target
curl -k -u admin:password -X POST https://pikvm-ip/api/hid/print \
    -H "Content-Type: application/json" \
    -d '{"text": "Hello from PiKVM!"}'

# Take a screenshot
curl -k -u admin:password -o screenshot.jpg https://pikvm-ip/api/streamer/snapshot
```

### Wake-on-LAN Integration

```yaml
# In /etc/kvmd/override.yaml
kvmd:
    gpio:
        drivers:
            wol:
                type: wol
                mac: AA:BB:CC:DD:EE:FF
        scheme:
            wol_button:
                driver: wol
                pin: 0
                mode: output
                switch: false
                pulse:
                    delay: 0.5
```

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| No video capture | Try a different HDMI capture dongle; check UVC compatibility |
| Laggy video | Use CSI-2 bridge instead of USB dongle; reduce resolution |
| Keyboard/mouse not working | Verify USB OTG cable is data-capable; check USB port on target |
| Can't enter BIOS | Some BIOS require PS/2 keyboard — use PiKVM's PS/2 emulation mode |
| Network unreachable | Check Ethernet cable; verify DHCP or set static IP |
| SD card corruption | Use a high-quality SD card; PiKVM runs in read-only mode by default |

---

## Cost Comparison: PiKVM vs Commercial Solutions

| Solution | Cost | Features |
|----------|------|----------|
| **PiKVM v4 (official kit)** | ~150 EUR | Complete kit, plug-and-play, best performance |
| **PiKVM DIY (Pi 4 + dongle)** | ~60-80 EUR | Requires assembly, good performance |
| **JetKVM** | ~70 EUR | Compact, dedicated hardware, similar features |
| **Dell iDRAC Enterprise** | ~100-200 EUR (license) | Server-only, full BMC integration |
| **HPE iLO Advanced** | ~200-400 EUR (license) | Server-only, full BMC integration |
| **Raritan Dominion KX** | ~1000+ EUR | Enterprise rack-mount IP KVM |

---

## See Also

- [PSU Diagnostics](PSU_Diagnostics.md) -- Diagnose power supply issues that cause the crashes PiKVM helps recover from
- [Smart Power Monitoring](Smart_Power_Monitoring.md) -- Alternative remote power control using smart plugs with REST API
- [HWiNFO Remote Monitoring](HWiNFO_Remote_Monitoring.md) -- Software-based sensor monitoring for voltage and temperature tracking
