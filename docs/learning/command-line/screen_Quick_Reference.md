---
description: GNU screen quick reference - terminal multiplexer, session management, detach/reattach, persistent SSH sessions, and window commands.
---

# screen Quick Reference

## Overview

**GNU screen** is a terminal multiplexer that predates tmux. It allows you to run multiple terminal sessions inside a single window, detach/reattach sessions, and persist sessions across disconnects.

**Why screen:**
- Available on virtually every Unix/Linux system
- Lighter weight than tmux
- Simple and battle-tested (since 1987)
- Default on many legacy servers
- Essential for persistent SSH sessions
- Serial console access support

**Check Version:**
```bash
screen -v
```

---

## 1. Core Concepts

### Session Management

Unlike tmux's three-level hierarchy (session → window → pane), screen has a two-level structure:

```
screen Session
 Window 0: bash
 Window 1: vim
 Window 2: logs
 Window 3: monitoring
```

**Terminology:**
- **Session:** A collection of windows that persists after disconnect
- **Window:** A single terminal instance running a command
- **Command Key:** Default `Ctrl+a`, triggers screen commands
- **Detach:** Disconnect from session (session continues running)
- **Reattach:** Reconnect to detached session

---

## 2. Session Management

### Create and Attach Sessions

```bash
# Start new session
screen

# Start session with name
screen -S mywork

# Start session running a command
screen -S build make build

# List all sessions
screen -ls
# or
screen -list

# Attach to session by name
screen -r mywork

# Attach to session by PID
screen -r 12345

# Attach to only session (if only one exists)
screen -r

# Force attach (disconnect other attached clients)
screen -dr mywork

# Detach from current session (inside screen)
Ctrl+a d
```

### Session Naming and Renaming

```bash
# Create named session
screen -S database

# Inside screen: rename session
Ctrl+a :
sessionname mynewname
Enter

# Kill session from outside
screen -S mywork -X quit

# Kill all detached sessions
screen -ls | grep Detached | cut -d. -f1 | awk '{print $1}' | xargs -I {} screen -S {} -X quit
```

---

## 3. Window Management

### Create and Navigate Windows

```
Ctrl+a c - Create new window
Ctrl+a A - Rename current window
Ctrl+a k - Kill current window (prompts for confirmation)
Ctrl+a \ - Kill all windows and terminate screen

Ctrl+a n - Next window
Ctrl+a p - Previous window
Ctrl+a 0-9 - Switch to window 0-9
Ctrl+a ' - Prompt for window number or name
Ctrl+a " - List all windows (interactive)

Ctrl+a Ctrl+a - Toggle between current and previous window
Ctrl+a w - Show window list in status line
```

**Example:**
```bash
# Create workflow
Ctrl+a c → Window 1 (created)
Ctrl+a A → Rename to "editor"
vim project.py
Ctrl+a c → Window 2 (created)
Ctrl+a A → Rename to "logs"
tail -f /var/log/app.log

# Navigate
Ctrl+a 0 → Switch to window 0
Ctrl+a n → Next window
Ctrl+a Ctrl+a → Toggle to previous window
```

---

## 4. Split Screen (Regions)

Screen supports splitting the terminal into regions (similar to tmux panes, but more limited).

### Split and Navigate

```
Ctrl+a S - Split horizontally (create region below)
Ctrl+a | - Split vertically (create region to right) [may need -v flag]
Ctrl+a Tab - Move to next region
Ctrl+a Q - Close all regions except current
Ctrl+a X - Close current region

# After splitting, new region is empty
# Switch to it and select a window:
Ctrl+a Tab - Move to new region
Ctrl+a c - Create window in region
# or
Ctrl+a 0-9 - Display existing window in region
```

**Example:**
```bash
# Create editor + terminal layout
Ctrl+a S → Split horizontally
Ctrl+a Tab → Move to bottom region
Ctrl+a c → Create new window in region

# Result (two horizontal regions):
# 
# Window 0 
# 
# Window 1 
# 
```

**Note:** Screen's split functionality is less feature-rich than tmux. Splits are not persistent across detach/reattach.

---

## 5. Copy Mode and Scrollback

### Enter Copy Mode

```
Ctrl+a [ - Enter copy mode (scrollback)
# or
Ctrl+a Esc

# In copy mode:
Space - Start selection
Space again - End selection (copies to screen's buffer)
Ctrl+a ] - Paste from screen's buffer

Esc - Exit copy mode
```

### Copy Mode Navigation

```
h, j, k, l - Move cursor (vi-style)
0, $ - Start/end of line
w, b - Move by word
Ctrl+u, Ctrl+d - Half page up/down
Ctrl+b, Ctrl+f - Full page up/down
g, G - Top/bottom of buffer
/ - Search forward
? - Search backward
n - Next search result
```

### Increase Scrollback Buffer

Add to `~/.screenrc`:
```bash
# Default is 100 lines, increase to 10000
defscrollback 10000
```

---

## 6. Configuration (~/.screenrc)

