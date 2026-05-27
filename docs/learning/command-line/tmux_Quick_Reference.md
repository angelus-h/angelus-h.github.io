---
description: tmux quick reference - terminal multiplexer, session persistence, panes, windows, key bindings, and remote server workflows.
---

# tmux Quick Reference

## Overview

**tmux** (terminal multiplexer) allows you to manage multiple terminal sessions from a single window, persist sessions across SSH disconnects, and share terminals with others.

**Why tmux:**
- Persistent sessions survive SSH disconnects
- Split terminal into multiple panes
- Multiple windows in one session
- Share sessions for pair programming
- Essential for remote server work
- Lightweight and ubiquitous on Unix/Linux

**Check Version:**
```bash
tmux -V
```

---

## 1. Core Concepts

### Session Hierarchy

```
tmux Server
Session (mywork)
Window 0: editor (active)
Pane 0: vim (active)
Pane 1: terminal
Pane 2: logs
Window 1: monitoring
Window 2: database
```

**Terminology:**
- **Session:** A collection of windows, survives disconnection
- **Window:** Like a tab, contains one or more panes
- **Pane:** A split section of a window, runs one command
- **Prefix Key:** Default `Ctrl+b`, triggers tmux commands

---

## 2. Session Management

### Create and Attach Sessions

```bash
# Start new session
tmux

# Start session with name
tmux new -s mywork

# Start session in detached mode
tmux new -s background -d

# Attach to existing session
tmux attach -t mywork
# or
tmux a -t mywork

# Attach to last session
tmux attach

# Detach from current session (inside tmux)
Ctrl+b d
```

### List and Switch Sessions

```bash
# List all sessions
tmux ls
# or
tmux list-sessions

# Inside tmux: list sessions interactively
Ctrl+b s

# Switch to session (inside tmux)
tmux switch-client -t mywork

# Kill session
tmux kill-session -t mywork

# Kill all sessions except current
tmux kill-session -a

# Rename current session (inside tmux)
Ctrl+b $
```

---

## 3. Window Management

### Create and Navigate Windows

```
Ctrl+b c - Create new window
Ctrl+b , - Rename current window
Ctrl+b & - Kill current window (prompts for confirmation)

Ctrl+b n - Next window
Ctrl+b p - Previous window
Ctrl+b 0-9 - Switch to window 0-9
Ctrl+b l - Switch to last window
Ctrl+b w - List windows (interactive)

Ctrl+b f - Find window by name
```

**Example:**
```bash
# Create 3 windows for different tasks
Ctrl+b c → Window 1 (created)
Ctrl+b , → Rename to "editor"
Ctrl+b c → Window 2 (created)
Ctrl+b , → Rename to "logs"
Ctrl+b c → Window 3 (created)
Ctrl+b , → Rename to "monitoring"

# Navigate
Ctrl+b 0 → Switch to window 0
Ctrl+b n → Next window
```

---

## 4. Pane Management

### Split Panes

```
Ctrl+b % - Split pane vertically (left/right)
Ctrl+b " - Split pane horizontally (top/bottom)
Ctrl+b x - Kill current pane

Ctrl+b arrow - Navigate between panes (↑ ↓ ← →)
Ctrl+b o - Cycle through panes
Ctrl+b ; - Toggle between last two panes

Ctrl+b q - Show pane numbers (then press number to select)
Ctrl+b z - Toggle pane zoom (fullscreen/restore)
```

### Resize Panes

```
Ctrl+b Ctrl+arrow - Resize pane (hold Ctrl, press arrow multiple times)
Ctrl+b Alt+arrow - Resize pane by 5 cells

# or resize mode
Ctrl+b :
resize-pane -L 10 # Resize left 10 cells
resize-pane -R 10 # Resize right 10 cells
resize-pane -U 5 # Resize up 5 cells
resize-pane -D 5 # Resize down 5 cells
```

### Arrange Panes

```
Ctrl+b Space - Cycle through preset layouts
Ctrl+b Alt+1 - Even horizontal layout
Ctrl+b Alt+2 - Even vertical layout
Ctrl+b Alt+3 - Main horizontal layout
Ctrl+b Alt+4 - Main vertical layout
Ctrl+b Alt+5 - Tiled layout

# Swap panes
Ctrl+b { - Swap with previous pane
Ctrl+b } - Swap with next pane
```

