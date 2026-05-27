# 🛡️ CEH (Certified Ethical Hacker) Practical Training Guide

![CEH Badge](https://img.shields.io/badge/CEH-v12%2Fv13-red?style=for-the-badge&logo=ec-council)
![Kali Linux](https://img.shields.io/badge/Kali-Linux-557C94?style=for-the-badge&logo=kali-linux&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)

A comprehensive, hands-on training guide for preparing for the **Certified Ethical Hacker (CEH)** certification exam. This repository contains **12 practical modules** with real-world exercises using Kali Linux and vulnerable lab environments.

## What's Inside?

This guide covers the complete CEH exam syllabus with **practical, lab-based exercises** that you can perform in a safe, legal environment.

### 12 Core Modules

| Module | Topic | Tools Covered |
|--------|-------|---------------|
| 1️⃣ | **Footprinting & Reconnaissance** | WHOIS, DNS, theHarvester, Google Dorking |
| 2️⃣ | **Scanning & Enumeration** | Nmap, NSE scripts, OpenVAS, Nessus |
| 3️⃣ | **System Hacking** | John the Ripper, Hydra, Metasploit |
| 4️⃣ | **Web Application Security** | DVWA, SQLMap, Burp Suite, XSS, SQLi |
| 5️⃣ | **Network Attacks** | Ettercap, Bettercap, Aircrack-ng, MITM |
| 6️⃣ | **Social Engineering** | Gophish, SET, Credential Harvesting |
| 7️⃣ | **Malware & Trojans** | msfvenom, Meterpreter, Reverse Shells |
| 8️⃣ | **Privilege Escalation** | LinPEAS, WinPEAS, SUID exploitation |
| 9️⃣ | **Sniffing & Traffic Analysis** | Wireshark, tcpdump, Protocol Analysis |
| 🔟 | **Denial of Service** | hping3, Slowloris, SYN Flood |
| 1️⃣1️⃣ | **IDS/Firewall Evasion** | Nmap evasion, Payload encoding |
| 1️⃣2️⃣ | **Incident Response & Forensics** | Log analysis, Volatility, Memory forensics |

## Quick Start

### Prerequisites

```bash
# System Requirements
- Virtualization: VMware or VirtualBox
- RAM: 8GB+ recommended
- Disk: 50GB+ free space
- OS: Kali Linux 2024+
```

### Lab Environment Setup

```bash
# 1. Download Kali Linux
wget https://cdimage.kali.org/kali-2024.1/kali-linux-2024.1-installer-amd64.iso

# 2. Download Metasploitable 2 (Primary Target)
wget https://sourceforge.net/projects/metasploitable/files/Metasploitable2/metasploitable-linux-2.0.0.zip

# 3. Start DVWA (Web Application Testing)
docker run -d -p 80:80 vulnerables/web-dvwa

# 4. Verify connectivity
ping <metasploitable_ip>
```

## Learning Path

### Recommended Study Schedule

```
Week 1-2: Modules 1-3 (Reconnaissance, Scanning, System Hacking)
Week 3-4: Modules 4-6 (Web Apps, Network Attacks, Social Engineering)
Week 5-6: Modules 7-9 (Malware, Privilege Escalation, Sniffing)
Week 7-8: Modules 10-12 (DoS, Evasion, Forensics)
Week 9-10: Practice exams and weak area reinforcement
Week 11:  Final review and exam registration
```

### Daily Practice Routine (2-3 hours)

1. **Theory Review** (30 min) - Read module documentation
2. **Hands-On Lab** (90 min) - Execute exercises in Kali Linux
3. **Documentation** (30 min) - Document findings and techniques

## 🛠️ Tools Reference

### Essential Kali Linux Tools

```bash
# Reconnaissance
nmap, whois, dig, theHarvester, maltego

# Scanning
nmap, nikto, openvas, dirb, gobuster

# Exploitation
metasploit, sqlmap, burpsuite, hydra

# Post-Exploitation
linpeas, winpeas, mimikatz, bloodhound

# Sniffing
wireshark, tcpdump, ettercap, bettercap

# Password Cracking
john, hashcat, hydra, medusa
```

## 📖 Documentation

### Available Languages

- 🇬🇧 **English:** [CEH_Practical_Training_Guide.md](CEH_Practical_Training_Guide.md)
- 🇭🇺 **Hungarian (Magyar):** [CEH_Gyakorlati_Tananyag.md](CEH_Gyakorlati_Tananyag.md)

### Key Sections

- **Lab Setup Instructions** - Complete environment configuration
- **Step-by-Step Exercises** - Detailed commands and explanations
- **Exam Tips & Strategy** - Must-know commands and concepts
- **Practice Lab Platforms** - HackTheBox, TryHackMe, VulnHub
- **Legal & Ethical Guidelines** - Responsible disclosure and authorization

## ⚖️ Legal & Ethical Use

### CRITICAL WARNING

This guide is for **EDUCATIONAL PURPOSES ONLY**. All techniques and tools should be used:

- On your own systems
- In authorized penetration testing engagements (written permission)
- In official lab environments (Metasploitable, DVWA, HackTheBox)
- **NEVER** on unauthorized systems (illegal and unethical)

### EC-Council Code of Ethics

1. **Integrity** - Always act ethically
2. **Confidentiality** - Protect information
3. **Authorization** - Only test with permission
4. **Disclosure** - Responsible vulnerability reporting
5. **No Harm** - Do not cause damage

**Unauthorized hacking is a criminal offense** punishable by law (Computer Fraud and Abuse Act, Budapest Convention on Cybercrime).

## Exam Preparation

### CEH Exam Details

- **Questions:** 125 multiple-choice
- **Duration:** 4 hours
- **Passing Score:** 70-75% (varies)
- **Cost:** ~$1,199 USD (exam only)
- **Format:** Proctored online or at testing center

### Practice Resources

| Platform | Type | Cost | Link |
|----------|------|------|------|
| **CEH iLabs** | Official Labs | Included with course | [EC-Council](https://iclass.eccouncil.org/) |
| **HackTheBox** | CTF Platform | Free + VIP | [HTB](https://www.hackthebox.com) |
| **TryHackMe** | Guided Learning | Free + Premium | [THM](https://tryhackme.com) |
| **PentesterLab** | Web Security | Free + Pro | [PentesterLab](https://pentesterlab.com) |
| **Boson Exams** | Practice Tests | $99 | [Boson](https://www.boson.com) |

### Must-Know for Exam

```bash
# Top 20 Commands You Must Know

# Nmap
nmap -sS -sV -O -A <target>
nmap --script vuln <target>

# Metasploit
msfconsole
search <keyword>
use exploit/...
set RHOSTS <target>
exploit

# Hydra
hydra -L users.txt -P pass.txt ssh://<target>

# John the Ripper
john --wordlist=rockyou.txt hashes.txt

# SQLMap
sqlmap -u <url> --dbs --dump

# Wireshark Filters
http.request.method == "POST"
tcp.flags.syn == 1
ftp

# Aircrack-ng
airmon-ng start wlan0
airodump-ng wlan0mon
aircrack-ng -w wordlist.txt capture.cap
```

## Project Statistics

- **Total Modules:** 12
- **Exercises:** 40+
- **Tools Covered:** 50+
- **Estimated Study Time:** 80-100 hours
- **Languages:** English, Hungarian

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. **Fork** this repository
2. **Create** a feature branch (`git checkout -b feature/new-exercise`)
3. **Commit** your changes (`git commit -m 'Add new privilege escalation exercise'`)
4. **Push** to the branch (`git push origin feature/new-exercise`)
5. **Open** a Pull Request

### Contribution Ideas

- ✨ Add new exercises or modules
- 🐛 Fix errors or typos
- 📝 Improve documentation
- 🌍 Translate to other languages
- 🎥 Add video demonstrations
- Add CTF writeups

## 📞 Contact & Support

**Author:** Miklos Greczi 
**Role:** Red Hat SRE / Security Researcher 
**GitHub:** [@mgreczi](https://github.com/mgreczi) 
**LinkedIn:** [Miklos Greczi](https://linkedin.com/in/mgreczi)

### Get Help

- 💬 **Questions?** Open an [Issue](https://github.com/mgreczi/ceh-training/issues)
- 🐛 **Found a bug?** Submit a [Bug Report](https://github.com/mgreczi/ceh-training/issues/new)
- **Feature request?** Create a [Feature Request](https://github.com/mgreczi/ceh-training/issues/new)

## 📜 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

```
MIT License - Free to use, modify, and distribute
```

## 🌟 Star History

If this guide helped you, please give it a ⭐ on GitHub!

[![Star History Chart](https://api.star-history.com/svg?repos=mgreczi/ceh-training&type=Date)](https://star-history.com/#mgreczi/ceh-training&Date)

## 🔗 Related Projects

- [OSCP Preparation Guide](https://github.com/mgreczi/oscp-prep)
- [LUMINO MCP Server](https://github.com/mgreczi/lumino-mcp-server) - Kubernetes observability
- [Jira MCP Server](https://github.com/mgreczi/jira-mcp-server) - Jira automation

## Additional Resources

### Recommended Books

- 📖 "CEH Certified Ethical Hacker All-in-One Exam Guide" - Matt Walker
- 📖 "The Web Application Hacker's Handbook" - Dafydd Stuttard
- 📖 "Metasploit: The Penetration Tester's Guide" - David Kennedy

### Video Courses

- 🎥 Udemy - "The Complete Ethical Hacking Course" (Heath Adams)
- 🎥 YouTube - "HackerSploit" Channel
- 🎥 Cybrary - "CEH v12 Full Course"

### Cheat Sheets

- [Nmap Cheat Sheet](https://www.stationx.net/nmap-cheat-sheet/)
- [Metasploit Cheat Sheet](https://www.comparitech.com/net-admin/metasploit-cheat-sheet/)
- [SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

## 🏆 Acknowledgments

Special thanks to:

- **EC-Council** - For CEH certification program
- **Offensive Security** - For Kali Linux
- **Rapid7** - For Metasploit Framework
- **OWASP** - For web security resources
- **The InfoSec Community** - For sharing knowledge

---

<div align="center">

**⚡ Happy Hacking! (Ethically) ⚡**

Made with ❤️ by [Miklos Greczi](https://github.com/mgreczi)

[⬆ Back to Top](#-ceh-certified-ethical-hacker-practical-training-guide)

</div>