### Basic Configuration

Create `~/.screenrc`:

```bash
# Disable startup message
startup_message off

# Increase scrollback buffer
defscrollback 10000

# Enable 256 color support
term screen-256color

# Set status line
hardstatus on
hardstatus alwayslastline
hardstatus string "%{.kW}%-w%{.bW}%t [%n]%{-}%+w %=%{..G} %H %{..Y} %Y-%m-%d %c"

# Visual bell instead of audible
vbell on
vbell_msg " Wuff ---- Wuff!! "

# Alternative caption (shows window list)
caption always "%{= kw}%-w%{= BW}%n %t%{-}%+w %-= @%H - %LD %d %LM - %c"

# Detach on hangup
autodetach on

# Enable mouse tracking (limited support)
mousetrack on

# UTF-8 support
defutf8 on

# Shell
shell -$SHELL

# Monitor window activity
defmonitor on
activity "Activity in window %n"

# Bind F1-F4 to switch windows
bindkey -k k1 select 0
bindkey -k k2 select 1
bindkey -k k3 select 2
bindkey -k k4 select 3

# Bind | for vertical split (if supported)
bind | split -v

# Bind - for horizontal split
bind - split

# Easier resize
# bind = resize =
# bind + resize +1
# bind - resize -1
```

### Reload Configuration

```bash
# From command line
screen -X source ~/.screenrc

# Inside screen
Ctrl+a :
source ~/.screenrc
Enter
```

---

## 7. Advanced Features

### Logging

```bash
# Inside screen: start logging window to file
Ctrl+a H

# Default log file: screenlog.0 (in current directory)

# Add to ~/.screenrc to customize:
logfile screenlog-%Y%m%d-%n.txt
```

### Monitoring Windows

```bash
# Monitor window for activity (alerts when output appears)
Ctrl+a M

# Monitor window for silence (alerts after 30 seconds of no output)
Ctrl+a _

# Add to ~/.screenrc:
# defmonitor on # Monitor all windows by default
# activity "Window %n: activity!"
```

### Lock Screen

```bash
# Lock screen session (requires password to unlock)
Ctrl+a x

# Will prompt for user password
```

### Multi-User Mode (Pair Programming)

```bash
# On server, enable multiuser mode
Ctrl+a :
multiuser on
Enter

# Add user with access
Ctrl+a :
acladd username
Enter

# Other user connects:
screen -x mainuser/sessionname

# Revoke access:
Ctrl+a :
acldel username
```

---

## 8. SRE/DevOps Workflows

### Persistent SSH Session

```bash
# SSH to remote server
ssh server01

# Start screen session
screen -S work

# Do work in multiple windows...
Ctrl+a c # new window for logs
Ctrl+a c # new window for monitoring

# Network dies or you close laptop
# Session continues running on server!

# Later: SSH back in
ssh server01

# Reattach to session
screen -r work

# Everything still running!
```

### Serial Console Access

```bash
# Connect to serial console (common for network equipment)
screen /dev/ttyUSB0 9600

# Common baud rates: 9600, 19200, 38400, 115200

# Disconnect without closing
Ctrl+a k
y

# Or detach (keeps connection open)
Ctrl+a d
```

### Run Long-Running Job

```bash
# Start screen
screen -S build

# Run long build
make build

# Detach
Ctrl+a d

# Log out, go home, come back tomorrow

# Reattach
screen -r build

# Build still running or completed!
```

### Multi-Server Monitoring

```bash
# Create monitoring session
screen -S monitor

# Window 0: server01
ssh server01
top

# Window 1: server02
Ctrl+a c
ssh server02
top

# Window 2: server03
Ctrl+a c
ssh server03
htop

# Navigate between servers
Ctrl+a 0-2
```

---

## 9. Useful One-Liners

```bash
# Kill all detached screen sessions
screen -ls | grep Detached | cut -d. -f1 | awk '{print $1}' | xargs kill

# Create screen session, run command, detach
screen -dmS backup tar czf backup.tar.gz /data

# List all screen sessions for all users (requires root)
sudo screen -ls

# Send command to detached screen session
screen -S mywork -X stuff "ls -la\n"

# Create screen with custom window titles
screen -t "Editor" 0 vim
screen -t "Logs" 1 tail -f /var/log/app.log

# Nested screen (change command key to Ctrl+b)
screen -e ^Bb

# Start screen in background with windows
screen -dmS dev -t editor vim -t server npm start
```

---

## 10. Troubleshooting

### Common Issues

**Problem:** "Cannot open your terminal '/dev/pts/0'" error

**Solution:**
```bash
# Fix permissions
script /dev/null
screen

# Or
sudo chmod 666 /dev/pts/0
```

**Problem:** Screen sessions not listed after reboot

**Solution:**
```bash
# screen sessions don't survive reboot (unlike tmux with plugins)
# Use systemd or supervisord for persistent services
```

**Problem:** Colors broken in screen