**Example Workflow:**
```bash
# Create editor + terminal + logs layout
Ctrl+b " → Split horizontally
Ctrl+b % → Split right pane vertically

# Result:
# 
# 
# Editor 
# 
# 
# Terminal Logs 
# 

# Zoom into editor
Ctrl+b ↑ → Move to top pane
Ctrl+b z → Zoom fullscreen
# Work in editor...
Ctrl+b z → Restore layout
```

---

## 5. Copy Mode and Scrolling

### Enter Copy Mode

```
Ctrl+b [ - Enter copy mode (scroll with arrows/PgUp/PgDn)
q - Exit copy mode

# In copy mode (vi bindings):
Space - Start selection
Enter - Copy selection
Ctrl+b ] - Paste copied text
```

### Copy Mode Navigation (vi-style)

```
h, j, k, l - Move cursor (left, down, up, right)
w, b - Move by word (forward, backward)
0, $ - Start/end of line
gg, G - Top/bottom of buffer
Ctrl+f, Ctrl+b - Page down/up
/ - Search forward
? - Search backward
n - Next search result
N - Previous search result
```

### Copy Mode Navigation (emacs-style)

```
# Enable emacs mode in ~/.tmux.conf:
# set-window-option -g mode-keys emacs

Ctrl+n, Ctrl+p - Move up/down
Ctrl+f, Ctrl+b - Move forward/backward
Alt+f, Alt+b - Move by word
Ctrl+a, Ctrl+e - Start/end of line
Ctrl+v, Alt+v - Page down/up
Ctrl+s, Ctrl+r - Search forward/backward
```

---

## 6. Command Mode

### Execute Commands

```
Ctrl+b : - Enter command mode

# Useful commands:
:new-window -n logs # Create window named "logs"
:split-window -h # Horizontal split
:split-window -v # Vertical split
:resize-pane -L 10 # Resize pane
:select-layout tiled # Apply tiled layout
:set-option -g mouse on # Enable mouse
:source-file ~/.tmux.conf # Reload config
```

---

## 7. Configuration (~/.tmux.conf)

### Basic Configuration

Create `~/.tmux.conf`:

```bash
# Change prefix key from Ctrl+b to Ctrl+a (like screen)
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# Enable mouse support
set -g mouse on

# Increase scrollback buffer
set-option -g history-limit 10000

# Start window and pane numbering at 1 (not 0)
set -g base-index 1
setw -g pane-base-index 1

# Renumber windows when one is closed
set -g renumber-windows on

# Enable vi mode in copy mode
setw -g mode-keys vi

# Enable 256 color support
set -g default-terminal "screen-256color"

# Faster command sequences (reduce escape time)
set -s escape-time 0

# Reload config with Prefix + r
bind r source-file ~/.tmux.conf \; display "Config reloaded!"

# Split panes using | and - (easier to remember)
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

# Switch panes using Alt+arrow without prefix
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

# Status bar configuration
set -g status-position bottom
set -g status-bg colour234
set -g status-fg colour137
set -g status-left ''
set -g status-right '#[fg=colour233,bg=colour241,bold] %d/%m #[fg=colour233,bg=colour245,bold] %H:%M:%S '
set -g status-right-length 50
set -g status-left-length 20

# Current window highlighting
setw -g window-status-current-format ' #I#[fg=colour250]:#[fg=colour255]#W#[fg=colour50]#F '
setw -g window-status-format ' #I#[fg=colour237]:#[fg=colour250]#W#[fg=colour244]#F '
```

### Reload Configuration

```bash
# From command line
tmux source-file ~/.tmux.conf

# Inside tmux (if you added the binding above)
Ctrl+b r
```

---

## 8. Advanced Features

### Synchronized Panes (Send to All)

```bash
# Toggle synchronization (type once, affects all panes)
Ctrl+b :
setw synchronize-panes on

# Turn off
setw synchronize-panes off

# Add to .tmux.conf for toggle keybinding:
# bind S setw synchronize-panes
# Then use: Ctrl+b S to toggle
```

**Use Case:** Run same command on multiple servers simultaneously.

### Session Management Scripts

**Auto-create session with layout:**

