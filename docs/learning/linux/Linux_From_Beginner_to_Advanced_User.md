# Linux: From Beginner to Advanced User - Learning Plan

**Version:** 1.0 
**Last Updated:** 2026-05-23 
**Target Audience:** Beginner users 
**Focus Distributions:** Fedora, Ubuntu 
**Estimated Duration:** 8-12 weeks (8-10 hours/week)

---

## Learning Objectives

By the end of this learning plan, you will be able to:
- Confidently use Linux distributions for daily tasks
- Use the terminal for file management and system maintenance
- Install, update software using package managers
- Perform basic system administration tasks
- Troubleshoot issues using logs and diagnostic tools
- Write bash scripts for automation
- Understand and apply networking fundamentals

---

## Prerequisites

- Working computer (or virtual machine)
- Ubuntu 24.04 LTS or Fedora 40+ installed
- Basic computer literacy (file management, web browsing)
- Ability to read English documentation

---

## 🗓️ Learning Schedule

### Module 1: First Steps in Linux (Week 1)

**Goal:** Develop comfort in the Linux environment

#### 1.1 Understanding the Graphical Interface
- **GNOME Desktop Environment** (default for Ubuntu/Fedora)
- Using the Activities overview
- Launching applications, window management
- File Manager (Files/Nautilus) basics
- Navigating Settings panel
- **Differences: Ubuntu vs Fedora**
- Ubuntu: Snap Store vs Fedora: Flatpak preference
- Kernel versions, update cycles (Ubuntu LTS stable, Fedora frequent updates)

#### 1.2 Essential Applications
- **Firefox/Chrome** - Web browsing
- **LibreOffice** - Office productivity
- **GNOME Text Editor** - Simple text editing
- **GNOME Terminal** - Command line access
- **Software Center** - Graphical software installer

#### 1.3 First Terminal Experiences
```bash
# Display current directory
pwd

# List files
ls
ls -l
ls -lah

# Change directory
cd Documents
cd ~
cd ..

# Create directory
mkdir my-project

# Create file
touch test.txt

# View help
man ls
ls --help
```

**Exercise 1.1:** Create a directory structure for your home projects:
```bash
mkdir -p ~/Projects/{web,scripts,data}
mkdir -p ~/Documents/{work,personal,learning}
```

**Exercise 1.2:** Explore `/etc`, `/home`, `/var` directories using `ls`.

---

### Module 2: Filesystem Navigation and Management (Weeks 1-2)

**Goal:** Confident file management in terminal

#### 2.1 Linux Filesystem Hierarchy
```
/ (root)
├── /bin     # Essential binaries (ls, cp, mv)
├── /boot     # Bootloader files
├── /dev     # Device files (hard disk, USB)
├── /etc     # Configuration files
├── /home     # User home directories
├── /opt     # Optional software
├── /tmp     # Temporary files
├── /usr     # User programs
└── /var     # Variable data (logs, cache)
```

#### 2.2 File Operations
```bash
# Copy
cp source.txt destination.txt
cp -r folder1 folder2 # recursive directory copy

# Move/rename
mv oldname.txt newname.txt
mv file.txt ~/Documents/

# Delete
rm file.txt
rm -r folder/ # delete directory
rm -rf dangerous/ # forced deletion (BE CAREFUL!)

# Create symbolic link
ln -s /path/to/original /path/to/link
```

**WARNING:** NEVER use `rm -rf` with `sudo` in root directory (`/`)!

#### 2.3 Viewing File Contents
```bash
# Entire file
cat file.txt

# Paginated view
less longfile.txt # q = quit

# First 10 lines
head file.txt
head -n 20 file.txt

# Last 10 lines
tail file.txt
tail -f /var/log/syslog # continuous follow (for logs)

# Count lines
wc -l file.txt
```

#### 2.4 Searching the Filesystem
```bash
# By filename
find /home -name "*.txt"
find . -type f -name "config*"

# By content
grep "error" /var/log/syslog
grep -r "TODO" ~/Projects/ # recursive search
grep -i "warning" file.txt # case-insensitive
```

