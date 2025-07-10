# Cybersecurity Notes Repository

Welcome to the `cybersecurity-note` repository. This repository serves as a foundational knowledge base for cybersecurity learners, enthusiasts, and professionals. This document specifically covers **Nmap Command Notes**, focusing on its usage and command-line options relevant for L1 (Beginner), L2 (Intermediate), and L3 (Advanced) cybersecurity professionals.

---

## 🛠 Nmap Command Notes: L1-L2-L3 (Beginner Focused with Examples)

### 1. 🎯 TARGET SPECIFICATION

* `-iL <inputfilename>`: Input target list from a file

```cmd
nmap -iL targets.txt
```

**Scenario**: You have multiple IPs or domains in a file (`targets.txt`) and want to scan all of them at once.

### 2. 🌐 HOST DISCOVERY

* `-sL`: List Scan – list targets only

```cmd
nmap -sL zero.webappsecurity.com
```

**Scenario**: Just check the hostname resolution for review, without scanning ports.

* `-sn`: Ping Scan – check if host is up, no ports scanned

```cmd
nmap -sn zero.webappsecurity.com
```

**Scenario**: Confirm if the host is live before deeper scanning.

* `-Pn`: Assume host is up, skip ping check

```cmd
nmap -Pn zero.webappsecurity.com
```

**Scenario**: Use when host blocks ICMP/ping (still want port scan).

### 3. ⚙️ SCAN TECHNIQUES

* `-sS`: TCP SYN scan (stealth scan)

```cmd
nmap -sS zero.webappsecurity.com
```

**Scenario**: Identify open ports without completing TCP handshake.

* `-sT`: TCP connect scan

```cmd
nmap -sT zero.webappsecurity.com
```

**Scenario**: Useful if you're running as a normal user (non-root).

* `-sA`: ACK scan

```cmd
nmap -sA zero.webappsecurity.com
```

**Scenario**: Check firewall rules or filtering behavior.

* `-sU`: UDP scan

```cmd
nmap -sU zero.webappsecurity.com
```

**Scenario**: Identify open UDP services (like DNS, SNMP).

### 4. 🚪 PORT SPECIFICATION AND SCAN ORDER

* `-p <ports>`: Scan specific ports

```cmd
nmap -p 80,443 zero.webappsecurity.com
```

**Scenario**: Limit scan to known web service ports.

* `--exclude-ports <ports>`: Skip specific ports

```cmd
nmap --exclude-ports 22,25 zero.webappsecurity.com
```

**Scenario**: Avoid scanning SSH and mail ports.

* `-F`: Fast scan (limited ports)

```cmd
nmap -F zero.webappsecurity.com
```

**Scenario**: Quick scan of the most common ports.

* `-r`: Scan ports sequentially

```cmd
nmap -r zero.webappsecurity.com
```

**Scenario**: Avoid randomization for analysis or teaching purposes.

### 5. 🔍 SERVICE/VERSION DETECTION

* `-sV`: Detect service versions

```cmd
nmap -sV zero.webappsecurity.com
```

**Scenario**: Understand what software/version is running on open ports.

* `--version-intensity <level>`

```cmd
nmap -sV --version-intensity 2 zero.webappsecurity.com
```

**Scenario**: Perform lighter version detection to avoid detection.

* `--version-light`

```cmd
nmap -sV --version-light zero.webappsecurity.com
```

**Scenario**: Use the most common probes only.

* `--version-all`

```cmd
nmap -sV --version-all zero.webappsecurity.com
```

**Scenario**: Perform a full and deep version probe.

* `--version-trace`

```cmd
nmap -sV --version-trace zero.webappsecurity.com
```

**Scenario**: See how Nmap tries to identify each service.

### 6. 📜 SCRIPT SCAN

* `-sC`: Run default scripts

```cmd
nmap -sC zero.webappsecurity.com
```

**Scenario**: Run built-in checks (like SSH version, HTTP headers).

* `--script=<script>`: Run specific NSE script

```cmd
nmap --script=http-title zero.webappsecurity.com
```

**Scenario**: Extract the page title from the site (simple recon).

### 7. ⏱ TIMING AND PERFORMANCE

* `-T<0-5>`: Set timing (0 = paranoid, 5 = insane)

```cmd
nmap -T4 zero.webappsecurity.com
```

**Scenario**: Faster scan suitable for internal testing.

### 8. 🧠 OS DETECTION

* `-O`: Try to identify OS

```cmd
nmap -O zero.webappsecurity.com
```

**Scenario**: Helps understand what operating system is running (Linux/Windows).

### 9. 📄 OUTPUT OPTIONS

* `-oN`, `-oX`, `-oS`, `-oG`: Output formats

```cmd
nmap -oN nmap_scan.txt zero.webappsecurity.com
```

**Scenario**: Save your scan result to a file.

* `-v`, `-vv`, `-vvv`: Increase verbosity

```cmd
nmap -vv zero.webappsecurity.com
```

**Scenario**: Useful during live scans for detailed output.

### 10. 🧩 MISCELLANEOUS

* `-A`: Aggressive mode (OS detect, version, scripts, traceroute)

```cmd
nmap -A zero.webappsecurity.com
```

**Scenario**: Perform an all-in-one detailed scan for beginners to understand capabilities.

---

## 📌 Purpose

This collection is part of a broader cybersecurity knowledge base tailored for:

* SOC Analysts (L1)
* Vulnerability Assessors & Penetration Testers (L2)
* Advanced Red/Blue Team Practitioners (L3)

Stay tuned as more tools and tactical references will be added to this repository in future releases.

> *"Master the command-line, master the network."*

---

## 📁 Repository Structure (Upcoming)

```bash
cybersecurity-note/
├── nmap-notes.md
└── README.md
```

---

## 🤝 Contributions

Feel free to raise a PR to contribute additional tool notes or suggest improvements.

---

## 🔐 License

This repository is licensed under MIT. Use it wisely and ethically.

---

Maintained by: **@hackwithvyshu** | Principal CyberSecurity Engineer
