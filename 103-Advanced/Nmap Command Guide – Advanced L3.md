## Nmap Command Guide – Advanced (L3 Level) - Extended Scenarios

This document expands the L3 guide with real-world professional use cases and examples.

---

### 1. Advanced Evasion, Obfuscation, and Stealth Scanning Techniques

#### 1.1 Fragmented Packets to Evade IDS

```bash
nmap -f 192.168.1.10
```

**Scenario:** During a Red Team internal recon, use fragmented packets to bypass basic IDS packet reassembly logic.

#### 1.2 Decoy Traffic Simulation

```bash
nmap -D RND:5,ME 10.0.0.15
```

**Scenario:** Simulate 5 random decoy IPs and hide true origin during pen-testing an exposed public interface.

#### 1.3 Timing Obfuscation

```bash
nmap -T0 --scan-delay 5s 192.168.1.0/24
```

**Scenario:** Slow scan across sensitive subnets to avoid triggering rate-based detection.

#### 1.4 Spoofed Source IP

```bash
nmap -S 172.16.0.100 -e eth0 target.com
```

**Scenario:** During adversary emulation, mimic traffic from a compromised trusted IP range.

#### 1.5 Encrypted Output + Obfuscation

```bash
nmap -oX - target.com | openssl enc -aes-256-cbc -out scan.enc
```

**Scenario:** Securely transport scan results without storing plaintext.

---

### 2. Firewall/IDS/IPS Testing Tactics

#### 2.1 FIN Scan to Bypass Stateless Filtering

```bash
nmap -sF target.com
```

**Scenario:** Map unfiltered ports on outdated stateless packet filters.

#### 2.2 Invalid TCP Flag Combinations

```bash
nmap --data-length 50 --badsum target.com
```

**Scenario:** Test firewall response to malformed packets or invalid checksums.

#### 2.3 NSE-Based WAF Detection

```bash
nmap --script http-waf-detect,target-ip
```

**Scenario:** Detect presence of cloud WAFs like Cloudflare, AWS Shield, etc.

#### 2.4 DNS-based WAF Evasion

```bash
nmap -sS www.target.com --dns-servers 1.1.1.1
```

**Scenario:** Override enterprise DNS resolution to bypass response-based WAF detections.

#### 2.5 Dynamic Source Port Rotation

```bash
nmap -g 20,53,443,1024 target.com
```

**Scenario:** Simulate legitimate services while probing for firewall holes.

---

### 3. Host Discovery Across Segmented and IPv6 Networks

#### 3.1 IPv6 Neighbor Discovery

```bash
nmap -6 -sU -p546 ff02::1%eth0
```

**Scenario:** Discover active IPv6 hosts via router-advertised multicast.

#### 3.2 Firewall-Bypass TTL Scanning

```bash
nmap --ttl 3 10.10.10.0/24
```

**Scenario:** Identify access points through hop-limited segments.

#### 3.3 Layered Broadcast Inventory

```bash
nmap -sU --script=broadcast-netbios-ns
```

**Scenario:** Inventory Windows machines in flat internal segments.

#### 3.4 VPN Discovery Through Leak Paths

```bash
nmap -Pn -sT --script ip-geolocation-* target.com
```

**Scenario:** Determine VPN exits or misconfigured tunnel routes.

#### 3.5 Traceroute Depth Recon

```bash
nmap -sn --traceroute 10.10.0.0/16
```

**Scenario:** Map DMZ, trust boundaries and internal links.

---

### 4. Complex NSE, Cloud, VPN Recon Use Cases

#### 4.1 Azure Public Services Exposure Check

```bash
nmap --script azure-enum target.azurewebsites.net
```

**Scenario:** Validate misconfigured open ports on Azure App Services.

#### 4.2 AWS EC2 Enumeration by IP Block

```bash
nmap -Pn -T4 -p- -iL aws-ip-ranges.txt
```

**Scenario:** Red team scans across elastic IP allocations for dev/test leaks.

#### 4.3 VPN Concentrator Fingerprinting

```bash
nmap -sU -p500,4500 --script ike-version target.com
```

**Scenario:** Identify IPsec VPN devices and supported encryption.

#### 4.4 Cloud Metadata Access Check

```bash
nmap --script http-cloud-metadata -p80 target.com
```

**Scenario:** Test for SSRF or leaked internal metadata URLs.

#### 4.5 NSE for Git Leaks

```bash
nmap --script http-git target.com
```

**Scenario:** Search for exposed .git directories and sensitive commits.

---

### 5. CI/CD, Reporting, Red/Blue Integration

#### 5.1 Scheduled Nmap via Jenkins

```bash
nmap -oX scan.xml target.com && curl -F "file=@scan.xml" https://ci.internal/upload
```

**Scenario:** Automate regular scan pipeline for exposed services audit.

#### 5.2 Blue Team SIEM Alerts via Log Parsing

```bash
nmap -oG - target.com | tee logs.txt | grep open
```

**Scenario:** Feed real-time alerting for newly exposed services to SIEM.

#### 5.3 Audit Baseline Comparison

```bash
nmap -sV -oX current.xml target.com && diff previous.xml current.xml
```

**Scenario:** Detect deviation in server services or versions.

#### 5.4 Merge with OpenVAS or Nessus Asset IDs

```bash
nmap -oX results.xml && ./map_assets.py results.xml openvas.csv
```

**Scenario:** Correlate scan data with existing vulnerability tracking.

#### 5.5 Red Team Discovery & Trigger Mapping

```bash
nmap -sV --script=banner -iL targets.txt -oX full_discovery.xml
```

**Scenario:** Feed pre-engagement scans into exploit framework or triggers.

---

### 6. VAPT Testing Use Cases

#### 6.1 Web App Recon

```bash
nmap -sS -sV --script=http-enum -p80,443 target.com
```

**Scenario:** Detect backend services, frameworks, and admin panels.

#### 6.2 API Endpoint Fuzzing

```bash
nmap --script http-methods,http-headers -p443 target.com
```

**Scenario:** Discover undocumented APIs or weak header configs.

#### 6.3 Cloud SaaS Port Exposure

```bash
nmap -Pn -T4 -p1-1000 saas-target.corp.com
```

**Scenario:** Identify old/unused dev/test ports left open externally.

#### 6.4 Endpoint/EDR Agent Discovery

```bash
nmap --script=banner -p22,135,445,3389 10.0.0.0/8
```

**Scenario:** Identify machines running security agents or RMM.

#### 6.5 Internal Network VAPT Mapping

```bash
nmap -A -sV -O -iL internal.txt --top-ports 1000 -oA fullmap
```

**Scenario:** Build comprehensive map before exploit phase.

---

**Maintained by:** [Vaishnavu C V](https://github.com/vaishnavucv)
Principal CyberSecurity Engineer | Ethical Hacker | Cyber Range Mentor