**Exercise 2.1:** Navigate to `/var/log` and find the last 20 lines of system log:
```bash
cd /var/log
sudo tail -n 20 syslog
```

**Exercise 2.2:** Create a `backup.sh` script that copies the Documents folder:
```bash
#!/bin/bash
cp -r ~/Documents ~/Documents-backup-$(date +%Y%m%d)
```

---

### Module 3: Software Management and Packages (Weeks 2-3)

**Goal:** Install, update, remove software using command-line tools

#### 3.1 Ubuntu - APT (Advanced Package Tool)
```bash
# Update package index
sudo apt update

# Upgrade system
sudo apt upgrade

# Install package
sudo apt install vim
sudo apt install nginx postgresql

# Remove package
sudo apt remove nginx
sudo apt purge nginx # with configuration files

# Search package
apt search python
apt show python3

# Clean up (unused packages)
sudo apt autoremove
sudo apt clean
```

#### 3.2 Fedora - DNF (Dandified YUM)
```bash
# Update package index
sudo dnf check-update

# Upgrade system
sudo dnf upgrade

# Install package
sudo dnf install vim
sudo dnf install nginx postgresql

# Remove package
sudo dnf remove nginx

# Search package
dnf search python
dnf info python3

# Clean up
sudo dnf autoremove
sudo dnf clean all
```

#### 3.3 Universal Package Managers

**Flatpak** (both, but Fedora prefers it):
```bash
# Install (on Ubuntu)
sudo apt install flatpak

# Add Flathub
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

# Install application
flatpak install flathub org.gimp.GIMP

# Run
flatpak run org.gimp.GIMP

# Update
flatpak update
```

**Snap** (Ubuntu prefers it):
```bash
# Install (on Fedora)
sudo dnf install snapd

# Install application
sudo snap install spotify

# List
snap list

# Update
sudo snap refresh
```

#### 3.4 Installing from Source (Advanced)
```bash
# 1. Install dependencies
sudo apt install build-essential # Ubuntu
sudo dnf groupinstall "Development Tools" # Fedora

# 2. Download source
git clone https://github.com/project/repo.git
cd repo

# 3. Configure and compile
./configure
make
sudo make install
```

**Exercise 3.1:** Install the `htop` system monitor:
```bash
# Ubuntu
sudo apt install htop

# Fedora
sudo dnf install htop

# Run
htop
```

**Exercise 3.2:** Explore installed packages:
```bash
# Ubuntu - all installed packages
dpkg -l

# Fedora - all installed packages
dnf list installed

# Which package contains a file?
dpkg -S /usr/bin/vim # Ubuntu
dnf provides /usr/bin/vim # Fedora
```

---

### Module 4: Users and Permissions (Weeks 3-4)

**Goal:** User management, understanding file permissions

#### 4.1 Users and Groups
```bash
# Current user
whoami

# Logged in users
who
w

# Create user
sudo useradd -m -s /bin/bash newuser
sudo passwd newuser

# Delete user
sudo userdel -r newuser

# Create group
sudo groupadd developers

# Add user to group
sudo usermod -aG developers username
```

#### 4.2 File Permissions (rwx)
```bash
# View permissions
ls -l myfile.txt
# -rw-r--r-- 1 user group 1234 May 23 10:00 myfile.txt
# ││││││││
# │││││││└─ Others: read
# ││││││└── Others: write (none)
# │││││└─── Others: execute (none)
# ││││└──── Group: read
# │││└───── Group: write (none)
# ││└────── Group: execute (none)
# │└─────── Owner: read
# └──────── Owner: write
```

**Permission values:**
- `r` (read) = 4
- `w` (write) = 2
- `x` (execute) = 1

```bash
# Modify permissions with numbers
chmod 755 script.sh # rwxr-xr-x
chmod 644 document.txt # rw-r--r--

# Modify permissions symbolically
chmod u+x script.sh # add user execute
chmod g-w file.txt # remove group write
chmod o+r file.txt # add others read

# Change ownership
sudo chown user:group file.txt
sudo chown -R user:group directory/
```

