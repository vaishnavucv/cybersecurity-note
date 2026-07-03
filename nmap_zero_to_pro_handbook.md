# Nmap Zero to Pro Handbook

Soon you can reach to https://cyberrange.vaishnavu.com/blogs/

A practical Nmap reference for beginners, intermediate users, VAPT engineers, DevSecOps teams, SOC analysts, system administrators, and security trainers.

This handbook focuses on authorized discovery, asset inventory, port scanning, service enumeration, OS fingerprinting, Nmap Scripting Engine usage, timing control, output handling, firewall validation, and repeatable professional workflows.

> Use Nmap only against systems you own, operate, or have explicit written authorization to test. Define scope, exclusions, rate limits, maintenance windows, and reporting requirements before scanning.

---

## Table of Contents

1. [Core Concepts](#1-core-concepts)
2. [Command Structure](#2-command-structure)
3. [Target Specification](#3-target-specification)
4. [Host Discovery](#4-host-discovery)
5. [Port States](#5-port-states)
6. [Scan Techniques](#6-scan-techniques)
7. [Port Specification and Scan Order](#7-port-specification-and-scan-order)
8. [Service and Version Detection](#8-service-and-version-detection)
9. [OS Detection](#9-os-detection)
10. [Nmap Scripting Engine](#10-nmap-scripting-engine)
11. [Timing and Performance](#11-timing-and-performance)
12. [Output and Reporting](#12-output-and-reporting)
13. [Runtime Interaction](#13-runtime-interaction)
14. [IPv6 and Miscellaneous Options](#14-ipv6-and-miscellaneous-options)
15. [Firewall and IDS Validation Options](#15-firewall-and-ids-validation-options)
16. [Beginner Command Playbook](#16-beginner-command-playbook)
17. [Intermediate Command Playbook](#17-intermediate-command-playbook)
18. [Advanced and Professional Workflows](#18-advanced-and-professional-workflows)
19. [Service Specific Recipes](#19-service-specific-recipes)
20. [UDP Scanning Strategy](#20-udp-scanning-strategy)
21. [Enterprise Assessment Methodology](#21-enterprise-assessment-methodology)
22. [Troubleshooting Guide](#22-troubleshooting-guide)
23. [Common Mistakes](#23-common-mistakes)
24. [Practice Labs](#24-practice-labs)
25. [Quick Reference Tables](#25-quick-reference-tables)
26. [Sources Reviewed](#26-sources-reviewed)

---

# 1. Core Concepts

## What Nmap does

Nmap is a network discovery and security auditing tool. It helps answer questions such as:

- Which hosts are alive?
- Which ports are open?
- Which services are running?
- Which versions are exposed?
- Which operating system might be running?
- Which firewall or filtering behavior exists?
- Which services need deeper validation?

## What Nmap does not guarantee

Nmap reports what it can infer from packets and responses. Results can be affected by:

- Firewalls
- IDS or IPS devices
- Packet loss
- NAT
- Load balancers
- Rate limits
- Host based firewall rules
- Nonstandard services
- Unreliable network paths

Treat Nmap as an evidence collection tool, not a final truth source.

## Recommended professional scan model

Use a phased methodology:

1. Validate the authorized scope.
2. Confirm target list.
3. Discover live hosts.
4. Discover TCP and UDP ports.
5. Enumerate services and versions.
6. Run targeted NSE scripts.
7. Save evidence in multiple formats.
8. Review results manually.
9. Report findings with commands, timestamps, and scope.

---

# 2. Command Structure

General syntax:

```bash
nmap [scan type] [options] [target.]
```

Example:

```bash
nmap -sV -p 22,80,443 192.168.56.10
```

Breakdown:

| Part | Meaning |
|---|---|
| `nmap` | Runs the Nmap scanner |
| `-sV` | Enables service and version detection |
| `-p 22,80,443` | Scans only ports 22, 80, and 443 |
| `192.168.56.10` | Target host |

## Safe placeholders used in this handbook

| Placeholder | Meaning |
|---|---|
| `target` | Replace with authorized IP, hostname, or CIDR |
| `192.168.56.10` | Example lab host |
| `192.168.56.0/24` | Example lab subnet |
| `10.10.10.0/24` | Example internal test subnet |
| `targets.txt` | File containing authorized targets |
| `<ports>` | Replace with discovered open ports |

---

# 3. Target Specification

## Single IP

```bash
nmap 192.168.56.10
```

Use case:

- Scan one known host.
- Good first command for a beginner lab.

## Hostname

```bash
nmap example.internal
```

Use case:

- Scan a named asset.
- Useful when DNS naming is part of your inventory.

## Multiple targets

```bash
nmap 192.168.56.10 192.168.56.11 192.168.56.12
```

Use case:

- Scan selected hosts.
- Useful for targeted validation.

## CIDR subnet

```bash
nmap 192.168.56.0/24
```

Use case:

- Scan an entire subnet.
- Useful for internal asset discovery.

## IP range

```bash
nmap 192.168.56.10-50
```

Use case:

- Scan a controlled range.
- Useful when scope is a partial subnet.

## Octet range

```bash
nmap 192.168.56-57.1-254
```

Use case:

- Scan multiple adjacent subnets.
- Useful for lab or internal inventory work.

## Read targets from file

```bash
nmap -iL targets.txt
```

Use case:

- Professional assessments.
- Large target lists.
- Repeatable scans.

Example `targets.txt`:

```text
192.168.56.10
192.168.56.11
192.168.56.20
example.internal
```

## Exclude a host

```bash
nmap 192.168.56.0/24 --exclude 192.168.56.1
```

Use case:

- Avoid routers, printers, fragile devices, OT systems, executive devices, or explicitly excluded assets.

## Exclude from file

```bash
nmap 192.168.56.0/24 --excludefile exclude.txt
```

Use case:

- Enterprise scope control.
- Client approved exclusions.

## List scan without probing

```bash
nmap -sL 192.168.56.0/24
```

Use case:

- Validate target expansion before scanning.
- Confirm DNS names and scope.
- Safe pre-scan sanity check.

## Scan all resolved addresses

```bash
nmap --resolve-all example.internal
```

Use case:

- Hostname resolves to multiple IPs.
- Useful for load balanced services.

## Avoid duplicate IP scans

```bash
nmap --unique -iL targets.txt
```

Use case:

- Prevent duplicate scanning when ranges overlap.

## Random targets

```bash
nmap -iR 10
```

Use case:

- Formal internet research only.
- Do not use for normal VAPT or training unless the scope explicitly permits it.

---

# 4. Host Discovery

Host discovery identifies which systems are online before deeper scanning.

## Ping scan, no port scan

```bash
nmap -sn 192.168.56.0/24
```

Use case:

- Find live hosts.
- Build inventory.
- Identify DHCP assets.
- Perform light internal discovery.

## Skip host discovery

```bash
nmap -Pn 192.168.56.10
```

Use case:

- Target blocks ICMP.
- Firewall drops discovery probes.
- You already know the host is alive.
- External perimeter scans where ping responses are unreliable.

Operational note:

- `-Pn` can slow scans across large ranges because every target is treated as alive.

## List scan only

```bash
nmap -sL 192.168.56.0/24
```

Use case:

- No packet probing against target hosts.
- Confirm scope.
- Review reverse DNS names.

## ARP discovery on local network

```bash
sudo nmap -sn -PR 192.168.56.0/24
```

Use case:

- Fast local LAN discovery.
- More reliable than ICMP on the same broadcast domain.

## ICMP echo discovery

```bash
sudo nmap -sn -PE 192.168.56.0/24
```

Use case:

- Traditional ping style discovery.
- Works when ICMP echo is allowed.

## ICMP timestamp discovery

```bash
sudo nmap -sn -PP 192.168.56.0/24
```

Use case:

- Alternative host discovery probe.
- Useful when echo request is blocked.

## ICMP address mask discovery

```bash
sudo nmap -sn -PM 192.168.56.0/24
```

Use case:

- Legacy networks.
- Rarely useful on hardened systems.

## TCP SYN discovery

```bash
sudo nmap -sn -PS22,80,443 192.168.56.0/24
```

Use case:

- Discover hosts that respond to SYN probes.
- Good for networks where ICMP is blocked.

## TCP ACK discovery

```bash
sudo nmap -sn -PA80,443 192.168.56.0/24
```

Use case:

- Identify hosts through firewall behavior.
- Useful for filtered network segments.

## UDP discovery

```bash
sudo nmap -sn -PU53,123,161 192.168.56.0/24
```

Use case:

- Discover infrastructure systems.
- DNS, NTP, and SNMP heavy environments.

## IP protocol ping

```bash
sudo nmap -sn -PO 192.168.56.0/24
```

Use case:

- Advanced discovery using IP protocol probes.
- Useful for protocol filtering analysis.

## Disable DNS resolution

```bash
nmap -n -sn 192.168.56.0/24
```

Use case:

- Faster scans.
- Avoid DNS delays.
- Clean output by IP address.

## Force DNS resolution

```bash
nmap -R -sn 192.168.56.0/24
```

Use case:

- Asset naming and inventory.
- Useful for reports where hostnames matter.

## Use specific DNS server

```bash
nmap -sL --dns-servers 192.168.56.53 192.168.56.0/24
```

Use case:

- Internal DNS enumeration.
- Validate DNS visibility from a test segment.

---

# 5. Port States

Nmap commonly reports six port states.

| State | Meaning | Analyst interpretation |
|---|---|---|
| `open` | A service is accepting connections or packets | Prioritize enumeration and exposure review |
| `closed` | Host responded but no service is listening | Useful for host confirmation and OS detection |
| `filtered` | Nmap cannot determine state because filtering blocks probes | Investigate firewall, ACL, or host firewall behavior |
| `unfiltered` | Port is reachable but Nmap cannot decide open or closed | Often seen with ACK scans |
| `open|filtered` | No response, could be open or filtered | Common in UDP, FIN, NULL, and Xmas scans |
| `closed|filtered` | Could be closed or filtered | Mostly associated with idle scan behavior |

Professional interpretation:

- `open` is not automatically a vulnerability.
- `filtered` is not automatically secure.
- `closed` confirms host reachability.
- UDP often needs service specific validation because many UDP services do not respond unless the payload is correct.

---

# 6. Scan Techniques

## Default scan

```bash
nmap 192.168.56.10
```

Use case:

- Beginner baseline.
- Scans the default common TCP ports.
- Uses the best default behavior available based on privileges.

## TCP SYN scan

```bash
sudo nmap -sS 192.168.56.10
```

Use case:

- Standard professional TCP scan.
- Fast and reliable.
- Requires privileges on most systems.

When to use:

- Internal VAPT.
- External perimeter scanning.
- Large TCP port discovery.

## TCP connect scan

```bash
nmap -sT 192.168.56.10
```

Use case:

- Non-root user.
- Environment where raw packets are unavailable.
- Works through the operating system TCP connect call.

Tradeoff:

- Usually noisier at the service level because it completes TCP connections.

## UDP scan

```bash
sudo nmap -sU 192.168.56.10
```

Use case:

- Discover UDP services such as DNS, NTP, SNMP, TFTP, IPsec, SIP, and DHCP related services.

Tradeoff:

- Slower than TCP.
- UDP results require careful validation.

## TCP ACK scan

```bash
sudo nmap -sA 192.168.56.10
```

Use case:

- Firewall rule mapping.
- Determine whether ports are filtered or unfiltered.
- Not used to find open services directly.

## TCP Window scan

```bash
sudo nmap -sW 192.168.56.10
```

Use case:

- Advanced firewall and stack behavior analysis.
- Works only against certain TCP/IP stack implementations.

## TCP Maimon scan

```bash
sudo nmap -sM 192.168.56.10
```

Use case:

- Advanced TCP behavior testing.
- Rarely used in modern routine assessments.

## TCP NULL scan

```bash
sudo nmap -sN 192.168.56.10
```

Use case:

- Advanced TCP stack behavior testing.
- May help in specific firewall analysis scenarios.

## TCP FIN scan

```bash
sudo nmap -sF 192.168.56.10
```

Use case:

- Advanced firewall and packet filtering validation.
- Not reliable against all operating systems.

## TCP Xmas scan

```bash
sudo nmap -sX 192.168.56.10
```

Use case:

- Advanced packet behavior testing.
- Mostly useful in labs and specific filtering analysis.

## Custom TCP flags

```bash
sudo nmap --scanflags SYNFIN -p 80 192.168.56.10
```

Use case:

- Lab testing.
- Firewall and IDS rule validation.
- TCP stack behavior research.

## SCTP INIT scan

```bash
sudo nmap -sY 192.168.56.10
```

Use case:

- Telecom and specialized infrastructure using SCTP.

## SCTP COOKIE-ECHO scan

```bash
sudo nmap -sZ 192.168.56.10
```

Use case:

- SCTP firewall and service testing.

## IP protocol scan

```bash
sudo nmap -sO 192.168.56.10
```

Use case:

- Identify supported IP protocols.
- Useful in infrastructure and security research.

## FTP bounce scan

```bash
nmap -b ftp-relay.internal 192.168.56.10
```

Use case:

- Historical technique.
- Only for controlled lab study or explicitly approved legacy assessments.

## Idle scan

```bash
sudo nmap -sI zombie-host.internal 192.168.56.10
```

Use case:

- Advanced lab demonstration of IP ID behavior.
- Not a routine enterprise scan.
- Requires strict authorization because it involves a third system.

---

# 7. Port Specification and Scan Order

## Scan one port

```bash
nmap -p 80 192.168.56.10
```

Use case:

- Validate a single service.

## Scan multiple ports

```bash
nmap -p 22,80,443,8080 192.168.56.10
```

Use case:

- Web and SSH focused review.

## Scan a port range

```bash
nmap -p 1-1000 192.168.56.10
```

Use case:

- Controlled low range scan.

## Scan all TCP ports

```bash
nmap -p- 192.168.56.10
```

Use case:

- Full TCP exposure discovery.
- Find services outside default ports.

## Scan by service name

```bash
nmap -p http,https,ssh 192.168.56.10
```

Use case:

- Human readable service targeting.

## Scan service wildcard

```bash
nmap -p "http*" 192.168.56.10
```

Use case:

- Scan ports named as HTTP related in the services database.

## TCP and UDP mixed port specification

```bash
sudo nmap -sS -sU -p T:22,80,443,U:53,123,161 192.168.56.10
```

Use case:

- Focused mixed protocol assessment.

## Fast scan

```bash
nmap -F 192.168.56.10
```

Use case:

- Quick triage.
- Time constrained scanning.

## Top ports

```bash
nmap --top-ports 100 192.168.56.10
```

Use case:

- Quick scan against most common ports.
- Useful across many hosts.

## Port ratio

```bash
nmap --port-ratio 0.01 192.168.56.10
```

Use case:

- Scan ports above a frequency threshold in Nmap service data.

## Exclude ports

```bash
nmap -p- --exclude-ports 9100,515 192.168.56.10
```

Use case:

- Avoid fragile services such as printer ports or client excluded services.

## Sequential scan order

```bash
nmap -r -p 1-1000 192.168.56.10
```

Use case:

- Reproducible testing.
- Training demonstrations.
- Packet capture analysis.

---

# 8. Service and Version Detection

## Basic version detection

```bash
nmap -sV 192.168.56.10
```

Use case:

- Identify service names and versions.
- Support vulnerability mapping and patch validation.

## Version detection on selected ports

```bash
nmap -sV -p 22,80,443 192.168.56.10
```

Use case:

- Focused enumeration after port discovery.

## Light version detection

```bash
nmap -sV --version-light 192.168.56.10
```

Use case:

- Faster service checks.
- Large environments where speed matters.

## Version intensity

```bash
nmap -sV --version-intensity 7 192.168.56.10
```

Use case:

- Balance speed and fingerprinting depth.
- Range is 0 to 9.

## Try every version probe

```bash
nmap -sV --version-all 192.168.56.10
```

Use case:

- Deep service fingerprinting.
- Unknown or unusual services.

Tradeoff:

- Slower.
- More network traffic.

## Version trace

```bash
nmap -sV --version-trace -p 8080 192.168.56.10
```

Use case:

- Troubleshoot why a service was not identified.
- Training and debugging.

## Do not exclude any ports from version detection

```bash
nmap -sV --allports 192.168.56.10
```

Use case:

- Deep assessment where every discovered port needs probing.

## RPC detection

```bash
nmap -sV -p 111,2049 192.168.56.10
```

Use case:

- NFS and RPC service enumeration.
- Nmap integrates RPC grinder behavior with version detection.

---

# 9. OS Detection

## Enable OS detection

```bash
sudo nmap -O 192.168.56.10
```

Use case:

- Identify likely operating system.
- Asset classification.
- Network inventory.

## OS detection with promising target limit

```bash
sudo nmap -O --osscan-limit 192.168.56.10
```

Use case:

- Save time by only attempting OS detection when conditions are good.

## Aggressive OS guess

```bash
sudo nmap -O --osscan-guess 192.168.56.10
```

Use case:

- Lab use.
- When normal OS detection is close but inconclusive.

## Set maximum OS tries

```bash
sudo nmap -O --max-os-tries 2 192.168.56.10
```

Use case:

- Control scan time.
- Large scoped environments.

## OS plus version detection

```bash
sudo nmap -O -sV 192.168.56.10
```

Use case:

- Combine host classification and service enumeration.

---

# 10. Nmap Scripting Engine

The Nmap Scripting Engine, often called NSE, extends Nmap using Lua scripts.

## Locate scripts on Linux

```bash
cd /usr/share/nmap/scripts
ls
```

Use case:

- Browse available scripts.
- Confirm installed NSE library.

## Search scripts by service

```bash
ls /usr/share/nmap/scripts | grep '^http-'
```

Use case:

- Find HTTP related scripts.

## View script details

```bash
nmap --script-help http-title
```

Use case:

- Understand what a script does before running it.

## Default scripts

```bash
nmap -sC 192.168.56.10
```

Equivalent:

```bash
nmap --script=default 192.168.56.10
```

Use case:

- Standard enumeration.
- Common discovery and safe checks.
- Should still require authorized scope.

## Single script

```bash
nmap --script=http-title -p 80,443 192.168.56.10
```

Use case:

- Identify web page title.

## Multiple scripts

```bash
nmap --script=http-title,http-headers -p 80,443 192.168.56.10
```

Use case:

- Web banner and header enumeration.

## Script wildcard

```bash
nmap --script "http-*" -p 80,443 192.168.56.10
```

Use case:

- Broad HTTP script run in a lab or approved assessment.

Caution:

- Wildcards may include noisy or intrusive scripts. Review before using in production.

## Script category

```bash
nmap --script=safe 192.168.56.10
```

Use case:

- Run scripts categorized as safe.

## Default or safe scripts

```bash
nmap --script "default or safe" 192.168.56.10
```

Use case:

- Broader enumeration with lower operational risk.

## Exclude intrusive scripts

```bash
nmap --script "not intrusive" 192.168.56.10
```

Use case:

- Avoid scripts categorized as intrusive.

## Vulnerability scripts

```bash
nmap -sV --script=vuln 192.168.56.10
```

Use case:

- Authorized vulnerability validation.
- Lab testing.
- Patch verification.

Caution:

- Review output manually.
- NSE vulnerability detection is not a replacement for a full vulnerability scanner.
- Do not run against third party targets without permission.

## Script arguments

```bash
nmap --script snmp-sysdescr --script-args snmpcommunity=public -p 161 -sU 192.168.56.10
```

Use case:

- Provide script specific input.
- SNMP validation in authorized environments.

## Script arguments from file

```bash
nmap --script=http-form-brute --script-args-file args.txt 192.168.56.10
```

Use case:

- Controlled lab only.
- Approved credential audit only.

Caution:

- Brute force, denial of service, exploit, fuzzer, and intrusive scripts need explicit written approval.

## Script trace

```bash
nmap --script=http-title --script-trace -p 80 192.168.56.10
```

Use case:

- Debug NSE traffic.
- Understand script behavior.

## Update script database

```bash
sudo nmap --script-updatedb
```

Use case:

- Refresh NSE script database after adding or modifying scripts.

## NSE categories

| Category | Meaning | Recommended usage |
|---|---|---|
| `auth` | Authentication related checks | Approved audits only |
| `broadcast` | Local broadcast discovery | Internal networks only |
| `brute` | Brute force authentication attempts | Lab or written approval only |
| `default` | Default script set | Routine enumeration with permission |
| `discovery` | Extra discovery and enumeration | Routine with scope control |
| `dos` | Denial of service tests | Lab only unless explicitly approved |
| `exploit` | Exploit oriented scripts | Lab only unless explicitly approved |
| `external` | Uses third party external services | Check privacy and client approval |
| `fuzzer` | Sends malformed inputs | Lab only |
| `intrusive` | More likely to affect target state | Written approval only |
| `malware` | Malware related checks | Defensive validation |
| `safe` | Lower risk scripts | Good default category |
| `version` | Version detection support | Service fingerprinting |
| `vuln` | Vulnerability detection | Authorized vulnerability assessment |

---

# 11. Timing and Performance

## Timing templates

| Template | Name | Use case |
|---|---|---|
| `-T0` | Paranoid | Very slow, controlled testing |
| `-T1` | Sneaky | Very slow, controlled testing |
| `-T2` | Polite | Low bandwidth and fragile environments |
| `-T3` | Normal | Default balanced behavior |
| `-T4` | Aggressive | Reliable internal or lab networks |
| `-T5` | Insane | Very fast networks, accuracy may suffer |

## Common timing choice

```bash
nmap -T4 192.168.56.10
```

Use case:

- Most internal VAPT scans on reliable networks.

## Conservative timing

```bash
nmap -T2 192.168.56.10
```

Use case:

- Fragile networks.
- OT style environments.
- Bandwidth sensitive systems.

## Control minimum rate

```bash
sudo nmap -sS -p- --min-rate 1000 192.168.56.10
```

Use case:

- Faster TCP port discovery on internal networks.

Risk:

- Too high a rate can reduce accuracy.

## Control maximum rate

```bash
nmap --max-rate 100 192.168.56.10
```

Use case:

- Avoid overwhelming sensitive links.
- Control scan footprint.

## Limit retries

```bash
nmap --max-retries 2 192.168.56.10
```

Use case:

- Speed up scans where some accuracy tradeoff is acceptable.

## Host timeout

```bash
nmap --host-timeout 5m 192.168.56.10
```

Use case:

- Avoid spending too long on one host.

## Scan delay

```bash
nmap --scan-delay 500ms 192.168.56.10
```

Use case:

- Reduce rate.
- Handle rate limited hosts.
- Maintain stability.

## Maximum scan delay

```bash
nmap --max-scan-delay 1s 192.168.56.10
```

Use case:

- Prevent Nmap from slowing excessively.

## RTT timeout tuning

```bash
nmap --initial-rtt-timeout 500ms --max-rtt-timeout 2s 192.168.56.10
```

Use case:

- Tune latency expectations.
- Useful across WAN or VPN links.

## Host group tuning

```bash
nmap --min-hostgroup 64 --max-hostgroup 256 192.168.56.0/24
```

Use case:

- Control batch size for large scans.

## Parallelism tuning

```bash
nmap --min-parallelism 10 --max-parallelism 50 192.168.56.0/24
```

Use case:

- Advanced performance tuning.
- Use carefully because manual parallelism can reduce accuracy.

## Ignore RST rate limits

```bash
sudo nmap -sS --defeat-rst-ratelimit --open 192.168.56.10
```

Use case:

- Fast open port discovery when closed versus filtered distinction is not critical.

Risk:

- Can reduce accuracy.

## Ignore ICMP rate limits for UDP

```bash
sudo nmap -sU --defeat-icmp-ratelimit --top-ports 100 192.168.56.10
```

Use case:

- Faster UDP triage.

Risk:

- UDP accuracy can suffer significantly.

---

# 12. Output and Reporting

## Normal output

```bash
nmap -oN report.txt 192.168.56.10
```

Use case:

- Human readable report.
- Notes and evidence.

## XML output

```bash
nmap -oX report.xml 192.168.56.10
```

Use case:

- Tool import.
- Automation.
- Reporting pipelines.

## Grepable output

```bash
nmap -oG report.gnmap 192.168.56.10
```

Use case:

- Quick shell parsing.

Note:

- Grepable output is deprecated, but still useful for quick one off parsing.

## All major output formats

```bash
nmap -oA assessment 192.168.56.10
```

Creates:

```text
assessment.nmap
assessment.xml
assessment.gnmap
```

Use case:

- Professional default.
- Keeps human readable, machine readable, and grepable formats.

## Append output

```bash
nmap -oN report.txt --append-output 192.168.56.11
```

Use case:

- Add scans to an existing report file.

## Resume scan

```bash
nmap --resume assessment.gnmap
```

Use case:

- Resume interrupted scans.

## Verbose output

```bash
nmap -v 192.168.56.10
```

Use case:

- See progress and discovered open ports earlier.

## Extra verbose

```bash
nmap -vv 192.168.56.10
```

Use case:

- More operational detail.

## Debug output

```bash
nmap -d 192.168.56.10
```

Use case:

- Troubleshoot Nmap behavior.

## Port reason

```bash
nmap --reason 192.168.56.10
```

Use case:

- Understand why Nmap assigned a host or port state.

## Show open ports only

```bash
nmap --open 192.168.56.10
```

Use case:

- Cleaner output.
- Useful in large scans.

## Packet trace

```bash
nmap --packet-trace -p 80 192.168.56.10
```

Use case:

- Training.
- Troubleshooting.
- Packet level understanding.

## Print interface and route list

```bash
nmap --iflist
```

Use case:

- Confirm interface selection.
- Troubleshoot routing.

## Periodic stats

```bash
nmap --stats-every 30s 192.168.56.0/24
```

Use case:

- Long running scans.
- Status monitoring.

## Convert XML to HTML

```bash
xsltproc report.xml -o report.html
```

Use case:

- Human readable HTML reporting.

## Compare two scans with Ndiff

```bash
ndiff old-scan.xml new-scan.xml
```

Use case:

- Change detection.
- Continuous monitoring.
- Before and after patch validation.

---

# 13. Runtime Interaction

During a running Nmap scan, certain keys can adjust output without restarting the scan.

| Key | Action |
|---|---|
| `v` | Increase verbosity |
| `V` | Decrease verbosity |
| `d` | Increase debug level |
| `D` | Decrease debug level |
| `p` | Turn packet tracing on |
| `P` | Turn packet tracing off |
| `?` | Show runtime help |
| Any other key | Print scan status |

Use case:

- Long scans.
- Need status without stopping.
- Troubleshooting during execution.

Disable runtime interactions:

```bash
nmap --noninteractive 192.168.56.10
```

Use case:

- CI pipelines.
- Automated jobs.

---

# 14. IPv6 and Miscellaneous Options

## IPv6 scan

```bash
nmap -6 2001:db8::10
```

Use case:

- IPv6 exposure assessment.
- Dual stack environments.

## Aggressive scan bundle

```bash
sudo nmap -A 192.168.56.10
```

Enables:

- OS detection
- Version detection
- Default script scanning
- Traceroute

Use case:

- Comprehensive host profiling in an authorized environment.

Caution:

- `-A` is convenient but not subtle.
- Use targeted options for production when you need tighter control.

## Traceroute

```bash
nmap --traceroute 192.168.56.10
```

Use case:

- Network path discovery.
- Routing visibility.

## Print version

```bash
nmap -V
```

Use case:

- Confirm installed Nmap version.
- Useful in reports.

## Help

```bash
nmap -h
```

Use case:

- Quick option reminder.

## Custom data directory

```bash
nmap --datadir /custom/nmap-data 192.168.56.10
```

Use case:

- Custom service probes, OS DB, or scripts in controlled environments.

## Custom services database

```bash
nmap --servicedb ./nmap-services 192.168.56.10
```

Use case:

- Lab tuning.
- Custom service frequency data.

## Custom version database

```bash
nmap --versiondb ./nmap-service-probes -sV 192.168.56.10
```

Use case:

- Research and custom fingerprinting.

## Force privileged behavior

```bash
sudo nmap --privileged -sS 192.168.56.10
```

Use case:

- Systems using Linux capabilities or nonstandard privilege models.

## Force unprivileged behavior

```bash
nmap --unprivileged 192.168.56.10
```

Use case:

- Testing behavior without raw packet features.

## Send raw Ethernet frames

```bash
sudo nmap --send-eth -sS 192.168.56.10
```

Use case:

- Link layer troubleshooting.
- Specific platform behavior.

## Send raw IP packets

```bash
sudo nmap --send-ip -sS 192.168.56.10
```

Use case:

- Force IP layer sending instead of Ethernet frames.

---

# 15. Firewall and IDS Validation Options

These options are powerful and should be treated as change controlled defensive validation tools. Use them only in a lab or in explicitly approved security assessments.

## Fragment packets

```bash
sudo nmap -sS -f -p 80 192.168.56.10
```

Use case:

- Validate whether network controls properly handle fragmented packets.

Limitations:

- Not supported for all scan features.
- Version detection and NSE generally rely on normal TCP behavior.

## Custom MTU

```bash
sudo nmap -sS --mtu 24 -p 80 192.168.56.10
```

Use case:

- Controlled packet fragmentation testing.

Note:

- MTU value must be a multiple of 8.

## Decoy source validation

```bash
sudo nmap -sS -D 192.168.56.20,ME,192.168.56.30 -p 80 192.168.56.10
```

Use case:

- IDS correlation testing in a lab.
- Confirm alert attribution and logging behavior.

Caution:

- Do not use third party systems as decoys.
- Use only owned lab addresses.

## Specify network interface

```bash
sudo nmap -e eth0 -sS 192.168.56.10
```

Use case:

- Multi-homed scanner.
- Confirm traffic uses correct interface.

## Spoof source address

```bash
sudo nmap -S 192.168.56.99 -e eth0 -Pn -sS -p 80 192.168.56.10
```

Use case:

- Lab validation of source address filtering.

Caution:

- Usually you will not receive replies unless routing supports it.
- Not a routine assessment command.

## Source port

```bash
sudo nmap -sS --source-port 53 -p 80 192.168.56.10
```

Use case:

- Validate whether firewall rules incorrectly trust traffic by source port.

## Add custom data string

```bash
sudo nmap -sS --data-string "Authorized Security Test" -p 80 192.168.56.10
```

Use case:

- Tag packet payloads during authorized control validation.

## Add random data length

```bash
sudo nmap -sS --data-length 24 -p 80 192.168.56.10
```

Use case:

- Test sensor behavior with payload padding.

## TTL control

```bash
sudo nmap -sS --ttl 64 -p 80 192.168.56.10
```

Use case:

- Path and packet behavior testing.

## MAC spoofing in lab

```bash
sudo nmap --spoof-mac 00:11:22:33:44:55 -sS -p 80 192.168.56.10
```

Use case:

- Lab NAC or switch control validation.

Caution:

- Do not disrupt production identity, NAC, or asset tracking systems.

## Proxy option

```bash
nmap --proxies http://127.0.0.1:8080 -sV -p 80 192.168.56.10
```

Use case:

- Controlled testing of NSE and version scan behavior through an approved proxy.

Limitations:

- Does not apply to all scan phases.

## Bad checksum

```bash
sudo nmap --badsum -p 80 192.168.56.10
```

Use case:

- Lab validation to identify whether responses are generated by firewalls or middleboxes instead of the target stack.

---

# 16. Beginner Command Playbook

## 1. Check Nmap version

```bash
nmap -V
```

Scenario:

- Before training, reporting, or troubleshooting.

## 2. Scan one host

```bash
nmap 192.168.56.10
```

Scenario:

- Learn basic output.
- Identify common open TCP ports.

## 3. Discover live hosts

```bash
nmap -sn 192.168.56.0/24
```

Scenario:

- Build a quick live host list.

## 4. Scan specific ports

```bash
nmap -p 22,80,443 192.168.56.10
```

Scenario:

- Validate SSH and web exposure.

## 5. Show only open ports

```bash
nmap --open 192.168.56.10
```

Scenario:

- Reduce noise in output.

## 6. Save output

```bash
nmap -oN beginner-scan.txt 192.168.56.10
```

Scenario:

- Keep evidence for notes.

## 7. Service detection

```bash
nmap -sV 192.168.56.10
```

Scenario:

- Identify what is running behind open ports.

## 8. Default scripts

```bash
nmap -sC 192.168.56.10
```

Scenario:

- Basic enumeration using default scripts.

## 9. Combined beginner enumeration

```bash
nmap -sV -sC -p 22,80,443 192.168.56.10
```

Scenario:

- Beginner friendly service enumeration.

## 10. Aggressive profile in lab

```bash
sudo nmap -A 192.168.56.10
```

Scenario:

- Lab demonstration of bundled OS, version, script, and traceroute features.

---

# 17. Intermediate Command Playbook

## 1. Full TCP discovery

```bash
sudo nmap -Pn -sS -p- --open -T4 -oA tcp-full 192.168.56.10
```

Scenario:

- Find all open TCP ports on a known live host.

Explanation:

| Option | Purpose |
|---|---|
| `-Pn` | Treat target as online |
| `-sS` | SYN scan |
| `-p-` | All TCP ports |
| `--open` | Show open ports only |
| `-T4` | Faster timing for reliable networks |
| `-oA` | Save all major output formats |

## 2. Enumerate discovered ports

```bash
sudo nmap -Pn -sV -sC -O -p 22,80,443 -oA enum 192.168.56.10
```

Scenario:

- Detailed enumeration after open ports are identified.

## 3. Top UDP ports

```bash
sudo nmap -Pn -sU --top-ports 100 -oA udp-top100 192.168.56.10
```

Scenario:

- Fast UDP triage.

## 4. Targeted UDP ports

```bash
sudo nmap -Pn -sU -p 53,67,68,69,123,161,500,514,520,1900,4500,5060 192.168.56.10
```

Scenario:

- Infrastructure and network service review.

## 5. Web and TLS enumeration

```bash
nmap -Pn -p 80,443,8080,8443 --script http-title,http-headers,ssl-cert,ssl-enum-ciphers 192.168.56.10
```

Scenario:

- Web service fingerprinting.
- TLS certificate and cipher review.

## 6. SMB enumeration

```bash
nmap -Pn -p 139,445 --script smb-os-discovery,smb-protocols,smb-security-mode 192.168.56.10
```

Scenario:

- Windows file sharing review.
- SMB protocol validation.

## 7. SSH enumeration

```bash
nmap -Pn -p 22 --script ssh-hostkey,ssh2-enum-algos 192.168.56.10
```

Scenario:

- SSH algorithm and host key review.

## 8. DNS enumeration

```bash
nmap -Pn -sU -p 53 --script dns-recursion,dns-service-discovery 192.168.56.10
```

Scenario:

- DNS service review.
- Recursive DNS validation.

## 9. SNMP discovery

```bash
sudo nmap -Pn -sU -p 161 --script snmp-info 192.168.56.10
```

Scenario:

- SNMP exposure validation.

## 10. Scan from target file and save outputs

```bash
sudo nmap -iL targets.txt -Pn -sS -p- --open -T4 -oA enterprise-tcp-full
```

Scenario:

- Repeatable enterprise TCP discovery.

---

# 18. Advanced and Professional Workflows

## Workflow 1: Clean internal VAPT workflow

Step 1: Validate target expansion.

```bash
nmap -sL -n -iL targets.txt -oN 00-scope-preview.txt
```

Step 2: Discover live hosts.

```bash
sudo nmap -sn -iL targets.txt -oA 01-live-hosts
```

Step 3: Discover all TCP ports.

```bash
sudo nmap -Pn -sS -p- --open -T4 --min-rate 1000 -iL targets.txt -oA 02-tcp-full
```

Step 4: Enumerate services on confirmed ports.

```bash
sudo nmap -Pn -sV -sC -O -p <open_ports> -iL live-hosts.txt -oA 03-service-enum
```

Step 5: UDP top ports.

```bash
sudo nmap -Pn -sU --top-ports 100 --max-retries 3 -iL live-hosts.txt -oA 04-udp-top100
```

Step 6: Targeted NSE based on discovered services.

```bash
nmap -Pn -sV --script "default or safe" -p <open_ports> -iL live-hosts.txt -oA 05-targeted-nse
```

Step 7: Save and compare results.

```bash
ndiff previous.xml current.xml
```

## Workflow 2: External perimeter assessment

```bash
sudo nmap -Pn -sS -p 80,443,8080,8443,22,25,53,110,143,465,587,993,995 --open -T3 -iL external-scope.txt -oA external-initial
```

Scenario:

- External exposure review.
- Conservative timing.
- Focus on common internet facing services.

Follow up:

```bash
nmap -Pn -sV -sC -p <open_ports> -iL external-live.txt -oA external-enum
```

## Workflow 3: Asset inventory

```bash
sudo nmap -sn -R 10.10.10.0/24 -oA inventory-discovery
```

Then:

```bash
nmap -Pn -sV --top-ports 50 10.10.10.0/24 -oA inventory-services
```

Scenario:

- Identify assets and common services.
- Useful for CMDB enrichment.

## Workflow 4: Patch validation

```bash
nmap -Pn -sV -p 22,80,443,445 192.168.56.10 -oA before-patch
```

After patch:

```bash
nmap -Pn -sV -p 22,80,443,445 192.168.56.10 -oA after-patch
```

Compare:

```bash
ndiff before-patch.xml after-patch.xml
```

Scenario:

- Verify version changes.
- Confirm unnecessary ports are closed.

## Workflow 5: Blue team detection validation

```bash
sudo nmap -sS -p 22,80,443 --data-string "Authorized Security Validation" 192.168.56.10 -oA detection-validation
```

Scenario:

- Confirm SIEM, IDS, or EDR network telemetry.
- Use a test label in payload where appropriate.

## Workflow 6: Low impact scan profile

```bash
nmap -T2 --max-rate 50 --scan-delay 100ms -sV -p 22,80,443 192.168.56.10 -oA low-impact
```

Scenario:

- Fragile networks.
- Low bandwidth links.
- Change controlled scans.

## Workflow 7: Fast internal TCP triage

```bash
sudo nmap -Pn -sS -p- --open -T4 --min-rate 3000 --max-retries 2 192.168.56.10 -oA fast-tcp
```

Scenario:

- Lab or reliable internal networks.
- Fast identification of open TCP ports.

Risk:

- High speed can reduce accuracy.
- Re-run important findings with conservative settings.

## Workflow 8: Deep service fingerprinting

```bash
nmap -Pn -sV --version-all -p <open_ports> 192.168.56.10 -oA deep-version
```

Scenario:

- Unknown services.
- Nonstandard ports.
- False or missing service banners.

---

# 19. Service Specific Recipes

## HTTP title

```bash
nmap -Pn -p 80,443,8080,8443 --script http-title 192.168.56.10
```

Use case:

- Quickly identify web applications.

## HTTP headers

```bash
nmap -Pn -p 80,443 --script http-headers 192.168.56.10
```

Use case:

- Review exposed server headers and security headers.

## HTTP methods

```bash
nmap -Pn -p 80,443 --script http-methods 192.168.56.10
```

Use case:

- Identify allowed HTTP methods.
- Validate whether risky methods are enabled.

## TLS certificate

```bash
nmap -Pn -p 443,8443 --script ssl-cert 192.168.56.10
```

Use case:

- Certificate subject, issuer, validity, and SAN review.

## TLS ciphers

```bash
nmap -Pn -p 443,8443 --script ssl-enum-ciphers 192.168.56.10
```

Use case:

- TLS configuration review.

## SSH algorithms

```bash
nmap -Pn -p 22 --script ssh2-enum-algos 192.168.56.10
```

Use case:

- Identify weak or legacy SSH algorithms.

## SSH host key

```bash
nmap -Pn -p 22 --script ssh-hostkey 192.168.56.10
```

Use case:

- Record SSH host keys for inventory and change monitoring.

## SMB protocols

```bash
nmap -Pn -p 445 --script smb-protocols 192.168.56.10
```

Use case:

- Check SMB protocol exposure.

## SMB security mode

```bash
nmap -Pn -p 445 --script smb-security-mode 192.168.56.10
```

Use case:

- Review SMB signing and authentication behavior.

## SMB OS discovery

```bash
nmap -Pn -p 445 --script smb-os-discovery 192.168.56.10
```

Use case:

- Windows host classification.

## RDP encryption

```bash
nmap -Pn -p 3389 --script rdp-enum-encryption 192.168.56.10
```

Use case:

- RDP security posture validation.

## DNS recursion

```bash
nmap -Pn -sU -p 53 --script dns-recursion 192.168.56.10
```

Use case:

- Identify open recursive DNS behavior.

## NTP info

```bash
sudo nmap -Pn -sU -p 123 --script ntp-info 192.168.56.10
```

Use case:

- NTP service inventory.

## SNMP info

```bash
sudo nmap -Pn -sU -p 161 --script snmp-info 192.168.56.10
```

Use case:

- SNMP service discovery.

## FTP anonymous check

```bash
nmap -Pn -p 21 --script ftp-anon 192.168.56.10
```

Use case:

- Validate anonymous FTP exposure.

## SMTP commands

```bash
nmap -Pn -p 25,465,587 --script smtp-commands 192.168.56.10
```

Use case:

- Review supported SMTP commands.

## Database service discovery

```bash
nmap -Pn -p 1433,1521,3306,5432,6379,27017 -sV 192.168.56.10
```

Use case:

- Identify exposed database services.

---

# 20. UDP Scanning Strategy

UDP scanning needs patience and validation.

## Recommended UDP phases

1. Scan top UDP ports.
2. Scan known UDP services.
3. Run service specific NSE scripts.
4. Validate critical UDP findings manually.
5. Repeat important findings with conservative timing.

## UDP top 100

```bash
sudo nmap -Pn -sU --top-ports 100 192.168.56.10 -oA udp-top100
```

Use case:

- Fast first pass.

## UDP common infrastructure ports

```bash
sudo nmap -Pn -sU -p 53,67,68,69,123,137,138,161,162,500,514,520,1900,4500,5060 192.168.56.10
```

Use case:

- Infrastructure service review.

## UDP with version detection

```bash
sudo nmap -Pn -sU -sV --top-ports 100 192.168.56.10 -oA udp-version
```

Use case:

- Improve UDP state confidence.

## UDP with conservative delay

```bash
sudo nmap -Pn -sU --top-ports 100 --scan-delay 1s 192.168.56.10
```

Use case:

- Rate limited hosts.
- Better accuracy on strict networks.

## UDP faster triage

```bash
sudo nmap -Pn -sU --top-ports 100 --max-retries 1 --defeat-icmp-ratelimit 192.168.56.10
```

Use case:

- Fast triage when only clearly responsive UDP services matter.

Risk:

- May miss services.
- Recheck important targets.

---

# 21. Enterprise Assessment Methodology

## Pre-scan checklist

| Item | Required action |
|---|---|
| Authorization | Written approval and rules of engagement |
| Scope | IPs, domains, cloud assets, internal ranges |
| Exclusions | Fragile systems, OT, printers, VIP assets, third party networks |
| Timing | Maintenance window and rate limits |
| Notification | SOC, NOC, IT operations, stakeholders |
| Logging | Output directory, naming convention, timestamps |
| Safety | Stop conditions and escalation contacts |

## Suggested folder structure

```text
nmap-assessment/
  00_scope/
  01_discovery/
  02_tcp/
  03_udp/
  04_enum/
  05_nse/
  06_reports/
  07_evidence/
```

## Naming convention

```text
<phase>-<environment>-<date>-<target-group>
```

Example:

```text
02-tcp-internal-2026-06-25-corp-lan
```

## Scope preview

```bash
nmap -sL -n -iL scope.txt -oN 00_scope/scope-preview.txt
```

## Discovery

```bash
sudo nmap -sn -iL scope.txt -oA 01_discovery/live-hosts
```

## TCP all ports

```bash
sudo nmap -Pn -sS -p- --open -T4 --min-rate 1000 -iL live-hosts.txt -oA 02_tcp/tcp-full
```

## TCP service enumeration

```bash
sudo nmap -Pn -sV -sC -O -p <ports> -iL live-hosts.txt -oA 04_enum/tcp-enum
```

## UDP top ports

```bash
sudo nmap -Pn -sU --top-ports 100 --max-retries 3 -iL live-hosts.txt -oA 03_udp/udp-top100
```

## Targeted scripts

```bash
nmap -Pn -sV --script "default or safe" -p <ports> -iL live-hosts.txt -oA 05_nse/safe-nse
```

## Vulnerability scripts after approval

```bash
nmap -Pn -sV --script vuln -p <ports> -iL approved-targets.txt -oA 05_nse/vuln-nse
```

## Evidence quality checklist

For every scan, record:

- Date and time
- Scanner host
- Scanner IP
- Nmap version
- Target scope
- Exclusion list
- Exact command
- Output files
- Relevant screenshots or packet captures if required
- Analyst notes
- False positive validation notes

---

# 22. Troubleshooting Guide

## Problem: All hosts appear down

Try:

```bash
nmap -Pn 192.168.56.10
```

Reason:

- ICMP or discovery probes may be blocked.

## Problem: Scan is very slow

Try:

```bash
nmap -T4 --max-retries 2 192.168.56.10
```

Reason:

- Packet loss, filtering, UDP scans, or slow timing.

## Problem: Need faster internal TCP scan

Try:

```bash
sudo nmap -sS -p- --min-rate 1000 -T4 --open 192.168.56.10
```

Reason:

- Full TCP scan can be accelerated on reliable internal networks.

## Problem: UDP scan is slow

Try:

```bash
sudo nmap -sU --top-ports 100 --max-retries 2 192.168.56.10
```

Reason:

- UDP often depends on timeouts and ICMP unreachable behavior.

## Problem: Too much output

Try:

```bash
nmap --open -oA clean-output 192.168.56.10
```

Reason:

- Only open or possibly open ports are shown.

## Problem: Need to know why a port is filtered

Try:

```bash
nmap --reason -p 80 192.168.56.10
```

Reason:

- Shows packet reason behind Nmap state.

## Problem: Service version is unclear

Try:

```bash
nmap -sV --version-all -p 8080 192.168.56.10
```

Reason:

- Runs deeper service probes.

## Problem: OS detection fails

Try:

```bash
sudo nmap -O --osscan-guess 192.168.56.10
```

Also ensure:

- At least one open and one closed TCP port are visible.
- Target is not heavily filtered.
- You have sufficient privileges.

## Problem: NSE script fails

Try:

```bash
nmap --script=http-title --script-trace -p 80 192.168.56.10
```

Reason:

- Shows script network activity.

## Problem: Wrong interface selected

Try:

```bash
nmap --iflist
```

Then:

```bash
sudo nmap -e eth0 192.168.56.10
```

Reason:

- Multi-interface systems can route traffic unexpectedly.

---

# 23. Common Mistakes

## Mistake 1: Running `-A` everywhere

Problem:

- `-A` enables multiple aggressive features and may be too broad.

Better:

```bash
nmap -sV -sC -p <ports> target
```

## Mistake 2: Scanning all ports and enumerating at the same time

Problem:

- Combining `-p- -sV -sC -O` can be slow and noisy.

Better two phase approach:

```bash
sudo nmap -Pn -sS -p- --open -oA tcp-full target
```

Then:

```bash
sudo nmap -Pn -sV -sC -O -p <open_ports> -oA enum target
```

## Mistake 3: Ignoring UDP

Problem:

- Critical services can run over UDP.

Better:

```bash
sudo nmap -Pn -sU --top-ports 100 target
```

## Mistake 4: Not saving output

Problem:

- Findings cannot be reproduced or reported properly.

Better:

```bash
nmap -oA scan-name target
```

## Mistake 5: Trusting banners blindly

Problem:

- Services can lie or hide versions.

Better:

```bash
nmap -sV --version-all -p <ports> target
```

Then validate manually.

## Mistake 6: Running intrusive NSE scripts without approval

Problem:

- Some scripts can affect service state.

Better:

```bash
nmap --script "default or safe" target
```

## Mistake 7: Forgetting exclusions

Problem:

- Scanning fragile or out-of-scope systems creates operational risk.

Better:

```bash
nmap -iL scope.txt --excludefile exclude.txt
```

---

# 24. Practice Labs

## Lab 1: Basic host scan

Goal:

- Understand basic output.

Command:

```bash
nmap 192.168.56.10
```

Tasks:

- Identify open ports.
- Identify closed or filtered ports.
- Save output.

## Lab 2: Host discovery

Goal:

- Discover live systems in a lab subnet.

Command:

```bash
nmap -sn 192.168.56.0/24
```

Tasks:

- Count live hosts.
- Compare results with ARP table.
- Repeat with `-n`.

## Lab 3: Full TCP discovery

Goal:

- Find nonstandard services.

Command:

```bash
sudo nmap -Pn -sS -p- --open 192.168.56.10
```

Tasks:

- Compare with default scan.
- Identify services outside top 1000 ports.

## Lab 4: Service enumeration

Goal:

- Identify service versions.

Command:

```bash
nmap -Pn -sV -sC -p <open_ports> 192.168.56.10
```

Tasks:

- Record versions.
- Identify missing banners.
- Try `--version-all`.

## Lab 5: UDP discovery

Goal:

- Understand UDP uncertainty.

Command:

```bash
sudo nmap -Pn -sU --top-ports 100 192.168.56.10
```

Tasks:

- Identify `open`, `open|filtered`, and `closed` results.
- Recheck with `-sV`.

## Lab 6: NSE script review

Goal:

- Learn script selection.

Commands:

```bash
nmap --script-help http-title
```

```bash
nmap --script http-title -p 80 192.168.56.10
```

Tasks:

- Review script help.
- Run one script.
- Compare with `-sC`.

## Lab 7: Timing impact

Goal:

- Understand scan speed and accuracy.

Commands:

```bash
nmap -T2 192.168.56.10
```

```bash
nmap -T4 192.168.56.10
```

Tasks:

- Compare time.
- Compare results.
- Check if faster timing missed anything.

## Lab 8: Output management

Goal:

- Produce professional evidence.

Command:

```bash
nmap -sV -oA lab-output 192.168.56.10
```

Tasks:

- Review `.nmap`, `.xml`, and `.gnmap`.
- Convert XML to HTML if desired.
- Compare with future scan using Ndiff.

---

# 25. Quick Reference Tables

## Target options

| Option | Use case |
|---|---|
| `-iL file` | Read targets from file |
| `-iR n` | Random targets for approved research only |
| `--exclude` | Exclude specific hosts |
| `--excludefile` | Exclude hosts from file |
| `--resolve-all` | Scan all resolved IPs |
| `--unique` | Avoid duplicate IP scans |

## Host discovery options

| Option | Use case |
|---|---|
| `-sL` | List targets only |
| `-sn` | Host discovery only |
| `-Pn` | Skip discovery and treat hosts as online |
| `-PR` | ARP discovery |
| `-PE` | ICMP echo discovery |
| `-PP` | ICMP timestamp discovery |
| `-PM` | ICMP address mask discovery |
| `-PS` | TCP SYN discovery |
| `-PA` | TCP ACK discovery |
| `-PU` | UDP discovery |
| `-PO` | IP protocol discovery |
| `-n` | Disable DNS |
| `-R` | Force DNS |
| `--dns-servers` | Use custom DNS servers |
| `--traceroute` | Trace route to host |

## Scan techniques

| Option | Technique | Use case |
|---|---|---|
| `-sS` | TCP SYN | Standard privileged TCP scan |
| `-sT` | TCP connect | Non-root TCP scan |
| `-sU` | UDP | UDP service discovery |
| `-sA` | TCP ACK | Firewall rule mapping |
| `-sW` | TCP Window | Advanced firewall analysis |
| `-sM` | TCP Maimon | Advanced stack behavior testing |
| `-sN` | TCP NULL | Advanced stack behavior testing |
| `-sF` | TCP FIN | Advanced stack behavior testing |
| `-sX` | TCP Xmas | Advanced stack behavior testing |
| `--scanflags` | Custom TCP flags | Lab and firewall validation |
| `-sY` | SCTP INIT | SCTP service discovery |
| `-sZ` | SCTP COOKIE-ECHO | SCTP testing |
| `-sO` | IP protocol | Supported IP protocol discovery |
| `-b` | FTP bounce | Legacy lab use |
| `-sI` | Idle scan | Advanced lab use |

## Port options

| Option | Use case |
|---|---|
| `-p 80` | One port |
| `-p 22,80,443` | Multiple ports |
| `-p 1-1000` | Port range |
| `-p-` | All TCP ports |
| `-p T:80,U:53` | Protocol specific ports |
| `-F` | Fast scan |
| `--top-ports n` | Most common ports |
| `--port-ratio r` | Ports above frequency ratio |
| `--exclude-ports` | Avoid specified ports |
| `-r` | Sequential port order |

## Version and OS options

| Option | Use case |
|---|---|
| `-sV` | Service and version detection |
| `--version-light` | Faster light probing |
| `--version-intensity n` | Set probe intensity |
| `--version-all` | Try all probes |
| `--version-trace` | Debug version detection |
| `--allports` | Do not exclude any ports from version detection |
| `-O` | OS detection |
| `--osscan-limit` | Only attempt OS detection on promising hosts |
| `--osscan-guess` | More aggressive OS guessing |
| `--max-os-tries n` | Limit OS detection retries |

## NSE options

| Option | Use case |
|---|---|
| `-sC` | Default scripts |
| `--script=default` | Default scripts |
| `--script=safe` | Safe category scripts |
| `--script=vuln` | Vulnerability scripts with authorization |
| `--script name` | Run a specific script |
| `--script "http-*"` | Wildcard script selection |
| `--script-args` | Provide script arguments |
| `--script-args-file` | Provide script arguments from file |
| `--script-help` | Show script help |
| `--script-trace` | Trace script network data |
| `--script-updatedb` | Update script database |

## Timing options

| Option | Use case |
|---|---|
| `-T0` to `-T5` | Timing template |
| `--min-rate` | Minimum packet rate |
| `--max-rate` | Maximum packet rate |
| `--max-retries` | Limit retries |
| `--host-timeout` | Give up on target after time |
| `--scan-delay` | Delay between probes |
| `--max-scan-delay` | Cap dynamic scan delay |
| `--min-rtt-timeout` | Minimum RTT timeout |
| `--max-rtt-timeout` | Maximum RTT timeout |
| `--initial-rtt-timeout` | Initial RTT timeout |
| `--min-hostgroup` | Minimum host group size |
| `--max-hostgroup` | Maximum host group size |
| `--min-parallelism` | Minimum outstanding probes |
| `--max-parallelism` | Maximum outstanding probes |
| `--defeat-rst-ratelimit` | Faster TCP triage with accuracy tradeoff |
| `--defeat-icmp-ratelimit` | Faster UDP triage with accuracy tradeoff |

## Output options

| Option | Use case |
|---|---|
| `-oN file` | Normal output |
| `-oX file` | XML output |
| `-oG file` | Grepable output |
| `-oA basename` | All major formats |
| `--append-output` | Append to output file |
| `--resume file` | Resume aborted scan |
| `-v`, `-vv` | Verbosity |
| `-d`, `-dd` | Debugging |
| `--reason` | Show host and port reasons |
| `--open` | Show open ports only |
| `--packet-trace` | Show packets sent and received |
| `--iflist` | Show interfaces and routes |
| `--stats-every` | Periodic progress output |
| `--noninteractive` | Disable runtime interaction |

## Miscellaneous options

| Option | Use case |
|---|---|
| `-6` | IPv6 scan |
| `-A` | OS, version, default scripts, traceroute |
| `--traceroute` | Trace path |
| `--datadir` | Custom data directory |
| `--servicedb` | Custom services database |
| `--versiondb` | Custom version probe database |
| `--send-eth` | Raw Ethernet sending |
| `--send-ip` | Raw IP sending |
| `--privileged` | Assume privileged user |
| `--unprivileged` | Assume unprivileged user |
| `-V` | Print version |
| `-h` | Help summary |

---

# 26. Sources Reviewed

Primary source:

- Nmap Reference Guide: https://nmap.org/book/man.html
- Options Summary: https://nmap.org/book/man-briefoptions.html
- Target Specification: https://nmap.org/book/man-target-specification.html
- Host Discovery: https://nmap.org/book/man-host-discovery.html
- Port Scanning Basics: https://nmap.org/book/man-port-scanning-basics.html
- Port Scanning Techniques: https://nmap.org/book/man-port-scanning-techniques.html
- Port Specification and Scan Order: https://nmap.org/book/man-port-specification.html
- Service and Version Detection: https://nmap.org/book/man-version-detection.html
- OS Detection: https://nmap.org/book/man-os-detection.html
- Nmap Scripting Engine: https://nmap.org/book/man-nse.html
- Timing and Performance: https://nmap.org/book/man-performance.html
- Firewall and IDS Evasion and Spoofing: https://nmap.org/book/man-bypass-firewalls-ids.html
- Output: https://nmap.org/book/man-output.html
- Miscellaneous Options: https://nmap.org/book/man-misc-options.html
- Runtime Interaction: https://nmap.org/book/man-runtime-interaction.html
- Examples: https://nmap.org/book/man-examples.html
- Legal Notices: https://nmap.org/book/man-legal.html

Operational principle:

- This handbook is designed for authorized security testing, defensive validation, network inventory, cyber range training, and professional reporting.