**Solution:**
```bash
# Add to ~/.screenrc:
term screen-256color

# Or start with:
screen -T screen-256color
```

**Problem:** Can't detach (Ctrl+a d does nothing)

**Solution:**
```bash
# Make sure you're typing Ctrl+a then d (not holding both)
# Or use explicit detach:
Ctrl+a :
detach
Enter
```

**Problem:** Accidentally killed screen with Ctrl+d

**Solution:**
```bash
# Ctrl+d exits the shell, which closes the window
# If it's the last window, screen terminates
# Prevention: use 'autodetach on' in ~/.screenrc
# Can't recover - session is gone
```

**Problem:** Screen frozen/hung

**Solution:**
```bash
# Flow control stopped output (Ctrl+s)
# Resume with:
Ctrl+q

# Disable flow control in ~/.screenrc:
defflow off
```

---

## 11. screen vs tmux

| Feature | screen | tmux |
|---------|--------|------|
| **Age** | Since 1987 | Since 2007 |
| **Default Prefix** | Ctrl+a | Ctrl+b |
| **Availability** | Everywhere | Most modern systems |
| **Split Panes** | Limited, not persistent | Full support, persistent |
| **Vertical Split** | Needs flag, not all versions | Native support |
| **Configuration** | ~/.screenrc | ~/.tmux.conf |
| **Status Line** | Basic | Rich, customizable |
| **Mouse Support** | Limited | Full support |
| **Plugin System** | None | TPM (extensive) |
| **Session Persistence** | No (native) | Yes (with plugins) |
| **Scripting** | Basic | Advanced |
| **Performance** | Lighter | Slightly heavier |
| **Learning Curve** | Moderate | Moderate |

**When to use screen:**
- Legacy systems where tmux isn't available
- Serial console access
- Minimal resource usage required
- Familiarity with screen from years of use

**When to use tmux:**
- Modern systems
- Need advanced pane management
- Want plugin ecosystem
- Prefer better defaults

---

## 12. Migration from screen to tmux

If you're used to screen but want to try tmux:

**Key Binding Compatibility:**

Add to `~/.tmux.conf`:
```bash
# Use Ctrl+a instead of Ctrl+b (like screen)
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# Last window (like screen's Ctrl+a Ctrl+a)
bind-key C-a last-window

# Kill window (like screen's Ctrl+a k)
bind-key k confirm-before -p "kill-window #W? (y/n)" kill-window

# Detach (like screen's Ctrl+a d)
bind-key d detach-client
```

**Command Equivalents:**

| screen | tmux | Action |
|--------|------|--------|
| `screen -S name` | `tmux new -s name` | New session |
| `screen -r` | `tmux attach` | Attach |
| `screen -ls` | `tmux ls` | List sessions |
| `Ctrl+a d` | `Ctrl+b d` (or Ctrl+a with config) | Detach |
| `Ctrl+a c` | `Ctrl+b c` | New window |
| `Ctrl+a n/p` | `Ctrl+b n/p` | Next/previous window |
| `Ctrl+a S` | `Ctrl+b "` | Horizontal split |
| `Ctrl+a |` | `Ctrl+b %` | Vertical split |

---

## 13. Cheat Sheet Summary

### Session Management
```
screen -S name - Create named session
screen -r name - Reattach to session
screen -ls - List sessions
screen -dr name - Force reattach (detach others)
Ctrl+a d - Detach from session
```

### Windows
```
Ctrl+a c - Create window
Ctrl+a A - Rename window
Ctrl+a n/p - Next/previous window
Ctrl+a 0-9 - Switch to window 0-9
Ctrl+a " - List windows
Ctrl+a k - Kill window
Ctrl+a w - Window list in status
```

### Regions (Splits)
```
Ctrl+a S - Horizontal split
Ctrl+a | - Vertical split
Ctrl+a Tab - Next region
Ctrl+a Q - Close all except current
Ctrl+a X - Close current region
```

### Copy Mode
```
Ctrl+a [ - Enter copy mode
Space - Start/end selection
Ctrl+a ] - Paste
Esc - Exit copy mode
```

### Misc
```
Ctrl+a ? - Help (show key bindings)
Ctrl+a : - Command mode
Ctrl+a H - Start/stop logging
Ctrl+a M - Monitor window activity
Ctrl+a x - Lock screen
```

---

## 14. Resources

**Official Documentation:**
- https://www.gnu.org/software/screen/
- `man screen`
- `info screen`

**Useful Links:**
- GNU screen manual: https://www.gnu.org/software/screen/manual/
- screen FAQs: https://www.gnu.org/software/screen/faq.html
- Arch Linux screen wiki: https://wiki.archlinux.org/title/GNU_Screen

**Configuration Examples:**
- https://gist.github.com/joaopizani/2718397 (popular .screenrc)

**Books:**
- "Learning the bash Shell" (includes screen chapter)

---

**Updated:** 2026-05-23 
**Author:** Documentation Team 
**Use Case:** Terminal multiplexing, Legacy systems, Serial console, SRE