#### 4.3 sudo and root privileges
```bash
# Run command with admin rights
sudo command

# Start root shell (NOT RECOMMENDED for daily use!)
sudo su -

# Edit with admin text editor
sudo nano /etc/hosts

# Check sudo privileges
sudo -l

# Edit sudo configuration
sudo visudo
```

**SECURITY:** NEVER run unknown scripts with `sudo`!

**Exercise 4.1:** Create a script, make it executable:
```bash
#!/bin/bash
echo "Hello, $(whoami)!"
echo "Today is $(date +%Y-%m-%d)."
```

```bash
chmod +x hello.sh
./hello.sh
```

**Exercise 4.2:** Check permissions of `/etc/passwd`:
```bash
ls -l /etc/passwd
# Why -rw-r--r--? Who can read? Who can write?
```

---

### Module 5: Processes and System Information (Weeks 4-5)

**Goal:** Monitor system, manage processes

#### 5.1 Viewing Processes
```bash
# All running processes
ps aux

# Own processes
ps -u $USER

# Process tree (hierarchy)
pstree

# Interactive process monitor
top
htop # colorful, user-friendly (needs installation)

# Find single process
ps aux | grep firefox
pgrep firefox
```

#### 5.2 Managing Processes
```bash
# Stop process (by PID)
kill 1234
kill -9 1234 # forced termination (SIGKILL)

# Stop process (by name)
pkill firefox
killall firefox

# Set process priority
nice -n 10 command # lower priority
renice -n -5 -p 1234 # higher priority

# Run in background
command &
nohup command & # continues after logout
```

#### 5.3 System Information
```bash
# System name, kernel version
uname -a
hostnamectl # detailed system info

# CPU information
lscpu
cat /proc/cpuinfo

# Memory usage
free -h
cat /proc/meminfo

# Disk usage
df -h # partitions
du -sh * # current directory size

# PCI devices (graphics card, network)
lspci

# USB devices
lsusb

# Network interfaces
ip addr
ip link show
nmcli device status # NetworkManager
```

#### 5.4 System Load
```bash
# Load average
uptime

# CPU, memory, swap usage in real-time
vmstat 1

# I/O statistics
iostat

# Network traffic
iftop # needs installation
nethogs # per-process
```

**Exercise 5.1:** Start a CPU-intensive process, monitor it:
```bash
# Terminal 1
yes > /dev/null &
PID=$!

# Terminal 2
htop # find the "yes" process

# Terminal 1 - stop
kill $PID
```

**Exercise 5.2:** Collect system information to a file:
```bash
#!/bin/bash
echo "=== System Report ===" > system-report.txt
date >> system-report.txt
uname -a >> system-report.txt
free -h >> system-report.txt
df -h >> system-report.txt
echo "Report saved to system-report.txt"
```

---

### Module 6: Text Processing and Pipes (Weeks 5-6)

**Goal:** Efficient text handling with command-line tools

#### 6.1 Text Editors
```bash
# nano - beginner-friendly
nano file.txt
# Ctrl+O save, Ctrl+X exit

# vim - advanced (worth learning!)
vim file.txt
# i = insert mode, ESC = command mode, :wq = save and quit

# emacs - alternative
emacs file.txt
```

**Vim Quick Guide:**
```
vim file.txt
i     # Insert mode (edit)
ESC    # back to Command mode
:w     # save (write)
:q     # quit
:wq    # save and quit
:q!    # quit without saving
/search  # search
dd     # delete line
yy     # copy line
p     # paste
```

#### 6.2 Pipes and Redirection
```bash
# Redirect output to file
echo "Hello" > output.txt # overwrite
echo "World" >> output.txt # append

# Input from file
sort < unsorted.txt

# Pipe - output of one command to another
cat /var/log/syslog | grep "error"
ps aux | grep firefox | wc -l

# Chain multiple commands
cat file.txt | grep "keyword" | sort | uniq

# tee - copy output to file AND screen
echo "Log entry" | tee -a logfile.txt
```