```bash
#!/bin/bash
# ~/bin/dev-session.sh

SESSION="development"

# Create session
tmux new-session -d -s $SESSION -n editor

# Window 0: Editor
tmux send-keys -t $SESSION:0 'cd ~/projects/myapp' C-m
tmux send-keys -t $SESSION:0 'vim' C-m

# Window 1: Servers
tmux new-window -t $SESSION:1 -n servers
tmux split-window -h -t $SESSION:1
tmux send-keys -t $SESSION:1.0 'cd ~/projects/myapp && npm run dev' C-m
tmux send-keys -t $SESSION:1.1 'cd ~/projects/myapp && npm run watch' C-m

# Window 2: Database
tmux new-window -t $SESSION:2 -n database
tmux send-keys -t $SESSION:2 'psql myapp_dev' C-m

# Attach to session
tmux attach-t $SESSION
```

Usage:
```bash
chmod +x ~/bin/dev-session.sh
~/bin/dev-session.sh
```

### Pair Programming / Shared Sessions

```bash
# On server, create shared session
tmux new -s pair-session

# Other user SSH in and attach to same session
tmux attach -t pair-session

# Both users see and control the same session
```

---

## 9. Plugins (TPM - tmux Plugin Manager)

### Install TPM

```bash
# Clone TPM
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

# Add to ~/.tmux.conf:
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'

# Initialize TPM (at bottom of .tmux.conf)
run '~/.tmux/plugins/tpm/tpm'

# Reload tmux config
tmux source ~/.tmux.conf

# Install plugins (inside tmux)
Ctrl+b I
```

### Popular Plugins

```bash
# In ~/.tmux.conf:

# Restore tmux sessions after reboot
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @continuum-restore 'on'

# Better mouse support
set -g @plugin 'nhdaly/tmux-better-mouse-mode'

# Copy to system clipboard
set -g @plugin 'tmux-plugins/tmux-yank'

# Easy pane navigation
set -g @plugin 'christoomey/vim-tmux-navigator'

# Status bar themes
set -g @plugin 'dracula/tmux'
# or
set -g @plugin 'jimeh/tmux-themepack'
set -g @themepack 'powerline/default/cyan'
```

**Plugin Commands:**
```
Ctrl+b I - Install plugins
Ctrl+b U - Update plugins
Ctrl+b Alt+u - Uninstall plugins not in list
```

---

## 10. SRE/DevOps Workflows

### Kubernetes Monitoring Layout

```bash
#!/bin/bash
# Create k8s monitoring session

SESSION="k8s-monitor"
NAMESPACE="production"

tmux new-session -d -s $SESSION -n overview

# Window 0: Cluster overview
tmux send-keys -t $SESSION:0 "watch -n 2 'kubectl get nodes'" C-m
tmux split-window -h -t $SESSION:0
tmux send-keys -t $SESSION:0.1 "watch -n 2 'kubectl top nodes'" C-m

# Window 1: Pod monitoring
tmux new-window -t $SESSION:1 -n pods
tmux send-keys -t $SESSION:1 "watch -n 2 'kubectl get pods -n $NAMESPACE'" C-m
tmux split-window -v -t $SESSION:1
tmux send-keys -t $SESSION:1.1 "kubectl logs -f deployment/myapp -n $NAMESPACE" C-m

# Window 2: Events
tmux new-window -t $SESSION:2 -n events
tmux send-keys -t $SESSION:2 "kubectl get events -n $NAMESPACE --watch" C-m

tmux attach -t $SESSION
```

### Multi-Server SSH Sessions

```bash
# Connect to multiple servers in panes
tmux new-session -d -s servers

# Create 4 panes
tmux split-window -h
tmux split-window -v
tmux select-pane -t 0
tmux split-window -v

# SSH to different servers
tmux send-keys -t servers:0.0 'ssh server01' C-m
tmux send-keys -t servers:0.1 'ssh server02' C-m
tmux send-keys -t servers:0.2 'ssh server03' C-m
tmux send-keys -t servers:0.3 'ssh server04' C-m

# Enable synchronized panes
tmux setw -t servers:0 synchronize-panes on

tmux attach -t servers
# Now type once, command runs on all 4 servers!
```

### Log Tailing Multiple Files

