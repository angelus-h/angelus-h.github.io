# SSH Deep Dive Guide

## Table of Contents

1. [SSH Fundamentals](#ssh-fundamentals)
2. [Key-Based Authentication](#key-based-authentication)
3. [SSH Configuration](#ssh-configuration)
4. [Port Forwarding](#port-forwarding)
5. [SSH Agent](#ssh-agent)
6. [ProxyJump and Bastion Hosts](#proxyjump-and-bastion-hosts)
7. [SSH Tunneling and VPN](#ssh-tunneling-and-vpn)
8. [Security and Best Practices](#security-and-best-practices)
9. [Troubleshooting](#troubleshooting)
10. [Advanced Use Cases](#advanced-use-cases)

---

## SSH Fundamentals

### What is SSH?

**SSH (Secure Shell)** is an encrypted network protocol for secure remote system access and command execution over untrusted networks.

**Key Features:**
- **Encrypted Communication:** All data (passwords, commands, output) is encrypted
- **Authentication:** Password or public key-based
- **Port Forwarding:** Local/remote/dynamic tunneling
- **File Transfer:** SCP, SFTP protocols
- **Standard Port:** 22/tcp

### SSH Protocol Versions

| Version | Status | Characteristics |
|---------|--------|----------------|
| SSH-1 | **DEPRECATED** | Security vulnerabilities, DO NOT use! |
| SSH-2 | **ACTIVE** | Modern, secure, all use cases |

**Version check:**
```bash
ssh -V
# OpenSSH_9.8p1, OpenSSL 3.3.2 3 Sep 2024
```

### Basic SSH Commands

```bash
# Simple connection
ssh username@hostname

# Specify port
ssh -p 2222 username@hostname

# Execute remote command
ssh username@hostname 'uptime'

# X11 forwarding (GUI applications)
ssh -X username@hostname

# Verbose mode (debug)
ssh -v username@hostname  # -vv, -vvv for more detail
```

---

## Key-Based Authentication

### SSH Key Pair Types

| Type | Key Size | Security | Usage |
|------|----------|----------|-------|
| **RSA** | 2048, 4096 bit | Good (4096 bit) | Wide compatibility |
| **Ed25519** | 256 bit (fixed) | **Excellent** | Modern, fast, **RECOMMENDED** |
| **ECDSA** | 256, 384, 521 bit | Good | High performance |
| **DSA** | 1024 bit | **DEPRECATED** | DO NOT use! |

### 1. Generate SSH Key Pair

#### Ed25519 Key (Recommended)

```bash
# Default generation
ssh-keygen -t ed25519 -C "user@example.com"

# Custom filename and passphrase
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_work -C "user@example.com"

# No passphrase (for automation, use carefully!)
ssh-keygen -t ed25519 -N "" -f ~/.ssh/id_ed25519_automation
```

#### RSA 4096 Key (For Compatibility)

```bash
# Strong RSA key
ssh-keygen -t rsa -b 4096 -C "user@example.com"
```

**Output:**
```
Your identification has been saved in /home/user/.ssh/id_ed25519
Your public key has been saved in /home/user/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:abc123... user@example.com
```

### 2. Deploy Public Key to Remote Server

#### Automatic Deployment

```bash
# Use ssh-copy-id (easiest)
ssh-copy-id -i ~/.ssh/id_ed25519.pub username@hostname

# Custom port
ssh-copy-id -i ~/.ssh/id_ed25519.pub -p 2222 username@hostname
```

#### Manual Deployment

```bash
# Copy public key content to clipboard
cat ~/.ssh/id_ed25519.pub

# On remote server (after SSH login with password)
mkdir -p ~/.ssh
chmod 700 ~/.ssh
echo "ssh-ed25519 AAAAC3Nza... user@example.com" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

#### Deployment via SCP

```bash
# Copy public key to remote server
scp ~/.ssh/id_ed25519.pub username@hostname:/tmp/

# On remote server
ssh username@hostname
mkdir -p ~/.ssh
cat /tmp/id_ed25519.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
rm /tmp/id_ed25519.pub
```

### 3. Private Key Passphrase Management

#### Change Passphrase

```bash
# Add/change passphrase
ssh-keygen -p -f ~/.ssh/id_ed25519
```

#### Remove Passphrase (Use Carefully!)

```bash
# Remove passphrase (switch to empty passphrase)
ssh-keygen -p -f ~/.ssh/id_ed25519 -N ""
```

---

## SSH Configuration

### SSH Client Configuration (`~/.ssh/config`)

The `~/.ssh/config` file allows saving custom settings per host.

#### Basic Config Structure

```ssh-config
# ~/.ssh/config

# Default settings for all hosts
Host *
ServerAliveInterval 60
ServerAliveCountMax 3
Compression yes

# Specific host configuration
Host bastion
HostName bastion.corp.example.com
User admin
Port 22
IdentityFile ~/.ssh/id_ed25519_work
ForwardAgent yes

# Wildcard usage
Host *.example.com
User admin
IdentityFile ~/.ssh/id_rsa_example
StrictHostKeyChecking no
UserKnownHostsFile /dev/null

# Shortened name usage
Host prod-web
HostName web-01.prod.example.com
User deploy
IdentityFile ~/.ssh/id_ed25519_deploy
ProxyJump bastion
```

#### Useful Config Options

| Option | Description | Example |
|--------|-------------|---------|
| `HostName` | Actual hostname or IP | `bastion.corp.example.com` |
| `User` | SSH username | `admin` |
| `Port` | SSH port (default: 22) | `2222` |
| `IdentityFile` | Private key file path | `~/.ssh/id_ed25519` |
| `ForwardAgent` | Enable SSH agent forwarding | `yes` |
| `ProxyJump` | Use bastion host | `bastion` |
| `ServerAliveInterval` | Keepalive message frequency (seconds) | `60` |
| `ServerAliveCountMax` | Max keepalive retries | `3` |
| `Compression` | Data compression | `yes` |
| `ControlMaster` | Connection multiplexing | `auto` |
| `ControlPath` | Multiplexing socket path | `~/.ssh/sockets/%r@%h:%p` |
| `ControlPersist` | Connection persistence time | `10m` |
| `StrictHostKeyChecking` | Host key verification | `yes`/`no`/`ask` |
| `UserKnownHostsFile` | known_hosts file location | `~/.ssh/known_hosts` |

### SSH Connection Multiplexing (Performance)

Multiple SSH connections over the same socket (faster connections).

```ssh-config
Host *
ControlMaster auto
ControlPath ~/.ssh/sockets/%r@%h:%p
ControlPersist 10m
```

```bash
# Create sockets directory
mkdir -p ~/.ssh/sockets
chmod 700 ~/.ssh/sockets

# Usage: First connection builds master, subsequent reuse it
ssh bastion # Master connection (slower)
ssh bastion # Reused connection (instant)
```

### SSH Server Configuration (`/etc/ssh/sshd_config`)

**Important:** After changing server config: `sudo systemctl reload sshd`

#### Secure sshd_config Baseline

```ssh-config
# /etc/ssh/sshd_config

# Basic settings
Port 22
AddressFamily inet # IPv4 only (inet6 or any for IPv6)
ListenAddress 0.0.0.0

# Authentication
PermitRootLogin no        # Disable root login
PubkeyAuthentication yes     # Enable key-based auth
PasswordAuthentication no     # DISABLE password auth (more secure)
PermitEmptyPasswords no      # Disable empty passwords
ChallengeResponseAuthentication no

# Kerberos/GSSAPI
GSSAPIAuthentication yes     # Enable Kerberos auth
GSSAPICleanupCredentials yes

# Session settings
MaxSessions 10
MaxStartups 10:30:100       # Max concurrent connections
ClientAliveInterval 300      # Keepalive every 5 minutes
ClientAliveCountMax 2       # Drop after 2 failed keepalives

# Security
StrictModes yes          # Check file permissions
PermitUserEnvironment no
AllowTcpForwarding yes      # Enable port forwarding
X11Forwarding yes         # X11 forwarding (GUI)
PrintMotd no
AcceptEnv LANG LC_*        # Accept environment variables

# Logging
SyslogFacility AUTHPRIV
LogLevel INFO           # DEBUG for troubleshooting

# Subsystems
Subsystem sftp /usr/libexec/openssh/sftp-server
```

#### User/Group-Based Access Control

```ssh-config
# Allow only specific users
AllowUsers admin deploy developer

# Group-based allow
AllowGroups sshusers developers

# Deny users
DenyUsers guest temp

# Deny groups
DenyGroups nologin
```

#### Match Blocks (Conditional Configuration)

```ssh-config
# Extra privileges for developer group
Match Group developers
AllowTcpForwarding yes
X11Forwarding yes
PermitTunnel yes

# Special settings for automation user
Match User ansible
PubkeyAuthentication yes
PasswordAuthentication no
AllowTcpForwarding no
X11Forwarding no
ForceCommand /usr/local/bin/ansible-wrapper

# Connections from bastion host
Match Host bastion.corp.example.com
PasswordAuthentication yes
```

---

## Port Forwarding

SSH port forwarding enables tunneling TCP ports over SSH connections.

### 1. Local Port Forwarding (Local → Remote)

**Use Case:** Access remote service via local port

```
Local Machine   SSH Server    Remote Service
:8080  <--- :22 --->    :3306
(You)     (Bastion)     (MySQL DB)
```

```bash
# MySQL example: Access remote DB on localhost:8080
ssh -L 8080:mysql-server.internal:3306 user@bastion.example.com

# Usage: MySQL client connection
mysql -h 127.0.0.1 -P 8080 -u dbuser -p

# Multiple port forwardings
ssh -L 8080:mysql:3306 -L 8443:web:443 user@bastion

# Run in background
ssh -f -N -L 8080:mysql:3306 user@bastion
```

**Options:**
- `-L [bind_address:]port:host:hostport` - Local forwarding
- `-f` - Send to background
- `-N` - No remote command execution (forwarding only)

### 2. Remote Port Forwarding (Remote → Local)

**Use Case:** Make local service accessible on remote server

```
Local Service   SSH Server    Remote Users
:3000  ---> :22 <---    :8080
(Dev Server) (Public)     (Users)
```

```bash
# Expose local webserver on remote server
ssh -R 8080:localhost:3000 user@public-server.example.com

# Usage: Remote users access localhost:8080 to reach dev server

# GatewayPorts required (sshd_config)
# GatewayPorts yes # or clientspecified
```

### 3. Dynamic Port Forwarding (SOCKS Proxy)

**Use Case:** Use SSH server as SOCKS5 proxy (browser proxy)

```
Browser/App    SSH Server    Internet
SOCKS5  ---> :22 --->     Anywhere
```

```bash
# Start SOCKS5 proxy on localhost:1080
ssh -D 1080 user@proxy-server.example.com

# Usage: Browser SOCKS proxy configuration
# Firefox: Preferences → Network Settings → Manual Proxy Configuration
# SOCKS Host: localhost, Port: 1080, SOCKS v5

# curl with SOCKS proxy
curl --socks5 localhost:1080 https://api.example.com

# Run in background
ssh -f -N -D 1080 user@proxy-server
```

### 4. Port Forwarding in ~/.ssh/config

```ssh-config
Host dev-db
HostName bastion.corp.example.com
User admin
LocalForward 3306 mysql.internal:3306
LocalForward 5432 postgres.internal:5432

Host socks-proxy
HostName proxy.example.com
User admin
DynamicForward 1080
```

```bash
# Usage: Simple command, config reads the forwards
ssh dev-db
```

---

## SSH Agent

SSH Agent stores private keys in memory, eliminating the need to re-enter passphrases for each connection.

### 1. Start SSH Agent

```bash
# Start agent (usually automatic)
eval $(ssh-agent)
# Agent PID: 12345

# Check agent status
echo $SSH_AUTH_SOCK
# /tmp/ssh-XXX/agent.12345

# Stop agent
ssh-agent -k
```

### 2. Add Keys to Agent

```bash
# Add key (prompts for passphrase)
ssh-add ~/.ssh/id_ed25519

# Add all default keys
ssh-add

# Add key with time limit (1 hour)
ssh-add -t 3600 ~/.ssh/id_ed25519

# List keys in agent
ssh-add -l
# 256 SHA256:abc123... user@example.com (ED25519)

# Display fingerprint (SHA256)
ssh-add -l -E sha256

# Remove all keys from agent
ssh-add -D

# Remove specific key
ssh-add -d ~/.ssh/id_ed25519
```

### 3. SSH Agent Forwarding

Agent forwarding allows using local SSH agent on remote servers (for chained SSH connections).

**Risks:**
- Use only on trusted servers!
- Root on remote server can access agent socket

```bash
# Enable agent forwarding
ssh -A user@bastion.example.com

# In config file
Host bastion
ForwardAgent yes
```

**Use Case:**
```
Local Machine → Bastion (Agent Forward) → Internal Server
(SSH Agent)    (Uses Local Agent)    (No keys needed)
```

```bash
# Locally
ssh -A bastion

# From bastion (uses local agent)
ssh internal-server # Doesn't prompt for passphrase!
```

### 4. SSH Agent Systemd Service (Auto-Start)

**Fedora/RHEL systemd user service:**

```bash
# Check user systemd agent service
systemctl --user status ssh-agent

# Enable auto-start
systemctl --user enable ssh-agent
systemctl --user start ssh-agent

# Set agent socket environment variable (~/.bashrc or ~/.zshrc)
export SSH_AUTH_SOCK="${XDG_RUNTIME_DIR}/ssh-agent.socket"
```

---

## ProxyJump and Bastion Hosts

### ProxyJump (Modern Solution)

ProxyJump enables SSH connections through intermediate (bastion) servers.

```bash
# Simple proxyjump
ssh -J bastion.example.com user@internal-server.local

# Multiple bastion hops
ssh -J bastion1,bastion2 user@target

# In config file
Host internal-server
HostName internal-server.local
User admin
ProxyJump bastion.example.com
```

**Advantages:**
- No agent forwarding needed (more secure)
- Simpler syntax
- Multiple hop support

### ProxyCommand (Legacy Solution)

```ssh-config
Host internal-server
HostName internal-server.local
User admin
ProxyCommand ssh -W %h:%p bastion.example.com
```

### Bastion Hosts Best Practices

```ssh-config
# ~/.ssh/config - Bastion usage example

# Bastion host
Host bastion
HostName bastion.corp.example.com
User admin
IdentityFile ~/.ssh/id_ed25519_bastion
ForwardAgent no # Security: Don't forward agent!

# Internal servers through bastion
Host *.internal
User admin
IdentityFile ~/.ssh/id_ed25519_internal
ProxyJump bastion
StrictHostKeyChecking yes

# Specific internal host
Host prod-db
HostName db-01.internal
User dbadmin
ProxyJump bastion
LocalForward 5432 localhost:5432
```

---

## SSH Tunneling and VPN

### SSH Tunnel as VPN (sshuttle)

`sshuttle` is an SSH-based VPN solution (doesn't require root on remote server).

```bash
# Install sshuttle
sudo dnf install sshuttle

# Full VPN to remote network
sshuttle -r user@bastion 10.0.0.0/8

# Specific IP ranges
sshuttle -r user@bastion 10.0.1.0/24 10.0.2.0/24

# DNS also through VPN
sshuttle --dns -r user@bastion 0.0.0.0/0

# Run in background as daemon
sshuttle -D -r user@bastion 10.0.0.0/8
```

### TUN/TAP Tunnel (PermitTunnel)

**Server configuration (`/etc/ssh/sshd_config`):**
```ssh-config
PermitTunnel yes
```

**Client:**
```bash
# Layer 3 tunnel (TUN)
ssh -w 0:0 user@server

# Interface configuration (requires root)
sudo ip addr add 10.10.10.1/30 dev tun0
sudo ip link set tun0 up

# On remote server
sudo ip addr add 10.10.10.2/30 dev tun0
sudo ip link set tun0 up
```

---

## Security and Best Practices

### 1. SSH Key Security

```bash
# Private key permissions (CRITICAL!)
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub

# ~/.ssh directory permissions
chmod 700 ~/.ssh

# authorized_keys permissions
chmod 600 ~/.ssh/authorized_keys
```

### 2. SSH Server Hardening

```ssh-config
# /etc/ssh/sshd_config - Secure configuration

# DISABLE
PermitRootLogin no
PasswordAuthentication no
PermitEmptyPasswords no
ChallengeResponseAuthentication no
UsePAM yes

# ENABLE
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys

# LIMITS
MaxAuthTries 3
MaxSessions 5
LoginGraceTime 30
ClientAliveInterval 300
ClientAliveCountMax 2

# NETWORK
AllowUsers admin deploy
DenyUsers root guest
AllowGroups sshusers

# CRYPTO (Modern, strong algorithms)
Ciphers aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes256-ctr
MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com
KexAlgorithms curve25519-sha256,diffie-hellman-group18-sha512
HostKeyAlgorithms ssh-ed25519,rsa-sha2-512,rsa-sha2-256
```

### 3. Fail2Ban Integration (Brute-Force Protection)

```bash
# Install Fail2ban
sudo dnf install fail2ban

# Enable SSH jail
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# /etc/fail2ban/jail.local
[sshd]
enabled = true
port = ssh
logpath = /var/log/secure
maxretry = 3
bantime = 3600
findtime = 600

# Check status
sudo fail2ban-client status sshd
```

### 4. SSH Audit (Security Check)

```bash
# Install ssh-audit
pip install ssh-audit

# Audit SSH server
ssh-audit localhost

# Remote server audit
ssh-audit bastion.example.com
```

### 5. 2FA (Two-Factor Authentication)

```bash
# Google Authenticator PAM module
sudo dnf install google-authenticator

# User setup
google-authenticator
# Scan QR code, save secret key

# /etc/pam.d/sshd - PAM configuration
auth required pam_google_authenticator.so

# /etc/ssh/sshd_config
ChallengeResponseAuthentication yes
AuthenticationMethods publickey,keyboard-interactive
```

---

## Troubleshooting

### 1. SSH Debug Mode

```bash
# Verbose SSH client (-v, -vv, -vvv)
ssh -vvv user@hostname 2>&1 | grep -i "debug\|error"

# Server-side debug (syslog)
sudo tail -f /var/log/secure | grep sshd
```

### 2. Common Errors and Solutions

| Error | Cause | Solution |
|-------|-------|----------|
| `Permission denied (publickey)` | Key not in authorized_keys | `ssh-copy-id`, check permissions |
| `Connection refused` | sshd not running, firewall blocks | `systemctl status sshd`, `firewall-cmd` |
| `Host key verification failed` | known_hosts conflict | `ssh-keygen -R hostname` |
| `Connection timed out` | Network issue, wrong IP/port | `ping`, `telnet`, `nc -zv` |
| `Too many authentication failures` | Too many keys in agent | `ssh -o IdentitiesOnly=yes` |
| `Agent admitted failure to sign` | Agent doesn't have key | `ssh-add` |

### 3. Known Hosts Management

```bash
# Remove host from known_hosts
ssh-keygen -R hostname

# Delete entire known_hosts file (USE CAREFULLY!)
rm ~/.ssh/known_hosts

# Check host key fingerprint
ssh-keyscan hostname | ssh-keygen -lf -
```

### 4. SSH Connection Test Tools

```bash
# Port reachability test
nc -zv hostname 22
telnet hostname 22

# SSH daemon status
sudo systemctl status sshd

# Active SSH connections
sudo lsof -i :22
sudo ss -tnp | grep :22

# SSH config syntax check
sudo sshd -t
```

### 5. SSH Timeout Issues

```ssh-config
# Client side (~/.ssh/config)
Host *
ServerAliveInterval 60
ServerAliveCountMax 3
TCPKeepAlive yes

# Server side (/etc/ssh/sshd_config)
ClientAliveInterval 60
ClientAliveCountMax 3
TCPKeepAlive yes
```

---

## Advanced Use Cases

### 1. SSH File Transfer

#### SCP (Secure Copy)

```bash
# Copy file to remote server
scp local_file.txt user@hostname:/remote/path/

# Copy directory (recursive)
scp -r local_directory/ user@hostname:/remote/path/

# Copy remote file locally
scp user@hostname:/remote/file.txt /local/path/

# Specify port
scp -P 2222 file.txt user@hostname:/path/

# Compression
scp -C large_file.tar.gz user@hostname:/path/
```

#### SFTP (Secure File Transfer Protocol)

```bash
# Start SFTP session
sftp user@hostname

# SFTP commands
sftp> ls          # List remote directory
sftp> lls         # List local directory
sftp> cd /remote/path   # Change remote directory
sftp> lcd /local/path   # Change local directory
sftp> get remote_file.txt # Download file
sftp> put local_file.txt  # Upload file
sftp> mkdir newdir     # Create directory
sftp> rm file.txt     # Delete file
sftp> exit         # Exit
```

#### rsync over SSH

```bash
# rsync over SSH
rsync -avz -e ssh /local/path/ user@hostname:/remote/path/

# Progress bar
rsync -avz --progress -e ssh /local/path/ user@hostname:/remote/path/

# Dry run
rsync -avz --dry-run -e ssh /local/path/ user@hostname:/remote/path/

# Delete files in destination if not in source
rsync -avz --delete -e ssh /local/path/ user@hostname:/remote/path/
```

### 2. SSH Jump Hosts (Multi-Hop)

```bash
# 3-step SSH (Local → Bastion → Internal → Target)
ssh -J bastion,internal-gw target-server

# In config file
Host target-server
HostName target.internal
User admin
ProxyJump bastion,internal-gw
```

### 3. SSH Reverse Tunnel (Remote Access)

**Use Case:** Access home server from corporate network (behind NAT)

```bash
# Home server: Start reverse tunnel to public server
ssh -R 2222:localhost:22 user@public-server.example.com -N -f

# Public server: Access home server
ssh -p 2222 localhost # Connects back to home server
```

### 4. SSH ControlMaster (Fast Reconnection)

```ssh-config
Host fast-*
ControlMaster auto
ControlPath ~/.ssh/sockets/%r@%h:%p
ControlPersist 10m
```

```bash
# First connection (slow)
ssh fast-server1

# Subsequent connections (instant)
ssh fast-server1 # Reuses master connection
scp file.txt fast-server1:/tmp/ # SCP also faster
```

### 5. SSH Escape Sequences (In Active Session)

```
~. - Disconnect (if frozen)
~^Z - Background SSH session
~# - List connections
~? - Escape sequence help
```

**Usage:** Press `Enter` then `~` then command (e.g., `~.`)

### 6. SSH + Ansible/Automation

```bash
# Ansible inventory SSH configuration
[webservers]
web01 ansible_host=10.0.1.10 ansible_user=deploy ansible_ssh_private_key_file=~/.ssh/id_ed25519

# Ansible SSH parameters
[all:vars]
ansible_ssh_common_args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'
```

### 7. SSH X11 Forwarding (GUI Applications)

```bash
# Enable X11 forwarding
ssh -X user@hostname

# Trusted X11 forwarding (faster, less secure)
ssh -Y user@hostname

# Run GUI application on remote server
firefox &
gedit &
```

### 8. SSH + tmux/screen (Persistent Sessions)

```bash
# SSH + tmux
ssh user@hostname -t tmux attach-session -t dev || tmux new-session -s dev

# In config file
Host dev-server
HostName dev.example.com
User admin
RequestTTY yes
RemoteCommand tmux attach-session -t dev || tmux new-session -s dev
```

---

## SSH + Kubernetes/OpenShift

### 1. Bastion SSH Configuration

```ssh-config
# ~/.ssh/config - Kubernetes Bastion Access

Host bastion-stage
HostName bastion.stage.k8s.example.com
User admin
IdentityFile ~/.ssh/id_ed25519_work
ForwardAgent no
ProxyCommand none

Host *.stage.k8s
User core
IdentityFile ~/.ssh/id_ed25519_k8s
ProxyJump bastion-stage
StrictHostKeyChecking no
UserKnownHostsFile /dev/null
```

### 2. OpenShift Node SSH (oc debug node)

```bash
# OpenShift node SSH via oc debug
oc debug node/worker-01.example.com

# Chroot to node filesystem
chroot /host

# Read systemd logs
journalctl -u kubelet -f
```

### 3. Pod Port Forward via SSH Tunnel

```bash
# SSH tunnel to OpenShift service
ssh -L 8080:service-name.namespace.svc.cluster.local:80 user@bastion

# Usage
curl http://localhost:8080
```

---

## Command Summary

| Command | Description |
|---------|-------------|
| `ssh user@host` | SSH connection |
| `ssh-keygen -t ed25519` | Generate Ed25519 key pair |
| `ssh-copy-id user@host` | Deploy public key |
| `ssh-add ~/.ssh/id_ed25519` | Add key to agent |
| `ssh-add -l` | List keys in agent |
| `ssh -L 8080:remote:80 user@host` | Local port forwarding |
| `ssh -R 8080:localhost:80 user@host` | Remote port forwarding |
| `ssh -D 1080 user@host` | SOCKS5 proxy (dynamic forward) |
| `ssh -J bastion user@internal` | ProxyJump (bastion hop) |
| `scp file.txt user@host:/path/` | Copy file over SSH |
| `sftp user@host` | Start SFTP session |
| `ssh -vvv user@host` | Debug mode (verbose) |
| `ssh-keygen -R hostname` | Remove host from known_hosts |
| `sudo sshd -t` | Check sshd config syntax |

---

## References and Further Reading

- **OpenSSH Documentation:** https://www.openssh.com/manual.html
- **SSH.com Guide:** https://www.ssh.com/academy/ssh
- **the company SSH Guide:** https://access.company.internal/documentation/en-us/red_hat_enterprise_linux/9/html/securing_networks/assembly_using-secure-communications-between-two-systems-with-openssh_securing-networks
- **SSH Hardening Guide:** https://stribika.github.io/2015/01/04/secure-secure-shell.html
- **Mozilla SSH Guidelines:** https://infosec.mozilla.org/guidelines/openssh

---

**Last Updated:** 2026-05-06 
**Author:** Generated for GitHub Portfolio 
**Version:** 1.0