#### 6.3 Text Processing Tools
```bash
# grep - search for patterns
grep "error" logfile.txt
grep -i "warning" logfile.txt # case-insensitive
grep -v "debug" logfile.txt # inverse (NOT containing)
grep -E "error|warning" logfile.txt # regex (multiple patterns)

# sed - stream editor (substitution)
sed 's/old/new/' file.txt # first occurrence
sed 's/old/new/g' file.txt # all occurrences
sed -i 's/old/new/g' file.txt # in-place modification

# awk - column-based processing
awk '{print $1}' file.txt # first column
ps aux | awk '{print $1, $11}' # user and command

# sort - sorting
sort file.txt
sort -r file.txt # reverse
sort -n file.txt # numeric

# uniq - remove duplicates (expects SORTED input!)
sort file.txt | uniq
sort file.txt | uniq -c # with occurrence count

# cut - extract columns
cut -d: -f1 /etc/passwd # : delimiter, 1st field (usernames)

# tr - character translation
echo "hello" | tr 'a-z' 'A-Z' # lowercase -> uppercase
```

**Exercise 6.1:** Display top 10 memory-consuming processes:
```bash
ps aux --sort=-%mem | head -n 11
```

**Exercise 6.2:** Log file analysis - count ERROR lines:
```bash
grep "ERROR" /var/log/syslog | wc -l
```

**Exercise 6.3:** Sort CSV file by 2nd column:
```bash
sort -t, -k2 data.csv
```

---

### Module 7: Networking Basics (Weeks 6-7)

**Goal:** Understand basic networking concepts, diagnostics

#### 7.1 Network Interfaces
```bash
# List IP addresses
ip addr show
ip a # shorthand

# Bring interface up/down
sudo ip link set eth0 down
sudo ip link set eth0 up

# Routing table
ip route show

# DNS resolution
nslookup google.com
dig google.com
host google.com
```

#### 7.2 Testing Connectivity
```bash
# Ping - test reachability
ping google.com
ping -c 4 8.8.8.8 # send 4 packets

# Traceroute - trace route
traceroute google.com
mtr google.com # interactive traceroute

# Port availability
telnet google.com 80
nc -zv google.com 443 # netcat
```

#### 7.3 Network Connections
```bash
# Active connections
ss -tuln # TCP/UDP listening ports
netstat -tuln # older tool

# Which process uses the port?
sudo ss -tulnp | grep :80
sudo lsof -i :8080

# Firewall status
# Ubuntu (ufw)
sudo ufw status
sudo ufw enable
sudo ufw allow 22/tcp
sudo ufw deny 80/tcp

# Fedora (firewalld)
sudo firewall-cmd --state
sudo firewall-cmd --list-all
sudo firewall-cmd --add-port=8080/tcp --permanent
sudo firewall-cmd --reload
```

#### 7.4 File Download
```bash
# wget - download file
wget https://example.com/file.tar.gz
wget -O newname.tar.gz https://example.com/file.tar.gz

# curl - HTTP requests
curl https://api.github.com
curl -o output.html https://example.com
curl -I https://example.com # headers only

# rsync - synchronization/copying
rsync -av source/ destination/
rsync -avz -e ssh user@remote:/path /local/path
```

**Exercise 7.1:** Check network connectivity:
```bash
# 1. IP address
ip addr show

# 2. Default gateway
ip route | grep default

# 3. DNS working
dig google.com

# 4. Internet reachability
ping -c 3 8.8.8.8
```

**Exercise 7.2:** Explore open ports:
```bash
sudo ss -tulnp
# Which ports are listening? Which program?
```

---

### Module 8: System Services (systemd) (Weeks 7-8)

**Goal:** Manage services, understand system boot

#### 8.1 systemd Basics
```bash
# Service status
systemctl status nginx

# Start service
sudo systemctl start nginx

# Stop service
sudo systemctl stop nginx

# Restart
sudo systemctl restart nginx

# Reload configuration (without restart)
sudo systemctl reload nginx

# Enable service (autostart)
sudo systemctl enable nginx

# Disable service (no autostart)
sudo systemctl disable nginx

# List all services
systemctl list-units --type=service
systemctl list-units --type=service --state=running
```