```bash
# Create session for log monitoring
tmux new-session -d -s logs -n application

# Split into 4 panes
tmux split-window -h -t logs:0
tmux split-window -v -t logs:0.0
tmux split-window -v -t logs:0.1

# Tail different logs
tmux send-keys -t logs:0.0 'tail -f /var/log/app/error.log' C-m
tmux send-keys -t logs:0.1 'tail -f /var/log/app/access.log' C-m
tmux send-keys -t logs:0.2 'tail -f /var/log/nginx/error.log' C-m
tmux send-keys -t logs:0.3 'tail -f /var/log/nginx/access.log' C-m

tmux attach -t logs
```

---

## 11. Useful One-Liners

```bash
# Kill all tmux sessions
tmux kill-server

# List all key bindings
tmux list-keys

# List all commands
tmux list-commands

# Capture pane content to file
tmux capture-pane -pS -1000 > output.txt

# Send keys to specific pane
tmux send-keys -t mysession:1.0 'ls -la' C-m

# Create session, run command, detach
tmux new -d -s build 'make build'

# Join pane from another window
tmux join-pane -s 2.0 -t 1.0

# Break pane into new window
tmux break-pane

# Display pane info (size, index)
tmux display-panes
```

---

## 12. Troubleshooting

### Common Issues

**Problem:** Colors look wrong in tmux

**Solution:**
```bash
# Add to ~/.tmux.conf:
set -g default-terminal "screen-256color"

# Or for true color support:
set -g default-terminal "tmux-256color"
set -ga terminal-overrides ",*256col*:Tc"
```

**Problem:** Can't scroll with mouse

**Solution:**
```bash
# Enable mouse in ~/.tmux.conf:
set -g mouse on
```

**Problem:** Escape key slow in Vim inside tmux

**Solution:**
```bash
# Add to ~/.tmux.conf:
set -s escape-time 0
```

**Problem:** Can't copy/paste to system clipboard

**Solution:**
```bash
# Install tmux-yank plugin or use:
# On macOS:
bind -T copy-mode-vi y send-keys -X copy-pipe-and-cancel 'pbcopy'

# On Linux:
bind -T copy-mode-vi y send-keys -X copy-pipe-and-cancel 'xclip -in -selection clipboard'
```

**Problem:** Session not persisting after reboot

**Solution:**
```bash
# Install tmux-resurrect + tmux-continuum plugins
# See Plugins section above
```

**Problem:** Prefix key conflicts with other apps

**Solution:**
```bash
# Change prefix in ~/.tmux.conf:
unbind C-b
set -option -g prefix C-a
bind-key C-a send-prefix
```

---

## 13. Cheat Sheet Summary

### Session Management
```
tmux new -s name - Create session
tmux a -t name - Attach to session
tmux ls - List sessions
Ctrl+b d - Detach from session
Ctrl+b $ - Rename session
```

### Windows
```
Ctrl+b c - Create window
Ctrl+b , - Rename window
Ctrl+b n/p - Next/previous window
Ctrl+b 0-9 - Switch to window 0-9
Ctrl+b w - List windows
```

### Panes
```
Ctrl+b % - Vertical split
Ctrl+b " - Horizontal split
Ctrl+b arrow - Navigate panes
Ctrl+b z - Zoom pane
Ctrl+b x - Kill pane
Ctrl+b Space - Cycle layouts
```

### Copy Mode
```
Ctrl+b [ - Enter copy mode
Space - Start selection
Enter - Copy selection
Ctrl+b ] - Paste
q - Exit copy mode
```

### Misc
```
Ctrl+b ? - List all key bindings
Ctrl+b : - Command mode
Ctrl+b t - Show clock
```

---

## 14. Resources

**Official Documentation:**
- https://github.com/tmux/tmux/wiki
- `man tmux`

**Useful Links:**
- tmux cheat sheet: https://tmuxcheatsheet.com/
- tmux Plugin Manager: https://github.com/tmux-plugins/tpm
- tmux-resurrect: https://github.com/tmux-plugins/tmux-resurrect
- Awesome tmux: https://github.com/rothgar/awesome-tmux

**Books:**
- "tmux 2: Productive Mouse-Free Development" by Brian P. Hogan

**Configuration Examples:**
- https://github.com/gpakosz/.tmux (popular tmux config)
- https://github.com/tony/tmux-config

---

**Updated:** 2026-05-23 
**Author:** Documentation Team 
**Use Case:** Terminal multiplexing, SRE, DevOps, Remote work
