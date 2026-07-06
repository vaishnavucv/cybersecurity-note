# Nmap Practical Examples — `zero.webappsecurity.com`

> **Target**: `zero.webappsecurity.com` — A deliberately vulnerable web application hosted for authorized security training.  
> **Authorization note**: `zero.webappsecurity.com` is a public intentionally-vulnerable practice target. Scanning it for educational purposes is permitted. Always respect rate limits and do not disrupt the service.

---

## Table of Contents

1. [Section 3 — Target Specification](#section-3--target-specification)
2. [Section 4 — Host Discovery](#section-4--host-discovery)
3. [Section 6 — Scan Techniques](#section-6--scan-techniques)
4. [Section 7 — Port Specification and Scan Order](#section-7--port-specification-and-scan-order)
5. [Section 8 — Service and Version Detection](#section-8--service-and-version-detection)
6. [Section 9 — OS Detection](#section-9--os-detection)
7. [Section 10 — Nmap Scripting Engine (NSE)](#section-10--nmap-scripting-engine-nse)
8. [Section 11 — Timing and Performance](#section-11--timing-and-performance)
9. [Section 12 — Output and Reporting](#section-12--output-and-reporting)
10. [Section 15 — Firewall and IDS Validation Options](#section-15--firewall-and-ids-validation-options)
11. [Section 17 — Intermediate Playbook](#section-17--intermediate-playbook)
12. [Section 19 — Service-Specific Recipes](#section-19--service-specific-recipes)
13. [Expected Output Reference](#expected-output-reference)

---

## Section 3 — Target Specification

### 3.1 Hostname Scan (Default)

```bash
nmap zero.webappsecurity.com
```

**Why each part:**

| Part | Meaning |
|---|---|
| `nmap` | The scanner binary |
| `zero.webappsecurity.com` | DNS-resolved hostname; Nmap scans all resolved IPs |

**Why use this:** First-look baseline. No flags means Nmap uses the best available scan type (SYN if root, TCP connect if not) and scans the 1000 most common TCP ports.

**Expected outcome:**
```
Starting Nmap 7.94 ( https://nmap.org )
Nmap scan report for zero.webappsecurity.com (54.243.143.117)
Host is up (0.18s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT    STATE SERVICE
80/tcp  open  http
443/tcp open  https

Nmap done: 1 IP address (1 host up) scanned in 12.45 seconds
```

---

### 3.2 List Scan — No Probes (`-sL`)

```bash
nmap -sL zero.webappsecurity.com
```

**Why each part:**

| Flag | Meaning |
|---|---|
| `-sL` | List scan — DNS resolution only, zero packets sent to target |

**Why use this:** Safe scope verification. Confirms the IP that the hostname resolves to without touching the target. Safe to run any time.

**Expected outcome:**
```
Nmap scan report for zero.webappsecurity.com (54.243.143.117)
Nmap done: 1 IP address (1 host up) scanned in 0.42 seconds
```

---

## Section 4 — Host Discovery

### 4.1 Ping Scan (`-sn`)

```bash
nmap -sn zero.webappsecurity.com
```

**Why each part:**

| Flag | Meaning |
|---|---|
| `-sn` | Discover if host is alive — stop before port scanning |

**Why use this:** Sends ICMP echo + TCP SYN/443 + TCP ACK/80. Confirms the host is online before committing to a full port scan.

**Expected outcome:**
```
Nmap scan report for zero.webappsecurity.com (54.243.143.117)
Host is up (0.17s latency).
Nmap done: 1 IP address (1 host up) scanned in 1.23 seconds
```

---

### 4.2 Skip Host Discovery (`-Pn`)

```bash
nmap -Pn -p 80,443 zero.webappsecurity.com
```

**Why each part:**

| Flag | Meaning |
|---|---|
| `-Pn` | Treat target as online — skip all discovery probes |
| `-p 80,443` | Only scan HTTP (80) and HTTPS (443) |

**Why use this:** `zero.webappsecurity.com` is hosted on AWS behind cloud infrastructure that frequently drops ICMP and discovery probes. Without `-Pn`, Nmap may report "host seems down" even when the web app is fully running. This is the most important flag for internet-facing targets.

**Expected outcome:**
```
Host discovery disabled (-Pn). All addresses will be marked 'up'.
Nmap scan report for zero.webappsecurity.com (54.243.143.117)
PORT    STATE SERVICE
80/tcp  open  http
443/tcp open  https
```

---

### 4.3 TCP SYN Discovery (`-PS80,443`)

```bash
nmap -sn -PS80,443 zero.webappsecurity.com
```

**Why each part:**

| Flag | Meaning |
|---|---|
| `-sn` | No port scan after discovery |
| `-PS80,443` | Send TCP SYN probes to ports 80 and 443 to test liveness |

**Why use this:** Web servers MUST respond to SYN on their listening ports. More reliable than ICMP echo for internet-facing hosts behind ICMP-blocking firewalls.

**Expected outcome:**
```
Nmap scan report for zero.webappsecurity.com (54.243.143.117)
Host is up (0.16s latency).
Nmap done: 1 IP address (1 host up) scanned in 0.85 seconds
```

---

### 4.4 TCP ACK Discovery (`-PA80,443`)

```bash
nmap -sn -PA80,443 zero.webappsecurity.com
```

**Why:** `-PA` sends ACK probes. Stateless firewalls may pass ACKs through where they block SYN. Reveals hosts that `-PS` misses and helps identify firewall behavior.

**Expected outcome:**
```
Host is up (0.18s latency).
```

---

### 4.5 Disable DNS Resolution (`-n`)

```bash
nmap -sn -n zero.webappsecurity.com
```

**Why:** `-n` skips reverse-DNS lookup. Faster scans and avoids DNS delays when you already know the hostname and only care about IP-level reachability.

---

## Section 6 — Scan Techniques

### 6.1 TCP Connect Scan (`-sT`) — No Root Required

```bash
nmap -sT -p 80,443,8080 zero.webappsecurity.com
```

**Why each part:**

| Flag | Meaning |
|---|---|
| `-sT` | Full 3-way TCP handshake using OS socket API — no raw packets |
| `-p 80,443,8080` | HTTP, HTTPS, and alternate web port |

**Why use this:** Works without sudo/root. Use in containers, restricted VMs, or CI pipelines where raw packet access is unavailable. The completed connections may appear in application access logs.

**Expected outcome:**
```
PORT     STATE    SERVICE
80/tcp   open     http
443/tcp  open     https
8080/tcp filtered http-proxy
```

---

### 6.2 TCP SYN Scan (`-sS`) — Needs sudo

```bash
sudo nmap -sS -p 80,443 zero.webappsecurity.com
```

**Why each part:**

| Flag | Meaning |
|---|---|
| `-sS` | Half-open SYN scan — sends SYN, reads SYN-ACK or RST, then RSTs |
| `-p 80,443` | Focus scan on web ports |

**Why use this:** The professional standard TCP scan. Faster than `-sT`, doesn't create full session log entries in most web servers, and provides the same open/closed/filtered state information. Requires raw packet privileges.

**Expected outcome:**
```
PORT    STATE SERVICE
80/tcp  open  http
443/tcp open  https
```

---

### 6.3 TCP ACK Scan (`-sA`) — Firewall Mapping

```bash
sudo nmap -sA -p 80,443 zero.webappsecurity.com
```

**Why:** `-sA` determines if ports are `filtered` (stateful firewall blocks ACK) or `unfiltered` (ACK reaches host). Reveals AWS security group and WAF filtering behavior. **NOT** used to find open services.

**Expected outcome:**
```
PORT    STATE      SERVICE
80/tcp  unfiltered http
443/tcp unfiltered https
```

---

### 6.4 TCP NULL Scan (`-sN`)

```bash
sudo nmap -sN -p 80 zero.webappsecurity.com
```

**Why:** Sends packets with no TCP flags. Open ports don't respond; closed ports send RST. Can bypass simplistic packet-inspection firewalls that only filter SYN.

**Expected outcome:**
```
PORT   STATE         SERVICE
80/tcp open|filtered http
```
*(Cloud firewall prevents clean NULL response, so result shows open|filtered)*

---

### 6.5 TCP FIN Scan (`-sF`)

```bash
sudo nmap -sF -p 80 zero.webappsecurity.com
```

**Why:** FIN packets pass through some firewalls that block SYN. Not reliable against Windows targets (which send RST for all unexpected packets).

---

### 6.6 TCP Xmas Scan (`-sX`)

```bash
sudo nmap -sX -p 80 zero.webappsecurity.com
```

**Why:** Sets FIN+PSH+URG simultaneously. Used in authorized lab settings for advanced TCP stack and filtering analysis.

---

## Section 7 — Port Specification and Scan Order

### 7.1 Single Port

```bash
nmap -p 80 zero.webappsecurity.com
```

**Why `-p 80`:** Focuses entire scan on HTTP. Fastest way to verify one specific service is reachable.

**Expected outcome:**
```
PORT   STATE SERVICE
80/tcp open  http
```

---

### 7.2 Multiple Specific Ports

```bash
nmap -p 80,443,8080,8443 zero.webappsecurity.com
```

**Why:** Comma-separated ports hit all common web variants in a single scan pass. Efficient for web-focused assessments.

**Expected outcome:**
```
PORT     STATE    SERVICE
80/tcp   open     http
443/tcp  open     https
8080/tcp filtered http-proxy
8443/tcp filtered https-alt
```

---

### 7.3 Port Range

```bash
nmap -p 1-1024 zero.webappsecurity.com
```

**Why:** Covers all 1024 well-known ports including privileged services without the overhead of a full 65535-port scan.

---

### 7.4 Fast Scan (`-F`)

```bash
nmap -F zero.webappsecurity.com
```

**Why:** `-F` selects the 100 most statistically common ports from Nmap's frequency database. Approximately 10× faster than the default 1000-port scan. Excellent for quick triage.

**Expected outcome:**
```
PORT    STATE    SERVICE
80/tcp  open     http
443/tcp open     https
[98 ports: closed/filtered — not shown]
Nmap done in 6.23 seconds
```

---

### 7.5 Top N Ports

```bash
nmap --top-ports 200 zero.webappsecurity.com
```

**Why:** `--top-ports N` gives more control than `-F`. Scanning 200 ports balances coverage and speed better for internet targets.

---

### 7.6 Show Open Ports Only (`--open`)

```bash
nmap -p 1-1024 --open zero.webappsecurity.com
```

**Why:** Removes closed and filtered port noise. Critical for internet targets where 90%+ of ports will be filtered, making output otherwise unreadable.

**Expected outcome:**
```
PORT    STATE SERVICE
80/tcp  open  http
443/tcp open  https
Nmap done in 14.22 seconds
```

---

### 7.7 Sequential Port Order (`-r`)

```bash
nmap -r -p 1-100 zero.webappsecurity.com
```

**Why:** `-r` scans ports in ascending numerical order instead of randomized. Makes packet captures and training walkthroughs much easier to follow.

---

## Section 8 — Service and Version Detection

### 8.1 Basic Version Detection (`-sV`)

```bash
nmap -sV -p 80,443 zero.webappsecurity.com
```

**Why each part:**

| Flag | Meaning |
|---|---|
| `-sV` | Send service probe payloads to open ports to identify software + version |
| `-p 80,443` | Limit probes to web ports to save time |

**Why use this:** Without `-sV` you only know port 80 is `open`. With `-sV` you learn it's `nginx 1.14.0` or `Apache httpd 2.4.41`. Version numbers map directly to CVEs and patch gap analysis.

**Expected outcome:**
```
PORT    STATE SERVICE VERSION
80/tcp  open  http    nginx
443/tcp open  ssl/http nginx
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

---

### 8.2 Light Version Detection

```bash
nmap -sV --version-light -p 80,443 zero.webappsecurity.com
```

**Why:** `--version-light` limits probes to intensity level 2 (default is 7). 2-3× faster at the cost of some fingerprinting depth. Good for large-scope scans.

---

### 8.3 High-Intensity Version Detection

```bash
nmap -sV --version-intensity 9 -p 80,443 zero.webappsecurity.com
```

**Why:** Intensity 9 tries every probe in Nmap's database. Maximizes identification of services with stripped or misleading banners.

---

### 8.4 Try All Version Probes (`--version-all`)

```bash
nmap -sV --version-all -p 80,443 zero.webappsecurity.com
```

**Why:** Equivalent to `--version-intensity 9`. Use when standard `-sV` returns `unknown` or an incorrect service identification.

---

### 8.5 Version on All Ports (`--allports`)

```bash
nmap -sV --allports -p 80,443,8080 zero.webappsecurity.com
```

**Why:** Prevents Nmap from skipping ports it normally excludes from version detection (like 9100 for printers). Ensures comprehensive coverage.

---

## Section 9 — OS Detection

### 9.1 OS Detection (`-O`)

```bash
sudo nmap -O -Pn -p 80,443 zero.webappsecurity.com
```

**Why each part:**

| Flag | Meaning |
|---|---|
| `-O` | Enable OS fingerprinting via TCP/IP stack analysis |
| `-Pn` | Skip discovery — required for cloud-hosted targets blocking ICMP |
| `-p 80,443` | Ensure open ports exist for accurate fingerprinting |

**Why use this:** OS fingerprinting compares TCP sequence numbers, window sizes, TTL values, and IP/TCP quirks against Nmap's 5000+ OS fingerprint database. Results drive asset classification and CVE scope definition.

**Expected outcome:**
```
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.10 - 4.11
Network Distance: 14 hops

OS detection performed. Please report any incorrect results.
```

---

### 9.2 Aggressive OS Guess (`--osscan-guess`)

```bash
sudo nmap -O --osscan-guess -Pn -p 80,443 zero.webappsecurity.com
```

**Why:** Forces a best-guess OS report even at <80% confidence. Useful when NAT, load balancers, or filtering prevent a clean fingerprint.

---

### 9.3 Combined OS + Version Detection

```bash
sudo nmap -O -sV -Pn -p 80,443 zero.webappsecurity.com
```

**Why:** Running `-O` and `-sV` together produces both host OS classification and per-service version information in a single efficient pass — the standard combination for VAPT enumeration.

---

## Section 10 — Nmap Scripting Engine (NSE)

### 10.1 Default Scripts (`-sC`)

```bash
nmap -sC -p 80,443 zero.webappsecurity.com
```

**Why:** `-sC` is shorthand for `--script=default`. Runs 90+ safe scripts automatically chosen based on detected service type. For HTTP ports this includes: title, redirect detection, header inspection.

**Expected outcome:**
```
PORT    STATE SERVICE
80/tcp  open  http
| http-title: zero.webappsecurity.com
443/tcp open  https
| ssl-cert: Subject: commonName=zero.webappsecurity.com
|   Not valid before: 2024-xx-xx
|_  Not valid after:  2025-xx-xx
```

---

### 10.2 HTTP Title Script

```bash
nmap --script http-title -p 80,443 zero.webappsecurity.com
```

**Why:** Pinpoints the web app name without retrieving the full page body. Confirms you're targeting the correct application.

**Expected outcome:**
```
PORT    STATE SERVICE
80/tcp  open  http
|_http-title: zero.webappsecurity.com
443/tcp open  https
|_http-title: zero.webappsecurity.com - Did not follow redirect to https://
```

---

### 10.3 HTTP Headers Script

```bash
nmap --script http-headers -p 80,443 zero.webappsecurity.com
```

**Why:** Exposes full HTTP response headers. Reveals server software, caching rules, cookie security flags, and missing security headers (HSTS, CSP, X-Frame-Options).

**Expected outcome:**
```
PORT    STATE SERVICE
80/tcp  open  http
| http-headers:
|   Server: nginx
|   X-Frame-Options: SAMEORIGIN
|   X-XSS-Protection: 1; mode=block
|   X-Content-Type-Options: nosniff
|_  (Request type: GET)
```

---

### 10.4 HTTP Methods Script

```bash
nmap --script http-methods -p 80,443 zero.webappsecurity.com
```

**Why:** Dangerous methods like PUT (file upload), DELETE (file removal), or TRACE (header reflection) indicate misconfiguration and should be explicitly blocked.

**Expected outcome:**
```
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
```

---

### 10.5 TLS Certificate (`ssl-cert`)

```bash
nmap --script ssl-cert -p 443 zero.webappsecurity.com
```

**Why:** Certificate details are required for: verifying domain coverage (SANs match hostname), checking expiry, confirming CA trust chain, and certificate pinning validation.

**Expected outcome:**
```
| ssl-cert: Subject: commonName=zero.webappsecurity.com
| Subject Alternative Name: DNS:zero.webappsecurity.com
| Issuer: commonName=Amazon RSA 2048 M02/organizationName=Amazon/countryName=US
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-xx-xx T00:00:00
|_Not valid after:  2025-xx-xx T23:59:59
```

---

### 10.6 TLS Cipher Enumeration (`ssl-enum-ciphers`)

```bash
nmap --script ssl-enum-ciphers -p 443 zero.webappsecurity.com
```

**Why:** Identifies weak cipher suites (RC4, 3DES, export-grade) and deprecated TLS versions (SSLv3, TLS 1.0, TLS 1.1). Required for PCI-DSS compliance validation and security baseline assessments.

**Expected outcome:**
```
| ssl-enum-ciphers:
|   TLSv1.2:
|     TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (ecdh_x25519) - A
|     TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (ecdh_x25519) - A
|   TLSv1.3:
|     TLS_AKE_WITH_AES_256_GCM_SHA384 (ecdh_x25519) - A
|_  least strength: A
```

---

### 10.7 HTTP robots.txt

```bash
nmap --script http-robots.txt -p 80,443 zero.webappsecurity.com
```

**Why:** Robots.txt Disallow entries inadvertently map hidden admin panels, API endpoints, and backup directories that developers intended to keep quiet.

**Expected outcome:**
```
| http-robots.txt: 1 disallowed entry
|_/login.htm
```

---

### 10.8 HTTP Security Headers

```bash
nmap --script http-security-headers -p 80,443 zero.webappsecurity.com
```

**Why:** Missing security headers are a very common web finding in OWASP Top 10 audits. Script checks for: HSTS, CSP, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy.

---

### 10.9 HTTP Server Header

```bash
nmap --script http-server-header -p 80,443 zero.webappsecurity.com
```

**Why:** The `Server:` response header reveals web server type and often version. Enables direct CVE lookup and version comparison.

---

### 10.10 Default OR Safe Scripts

```bash
nmap --script "default or safe" -p 80,443 zero.webappsecurity.com
```

**Why:** Runs all scripts in `default` category plus all in `safe` category. Broader than `-sC` alone but still avoids intrusive, brute-force, and exploit scripts.

---

### 10.11 Exclude Intrusive Scripts

```bash
nmap --script "not intrusive" -p 80,443 zero.webappsecurity.com
```

**Why:** The `not intrusive` filter runs all categories except those tagged as intrusive. Safer for production systems where only limited authorization is granted.

---

### 10.12 Vulnerability Detection (`--script vuln`)

```bash
nmap -sV --script vuln -p 80,443 zero.webappsecurity.com
```

**Why each part:**

| Flag | Meaning |
|---|---|
| `-sV` | Detect versions first so vuln scripts target the right checks |
| `--script vuln` | Run all vulnerability detection scripts |
| `-p 80,443` | Focus on the web surface |

**Why use this:** May detect Slowloris DoS susceptibility, header injection, outdated TLS configuration, and CVE-tagged vulnerabilities. **Requires explicit written authorization.** May generate IDS/SIEM alerts.

**Expected outcome:**
```
PORT    STATE SERVICE VERSION
80/tcp  open  http    nginx
| http-slowloris-check:
|   VULNERABLE: Slowloris DOS attack
|     State: LIKELY VULNERABLE
|     IDs:  CVE:CVE-2007-6750
443/tcp open  ssl/http nginx
```
*(Actual results depend on server configuration)*

---

### 10.13 Combined Web + TLS Full Recipe

```bash
nmap -sV --script http-title,http-headers,http-methods,ssl-cert,ssl-enum-ciphers \
     -p 80,443 zero.webappsecurity.com
```

**Why:** The single most useful command for web application security assessments.

| Script | Security value |
|---|---|
| `http-title` | Confirms target app identity |
| `http-headers` | Finds missing security headers, server disclosure |
| `http-methods` | Flags dangerous HTTP methods |
| `ssl-cert` | Certificate expiry, domain coverage, CA trust |
| `ssl-enum-ciphers` | Weak cipher / deprecated TLS version detection |

**Expected outcome:**
```
PORT    STATE SERVICE VERSION
80/tcp  open  http    nginx
| http-title: zero.webappsecurity.com
| http-headers: Server: nginx  X-Frame-Options: SAMEORIGIN
| http-methods: GET HEAD POST OPTIONS
443/tcp open  ssl/http nginx
| ssl-cert: commonName=zero.webappsecurity.com (valid)
| ssl-enum-ciphers: TLSv1.2/1.3 Grade: A
```

---

## Section 11 — Timing and Performance

### 11.1 Polite Timing (`-T2`)

```bash
nmap -T2 -p 80,443 zero.webappsecurity.com
```

**Why `-T2`:** `zero.webappsecurity.com` is a shared public training server. `-T2` adds inter-probe delays to:
- Stay under WAF and DDoS protection thresholds
- Avoid triggering automated blocking
- Be a responsible user of shared lab resources

Results are the same as default but scan takes 2-3× longer.

---

### 11.3 Aggressive Timing (`-T4`)

```bash
nmap -T4 -p 80,443 zero.webappsecurity.com
```

**Why `-T4`:** Reduces timeouts and retransmit windows. Fine for fast local networks or a VPS close to the target. May trigger WAF rate-limiting on strict cloud deployments.

---

### 11.4 Max Rate Control (`--max-rate 50`)

```bash
nmap --max-rate 50 -p 80,443 zero.webappsecurity.com
```

**Why:** Hard caps at 50 packets/second regardless of timing template. Ensures you don't accidentally DoS or trigger auto-blocking on a shared training platform.

---

### 11.6 Show Port Reason (`--reason`)

```bash
nmap --reason -p 80,443 zero.webappsecurity.com
```

**Expected outcome:**
```
PORT    STATE SERVICE REASON
80/tcp  open  http    syn-ack ttl 41
443/tcp open  https   syn-ack ttl 41
```

**Why `--reason` matters:**

| Reason | Meaning |
|---|---|
| `syn-ack` | Server responded with SYN-ACK → port is genuinely open |
| `rst` | Server sent RST → port is closed, host is reachable |
| `no-response` | Nothing came back → filtered by firewall/ACL |
| `admin-prohibited` | ICMP port-unreachable received → actively rejected |

---

## Section 12 — Output and Reporting

### 12.4 Save All Formats (`-oA`) — Professional Default

```bash
nmap -p 80,443 -oA ./nmap_outputs/all_formats zero.webappsecurity.com
```

**Why `-oA`:** Creates three files simultaneously:

| Extension | Format | Use |
|---|---|---|
| `.nmap` | Human-readable | Reading, notes, reports |
| `.xml` | Structured XML | Tool import (Metasploit, Faraday, Dradis) |
| `.gnmap` | Grepable | `grep open *.gnmap` shell parsing |

**Never scan without saving output.**

---

### 12.7 Packet Trace (`--packet-trace`)

```bash
nmap --packet-trace -p 80 zero.webappsecurity.com
```

**Why:** Prints every packet sent and received. Essential for:
- Learning exactly how Nmap communicates
- Troubleshooting unexpected results
- Training demonstrations

**Expected outcome (excerpt):**
```
SENT (0.1500s) TCP 192.168.1.x:54312 > 54.243.143.117:80 S ...
RCVD (0.3200s) TCP 54.243.143.117:80 > 192.168.1.x:54312 SA ...
PORT   STATE SERVICE
80/tcp open  http
```

---

## Section 15 — Firewall and IDS Validation Options

### 15.1 Source Port Manipulation

```bash
nmap --source-port 53 -p 80,443 zero.webappsecurity.com
```

**Why:** Validates whether the target's perimeter firewall has rules that incorrectly trust traffic originating from port 53 (DNS). A classic misconfiguration that allows firewall bypass.

---

### 15.2 Payload Marker (`--data-string`)

```bash
nmap --data-string "AuthorizedSecurityTest" -p 80 zero.webappsecurity.com
```

**Why:** Embeds an ASCII string in packet payloads. When SOC/SIEM teams review IDS alerts from authorized scans, the marker identifies it as sanctioned activity. Professional practice for purple-team exercises.

---

### 15.3 Bad Checksum Test (`--badsum`)

```bash
nmap --badsum -p 80 zero.webappsecurity.com
```

**Why:** A correctly implemented TCP/IP stack silently drops packets with invalid checksums. If any response is received, it means a middleware device (firewall, load balancer, IDS) generated it — revealing the presence and behavior of network middleboxes.

**Expected outcome if real server:**
```
PORT   STATE    SERVICE
80/tcp filtered http
```

**If middlebox detected:** A response arrives despite bad checksum.

---

## Section 17 — Intermediate Playbook

### Complete Web Assessment Command

```bash
nmap -Pn -p 80,443,8080,8443 \
     --script http-title,http-headers,ssl-cert,ssl-enum-ciphers \
     zero.webappsecurity.com
```

**Why each flag:**

| Flag / Argument | Reason |
|---|---|
| `-Pn` | Cloud-hosted target drops ICMP — treat as online to avoid false "down" report |
| `-p 80,443,8080,8443` | All common web and HTTPS ports including non-standard |
| `--script http-title` | Confirm target app identity |
| `--script http-headers` | Security header audit |
| `--script ssl-cert` | Certificate validity and domain coverage |
| `--script ssl-enum-ciphers` | TLS protocol and cipher strength grading |

**Expected outcome:**
```
Nmap scan report for zero.webappsecurity.com (54.243.143.117)
PORT     STATE    SERVICE
80/tcp   open     http
| http-title: zero.webappsecurity.com
| http-headers: Server: nginx   X-Frame-Options: SAMEORIGIN
443/tcp  open     https
| ssl-cert: commonName=zero.webappsecurity.com (valid, Amazon CA)
| ssl-enum-ciphers: TLSv1.2 Grade: A  TLSv1.3 Grade: A
8080/tcp filtered http-proxy
8443/tcp filtered https-alt
```

---

## Section 19 — Service-Specific Recipes

### 19.2 TLS Certificate Review

```bash
nmap -Pn -p 443 --script ssl-cert zero.webappsecurity.com
```

**Findings to look for:**
- Subject CN matches the hostname
- SANs cover all domains/subdomains in scope
- Not expired (`Not valid after` is in the future)
- CA is trusted (Amazon, DigiCert, Let's Encrypt, etc.)
- Key size ≥ 2048-bit RSA or ≥ 256-bit ECDSA

---

### 19.3 TLS Cipher Strength

```bash
nmap -Pn -p 443 --script ssl-enum-ciphers zero.webappsecurity.com
```

**Grade reference:**

| Grade | Meaning |
|---|---|
| A | Strong modern ciphers, TLS 1.2/1.3 only |
| B | Acceptable but some legacy support |
| C | Weak ciphers or TLS 1.0/1.1 still supported |
| D/F | Broken (RC4, DES, export ciphers, SSLv3) |

---

### 19.4 Aggressive Full Host Profile (`-A`)

```bash
sudo nmap -A -Pn -p 80,443 zero.webappsecurity.com
```

**Why `-A` enables four capabilities:**

| Enabled by `-A` | What it does |
|---|---|
| `-O` | OS fingerprinting |
| `-sV` | Service version detection |
| `-sC` | Default NSE script category |
| `--traceroute` | Network path to host |

**Use when:** You want a complete first-look profile in one command during an authorized assessment. Noisy — generates more traffic than targeted scans.

**Expected outcome:**
```
PORT    STATE SERVICE VERSION
80/tcp  open  http    nginx
| http-title: zero.webappsecurity.com
|_http-robots.txt: 1 disallowed entry
443/tcp open  ssl/http nginx
| ssl-cert: commonName=zero.webappsecurity.com
| ssl-enum-ciphers: TLSv1.2/1.3 Grade: A

TRACEROUTE
HOP RTT       ADDRESS
14  181.23ms  54.243.143.117

OS: Linux 3.X|4.X (best guess)
```

---

## Expected Output Reference

| Command | Expected Key Finding | Typical Port State |
|---|---|---|
| `nmap zero.webappsecurity.com` | Ports 80, 443 open | `open` |
| `nmap -sL zero.webappsecurity.com` | IP: 54.243.143.117 | No scan |
| `nmap -sn zero.webappsecurity.com` | Host is up (~180ms) | Up |
| `nmap -Pn -p 80,443` | Ports open despite cloud ICMP block | `open` |
| `nmap -sT -p 80,443,8080` | 80/443 open, 8080 filtered | Mixed |
| `nmap -sS -p 80,443` | Same as -sT but half-open | `open` |
| `nmap -sA -p 80,443` | `unfiltered` (ACK reaches host) | `unfiltered` |
| `nmap -sN/-sF/-sX -p 80` | `open\|filtered` (cloud blocks) | `open\|filtered` |
| `nmap -sV -p 80,443` | nginx (version may vary) | `open` |
| `nmap --script ssl-cert -p 443` | Amazon CA, valid date range | `open` |
| `nmap --script ssl-enum-ciphers -p 443` | TLS 1.2/1.3, Grade A | `open` |
| `nmap --script http-methods -p 80` | GET HEAD POST OPTIONS only | `open` |
| `nmap --script http-robots.txt -p 80` | /login.htm disallowed | `open` |
| `nmap --script vuln -p 80,443` | Check for Slowloris, headers | Verify manually |
| `nmap --badsum -p 80` | `filtered` (real stack drops it) | `filtered` |
| `nmap -A -Pn -p 80,443` | Full profile + Linux OS guess + traceroute | `open` |
| `nmap --reason -p 80,443` | `syn-ack ttl 41` (genuine open) | `open` |
| `nmap --packet-trace -p 80` | SYN sent → SA received (wire-level) | `open` |

---

> **Legal reminder:** Always confirm authorization before scanning any target.  
> `zero.webappsecurity.com` is an authorized intentionally-vulnerable application.  
> Do not attempt brute force, file upload, command injection, or service disruption.

---

*Based on: Nmap Zero to Pro Handbook (all 26 sections)*  
*Target practice host: `zero.webappsecurity.com`*  
*Python runner script included — run with `sudo python3 nmap_runner.py`*