#### 8.2 Viewing Logs (journalctl)
```bash
# All logs
journalctl

# Specific service log
journalctl -u nginx.service

# Last 50 lines
journalctl -n 50

# Continuous follow (like tail -f)
journalctl -f

# From specific time
journalctl --since "2026-05-20"
journalctl --since "1 hour ago"

# Only ERROR level messages
journalctl -p err

# Kernel messages (dmesg alternative)
journalctl -k
```

#### 8.3 System State
```bash
# Boot time
systemd-analyze
systemd-analyze blame # what slows down boot?

# Target states (like runlevels)
systemctl get-default
systemctl set-default multi-user.target # text mode
systemctl set-default graphical.target # GUI

# Reboot system
sudo systemctl reboot

# Shutdown system
sudo systemctl poweroff
```

**Exercise 8.1:** Create a simple systemd service:

`/etc/systemd/system/myservice.service`:
```ini
[Unit]
Description=My Test Service
After=network.target

[Service]
ExecStart=/usr/bin/python3 /home/user/myapp.py
Restart=always
User=user

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl start myservice
sudo systemctl status myservice
journalctl -u myservice -f
```

**Exercise 8.2:** Analyze boot time:
```bash
systemd-analyze blame | head -n 20
# Which services slow down boot?
```

---

### Module 9: Bash Scripting Basics (Weeks 8-9)

**Goal:** Automation with bash scripts

#### 9.1 Basic Script Structure
```bash
#!/bin/bash
# Shebang - tells OS which interpreter to use

# Variables
NAME="Alice"
COUNT=42

# Output
echo "Hello, $NAME!"
echo "Count: $COUNT"

# Command output to variable
CURRENT_DATE=$(date +%Y-%m-%d)
echo "Today: $CURRENT_DATE"
```

#### 9.2 Conditionals (if-else)
```bash
#!/bin/bash

AGE=25

if [ $AGE -ge 18 ]; then
echo "Adult"
elif [ $AGE -ge 13 ]; then
echo "Teenager"
else
echo "Child"
fi

# File existence
if [ -f /etc/passwd ]; then
echo "File exists"
fi

# String comparison
if [ "$USER" == "root" ]; then
echo "Running as root!"
fi
```

**Comparison operators:**
- `-eq` - equal (numbers)
- `-ne` - not equal
- `-gt` - greater than
- `-lt` - less than
- `-ge` - greater or equal
- `-le` - less or equal
- `==` - equal (strings)
- `!=` - not equal

#### 9.3 Loops
```bash
#!/bin/bash

# For loop
for i in 1 2 3 4 5; do
echo "Number: $i"
done

# Range
for i in {1..10}; do
echo $i
done

# Iterate files
for file in *.txt; do
echo "Processing $file"
done

# While loop
COUNT=0
while [ $COUNT -lt 5 ]; do
echo "Count: $COUNT"
COUNT=$((COUNT + 1))
done
```

#### 9.4 Functions
```bash
#!/bin/bash

# Define function
greet() {
local NAME=$1 # first parameter
echo "Hello, $NAME!"
}

# Call
greet "Alice"
greet "Bob"

# Return value
add() {
local RESULT=$(( $1 + $2 ))
echo $RESULT
}

SUM=$(add 5 10)
echo "Sum: $SUM"
```

#### 9.5 Handling Arguments
```bash
#!/bin/bash

echo "Script name: $0"
echo "First argument: $1"
echo "Second argument: $2"
echo "All arguments: $@"
echo "Number of arguments: $#"

# Example usage:
# ./script.sh arg1 arg2 arg3
```

**Exercise 9.1:** Create backup script:
```bash
#!/bin/bash

SOURCE="$HOME/Documents"
BACKUP_DIR="$HOME/Backups"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/backup_$DATE.tar.gz" "$SOURCE"

echo "Backup created: $BACKUP_DIR/backup_$DATE.tar.gz"
```

