# Nmap Command Guide – Intermediate (L2 Level)

This document provides a comprehensive guide to Nmap usage for L2-level cybersecurity professionals. It is tailored for roles such as SOC Tier-2 Analysts, Vulnerability Assessment Engineers, and Penetration Testers, focusing on realistic use cases and advanced scanning techniques.

---

## Targeting and Host Enumeration

### Scan a Specific IP Range or Subnet

```bash
nmap 192.168.1.0/24
```

Use to discover all hosts on a class C network.

### Load Targets from a File

```bash
nmap -iL targets.txt
```

Useful in automation or when reusing results from earlier discovery phases.

### Skip Hosts Listed in a File

```bash
nmap --exclude 192.168.1.100,192.168.1.200
```

Exclude specific hosts during broad subnet scans.

### Randomize Target Scan Order

```bash
nmap --randomize-hosts -iL targets.txt
```

Avoid pattern detection in enterprise environments.

### List Targets Without Scanning

```bash
nmap -sL 10.0.0.0/24
```

Validates input structure and DNS resolution pre-scan.

---

## Host Discovery in Restricted Networks

### ARP Ping Scan (Local Only)

```bash
nmap -PR 192.168.0.0/24
```

Quick way to detect live hosts within LAN.

### Disable Ping Checks

```bash
nmap -Pn 10.10.10.10
```

Used where ICMP is blocked by firewall.

### TCP SYN Ping

```bash
nmap -PS22,80,443 192.168.1.0/24
```

Detect hosts based on TCP response from known open ports.

### ICMP Echo, Timestamp, and Address Mask

```bash
nmap -PE -PP -PM 192.168.1.0/24
```

Layered ICMP probes for broad detection.

### IPv6 Discovery

```bash
nmap -6 -sn fe80::/64
```

Scans hosts in IPv6-enabled networks.

---

## Scan Techniques and Modes

### SYN Scan

```bash
sudo nmap -sS 192.168.0.10
```

Standard stealth scan for TCP services.

### UDP Scan

```bash
sudo nmap -sU -p 53,161 192.168.1.10
```

Identifies DNS, SNMP, and other UDP-based services.

### Connect Scan

```bash
nmap -sT 10.0.0.5
```

Fallback method when raw socket access is unavailable.

### ACK Scan

```bash
nmap -sA 192.168.1.100
```

Used to map firewall rules (filtered vs unfiltered ports).

### Idle Scan

```bash
nmap -sI zombiehost 192.168.0.10
```

Scan via third-party idle host for anonymity.

---

## Port Management and Performance

### Full Port Range

```bash
nmap -p- 192.168.1.50
```

Scans all 65535 ports.

### Top Ports by Relevance

```bash
nmap --top-ports 100 192.168.0.5
```

Efficient enumeration using Nmap's frequency database.

### Avoid Commonly Monitored Ports

```bash
nmap -p 1-65535 --exclude-ports 22,3389
```

Avoid triggering security alerts on critical services.

### Sequential Scan

```bash
nmap -r -p1-1000 10.10.10.10
```

Disables random port order to assist with pattern-based analysis.

### Quick Tuning

```bash
nmap -F -T4 192.168.1.1
```

Fast scan using default top 100 ports with aggressive timing.

---

## Version Detection and Fingerprinting

### Standard Version Scan

```bash
nmap -sV 192.168.1.100
```

Used to determine service versions.

### Intense Version Probe

```bash
nmap -sV --version-intensity 9 192.168.1.100
```

Deep detection of evasive or lightly fingerprinted services.

### Light Version Scan

```bash
nmap -sV --version-light 192.168.0.10
```

Faster scans with less intrusive probing.

### Enable Service Guessing

```bash
nmap -sV --version-all 10.0.0.5
```

Enables all probes to maximize coverage.

### Debug Version Fingerprinting

```bash
nmap -sV --version-trace 192.168.1.15
```

Used to understand probe behavior in debugging or learning mode.

---

## NSE Script Scanning

### Default Script Scan

```bash
nmap -sC -sV 192.168.1.10
```

Performs default info gathering.

### Vulnerability Checks

```bash
nmap --script vuln -p 80,443 192.168.0.10
```

Looks for publicly known vulnerabilities.

### SSL Enumeration

```bash
nmap --script ssl-enum-ciphers -p 443 10.10.10.5
```

Audits HTTPS services for weak cipher suites.

### SMB Security Mode

```bash
nmap --script smb-security-mode -p 445 192.168.1.30
```

Identifies SMB signing, authentication policies.

### HTTP Headers Review

```bash
nmap --script http-headers -p 80,443 10.0.0.15
```

Pulls and reviews HTTP response headers.

---

## Timing and Reliability Options

### Timing Template (Balanced)

```bash
nmap -T3 -p 22,80 192.168.1.10
```

Safe for external enterprise scans.

### Maximum Retries

```bash
nmap --max-retries 2 192.168.1.1
```

Reduces latency from non-responsive hosts.

### Host Timeout

```bash
nmap --host-timeout 30s 192.168.1.5
```

Ensures scans exit early for slow or filtered hosts.

### Parallel Host Scan

```bash
nmap --min-parallelism 5 --max-parallelism 20 192.168.0.0/24
```

Manages resource allocation in large-scale scans.

### Scan Delay and Jitter

```bash
nmap --scan-delay 100ms --max-rate 1000 192.168.0.1
```

Useful for IDS evasion or rate-controlled environments.

---

## OS Detection

### Passive OS Fingerprint

```bash
nmap -O 192.168.1.1
```

Guesses OS based on TCP/IP behavior.

### OS Detection with Aggressive Scan

```bash
nmap -A 10.0.0.1
```

Combines multiple scan types for full fingerprinting.

### Disable DNS During OS Scan

```bash
nmap -O -n 192.168.1.10
```

Speeds up scanning and avoids revealing queries.

### Compare Against Known Signatures

```bash
nmap -O --osscan-guess 192.168.0.15
```

Tries harder to match based on incomplete response sets.

### Log Detected OS to File

```bash
nmap -O -oN os_output.txt 192.168.1.20
```

Maintains OS info for audit logs or reports.

---

## Output and Reporting

### Grepable Output

```bash
nmap -oG live_hosts.txt 192.168.1.0/24
```

Quick parsing using grep/awk tools.

### Normal Output for Reports

```bash
nmap -oN nmap_scan.txt 192.168.0.1
```

Readable and useful in assessments.

### XML Format

```bash
nmap -oX results.xml 192.168.1.100
```

Used in automation pipelines.

### Combined Output Formats

```bash
nmap -oA fullscan 192.168.0.5
```

Generates `.nmap`, `.xml`, and `.gnmap` in one command.

### JSON via XML Convertors

Use `xsltproc`, `xml2json`, or third-party dashboards to visualize and process Nmap outputs.

---

## Maintained by

**[Vaishnavu C V](https://github.com/vaishnavucv)**
Principal CyberSecurity Engineer | Ethical Hacker | Cyber Range Mentor