**Exercise 9.2:** System information script:
```bash
#!/bin/bash

echo "=== System Information ==="
echo "Hostname: $(hostname)"
echo "Kernel: $(uname -r)"
echo "Uptime: $(uptime -p)"
echo "CPU: $(lscpu | grep 'Model name' | cut -d: -f2 | xargs)"
echo "Memory: $(free -h | grep Mem | awk '{print $3 "/" $2}')"
echo "Disk: $(df -h / | tail -1 | awk '{print $3 "/" $2 " (" $5 " used)"}')"
```

---

### Module 10: Git Basics (Weeks 9-10)

**Goal:** Master version control fundamentals

#### 10.1 Git Configuration
```bash
# Global user data
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# Default editor
git config --global core.editor vim

# View configuration
git config --list
```

#### 10.2 Repository Management
```bash
# Create new repository
git init my-project
cd my-project

# Clone existing repository
git clone https://github.com/user/repo.git

# Check status
git status

# Add changes
git add file.txt
git add . # all files

# Commit
git commit -m "Add new feature"

# History
git log
git log --oneline --graph
```

#### 10.3 Branching
```bash
# Create branch
git branch feature-x

# Switch branch
git checkout feature-x
# OR
git switch feature-x

# Create and switch in one command
git checkout -b feature-y

# List branches
git branch

# Delete branch
git branch -d feature-x
```

#### 10.4 Remote Repository
```bash
# Add remote
git remote add origin https://github.com/user/repo.git

# List remotes
git remote -v

# Push (upload)
git push origin main

# Pull (download and merge)
git pull origin main

# Fetch (download only)
git fetch origin
```

**Exercise 10.1:** Create first project:
```bash
mkdir my-scripts
cd my-scripts
git init
echo "# My Scripts" > README.md
git add README.md
git commit -m "Initial commit"
```

**Exercise 10.2:** Feature branch workflow:
```bash
git checkout -b add-backup-script
# ... edit ...
git add backup.sh
git commit -m "Add backup script"
git checkout main
git merge add-backup-script
```

---

### Module 11: Advanced Topics (Optional, Weeks 10-12)

#### 11.1 Cron Jobs (Scheduled Tasks)
```bash
# Edit crontab
crontab -e

# Format: * * * * * command
# ┌───────────── minute (0-59)
# │ ┌────────── hour (0-23)
# │ │ ┌──────── day of month (1-31)
# │ │ │ ┌────── month (1-12)
# │ │ │ │ ┌──── day of week (0-7, 0 and 7 = Sunday)
# │ │ │ │ │
# * * * * * command

# Examples:
0 2 * * * /home/user/backup.sh     # Every day at 2 AM
*/15 * * * * /home/user/check.sh    # Every 15 minutes
0 0 * * 0 /home/user/weekly.sh     # Every Sunday at midnight

# List cron jobs
crontab -l
```

#### 11.2 SSH and Remote Servers
```bash
# SSH connection
ssh user@hostname

# Generate SSH key
ssh-keygen -t ed25519 -C "your@email.com"

# Copy public key to server
ssh-copy-id user@hostname

# SCP - file copy
scp file.txt user@remote:/path/to/destination
scp user@remote:/path/to/file.txt ./

# SSHFS - mount remote directory
sshfs user@remote:/path /local/mountpoint
```

#### 11.3 Compression and Archiving
```bash
# tar - archiving
tar -czf archive.tar.gz folder/ # compressed
tar -xzf archive.tar.gz     # extract

# zip/unzip
zip -r archive.zip folder/
unzip archive.zip

# gzip (single file only)
gzip file.txt   # creates file.txt.gz
gunzip file.txt.gz
```

#### 11.4 Environment Variables
```bash
# List
env
printenv

# Set (temporary)
export MY_VAR="value"

# Persistent setting (.bashrc or .bash_profile)
echo 'export PATH=$PATH:/my/custom/path' >> ~/.bashrc
source ~/.bashrc

# Modify PATH
export PATH=$PATH:/opt/myapp/bin
```

---

## Final Exam Tasks

If you can solve these independently, you're ready for advanced user level!

### Task 1: System Audit Script
Write a bash script that:
- Collects main system parameters (CPU, RAM, disk, network)
- Lists running services
- Checks disk usage and warns if >80%
- Outputs to HTML or Markdown file

### Task 2: Log Monitor
Create a script that:
- Monitors `/var/log/syslog` file
- Filters ERROR level messages
- If found, displays and sends email (or notification)

### Task 3: Backup Automation
Create a backup system that:
- Runs from cron job daily
- Saves Documents folder to tar.gz archive
- Keeps only last 7 days' backups (deletes older)
- Logs each run

### Task 4: Web Server Deploy
Install and configure an Nginx web server:
- Install using package manager
- Enable systemd service
- Create simple HTML page
- Configure firewall (allow port 80)
- Verify in browser

### Task 5: Git Workflow
Create a git repository:
- Initialize new project
- Make at least 3 commits with different files
- Use at least 2 branches
- Merge them
- Push to GitHub/GitLab

---

## Recommended Resources

### Documentation
- **Ubuntu Documentation:** https://help.ubuntu.com
- **Fedora Docs:** https://docs.fedoraproject.org
- **Linux Man Pages:** `man <command>` or https://man7.org

### Books
- "The Linux Command Line" - William Shotts (free PDF)
- "How Linux Works" - Brian Ward
- "Linux Bible" - Christopher Negus

### Online Practice
- **Linux Journey:** https://linuxjourney.com (interactive material)
- **OverTheWire Bandit:** https://overthewire.org/wargames/bandit/ (practice tasks)
- **Vim Adventures:** https://vim-adventures.com (learn Vim through games)

### Video Courses
- **YouTube - LearnLinuxTV:** https://youtube.com/@LearnLinuxTV
- **YouTube - Chris Titus Tech:** https://youtube.com/@ChrisTitusTech

### Communities
- **r/linux4noobs** (Reddit for beginners)
- **r/linuxquestions** (Reddit Q&A)
- **Ask Fedora:** https://ask.fedoraproject.org
- **Ask Ubuntu:** https://askubuntu.com

---

## 🏆 Next Steps (System Administration)

If you've mastered this material, you're ready for the next level:

1. **Linux System Administrator (Sysadmin) Course:**
- Server management (headless Ubuntu/Fedora Server)
- Automation (Ansible, Puppet, Chef)
- Containerization (Docker, Podman)
- Kubernetes basics
- Network services (DNS, DHCP, NFS, Samba)
- Backup strategies (rsync, Borg, Restic)
- Security hardening (SELinux, AppArmor)

2. **Specializations:**
- **DevOps:** CI/CD (Jenkins, GitLab CI), Infrastructure as Code
- **Cloud:** AWS, Azure, GCP fundamentals
- **Security:** Penetration testing, SOC analyst
- **Monitoring:** Prometheus, Grafana, ELK stack

---

## Self-Assessment Checklist

- [ ] Navigate terminal using `.`, `..`, `~`, absolute/relative paths
- [ ] Confidently use `ls`, `cd`, `mkdir`, `cp`, `mv`, `rm`
- [ ] Install software using APT/DNF package managers
- [ ] Understand file permissions (rwx, chmod, chown)
- [ ] Monitor processes with `ps`, `top`, `htop`
- [ ] Filter and process text with grep/sed/awk
- [ ] Chain commands with pipes
- [ ] Use vim or nano editor
- [ ] Manage services with systemctl
- [ ] Read logs with journalctl
- [ ] Write bash scripts with variables, loops, conditionals
- [ ] Use git (clone, commit, push, branch)
- [ ] Perform network diagnostics (ping, traceroute, ss)
- [ ] Connect to remote servers via SSH

---

**Congratulations if you made it this far! 🎉**

With this knowledge, you can confidently use Linux for daily work and have laid the foundation for a system administration career.

**Next recommendation:** If you're interested in DevOps/SRE direction, check out Docker and Kubernetes learning plans!

---

**Version Information:**
- **Ubuntu:** 24.04 LTS (Noble Numbat) - supported until 2034
- **Fedora:** 40+ (new version every 6 months, 13 months support)
- **Kernel:** 6.x series (modular, modern hardware support)

---

*Created by: Claude Code (claude.ai/code)* 
*Last updated: 2026-05-23*
