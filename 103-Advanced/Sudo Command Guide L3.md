# Sudo Command Guide for Advanced (L3 Level)

This document outlines high-level, production-grade `sudo` management practices for L3-level professionals, focusing on secure privilege delegation, enterprise-grade auditability, command abuse mitigation, and integration with cybersecurity and system operations.

---

## Advanced `sudo` Purpose and Governance

* Enforces **role-based access control (RBAC)** via `/etc/sudoers.d/`
* Enables **Zero Trust** by limiting commands and user scope
* Supports **compliance auditing** (ISO, PCI-DSS, SOC2)
* Reduces attack surface by restricting root-level exposure

---

## Command Structure and Delegation Control

### Advanced Syntax

```bash
sudo -u <user> -g <group> <command>
```

**Example:**

```bash
sudo -u nginx -g webgrp systemctl restart nginx
```

Used to restart Nginx as the exact user/group service account.

---

## Real-World High-Level Scenarios

### Scenario 1: Controlled Software Deployment Pipeline Access

**Command:**

```bash
sudo /usr/local/bin/deploy-app-prod.sh
```

**Role:** CI/CD automation engineer
**Why:** Limit production deployment access without full root privileges
**Sudoers Entry:**

```bash
cicd ALL=(ALL) NOPASSWD: /usr/local/bin/deploy-app-prod.sh
```

**Security Note:** Script has strict input validation + logging to `/var/log/deploy.log`

---

### Scenario 2: Privilege Escalation Defense via Logging and Alerting

**Toolchain:** `sudo` + `auditd` + `Wazuh`
**Command for audit:**

```bash
aureport --user | grep sudo
```

**Why:** Detect anomalies in sudo execution (e.g., time anomalies, off-hours usage)
**Integration:** Log forwarding to SIEM (Splunk, QRadar)
**Impact:** Real-time alerting on sensitive command execution.

---

### Scenario 3: Red Team Simulation – Limiting Exploitable Sudo Paths

**Command:**

```bash
sudo find / -name "*.log"
```

**Problem:** Overprivileged sudo rules allow unintended file enumeration.
**Solution:** Restrict rule:

```bash
redteam ALL=(ALL) NOPASSWD: /usr/bin/find /var/log
```

**Result:** Reduces lateral movement vectors during simulated breaches.

---

### Scenario 4: Managing `sudo` Inside Docker Microservices

**Use Case:** Running privileged maintenance tasks inside isolated containers.
**Command:**

```bash
sudo docker exec -u root service-container /usr/local/bin/patch.sh
```

**Why:** Minimal base images often lack `sudo`; container entrypoints can enforce role-based sudo behavior within orchestrated tasks.
**Note:** Dockerfile must include `sudo` and proper `/etc/sudoers.d` config.

---

### Scenario 5: CI/CD Pipeline Secure Execution with `sudo`

**Use Case:** Restrict pipeline scripts to predefined, controlled command set.
**Command in pipeline script:**

```bash
sudo /usr/bin/systemctl reload nginx
```

**Sudoers Configuration:**

```bash
jenkins ALL=(ALL) NOPASSWD: /usr/bin/systemctl reload nginx
```

**Reason:** Prevent arbitrary command execution from CI tool contexts.

---

### Scenario 6: Enforcing Whitelisted Command Execution for IT Operations

**Use Case:** Allow only a specific binary and subcommand set.
**Command:**

```bash
sudo /usr/local/bin/bkp.sh start
```

**Sudoers Restriction:**

```bash
Cmnd_Alias BACKUP_CMDS = /usr/local/bin/bkp.sh start, /usr/local/bin/bkp.sh status
backupop ALL=(ALL) BACKUP_CMDS
```

**Impact:** Prevent execution of unapproved subcommands or script misuse.

---

## Best Practices for L3 Administrators

* Maintain all sudo custom rules under `/etc/sudoers.d/`, never edit `/etc/sudoers` directly
* Use `sudoedit` or `visudo` with `EDITOR=vim` to prevent syntax errors
* Use `Cmnd_Alias` and `Runas_Alias` for modular policy definitions
* Audit and rotate `sudo` rules every quarter as part of RBAC review

---

## Advanced Logging & Integrity Control

### Enable TTY Logging with Time Stamp

```bash
Defaults logfile="/var/log/sudo_log"
Defaults log_input,log_output
Defaults iolog_dir="/var/log/sudo-io"
```

**Outcome:** Full session recording including keystrokes

### Integrate with Immutable Audit Trails (e.g., WORM)

* Forward logs to secure immutable storage (e.g., Amazon S3 with object lock)
* Use `auditd` rules like:

```bash
-w /etc/sudoers -p wa -k sudo-policy
```

---

## Granular Policy with Tags and Environment Restrictions

```bash
Defaults!REBOOT !env_reset
adminuser ALL=(ALL) NOPASSWD: /sbin/reboot
```

**Use Case:** Permit reboot without env sanitization (only for isolated physical lab)

```bash
Cmnd_Alias BACKUPS = /usr/bin/rsync, /usr/bin/tar
backupuser ALL=(ALL:ALL) BACKUPS
```

**Use Case:** Backup role with explicit file-level privileges

---

## Integrating `sudo` with LDAP / AD

1. Configure `/etc/nsswitch.conf`:

```bash
sudoers: files ldap
```

2. Set up LDAP schema with `sudoRole` objectClass
3. Manage centralized sudo access using OpenLDAP or Microsoft AD + SSSD

---

## Defense-In-Depth Use Case: `sudo` Abuse Detection

| Indicator                    | Description                                | Remediation                          |
| ---------------------------- | ------------------------------------------ | ------------------------------------ |
| Excessive `sudo` frequency   | Multiple invocations in < 30 sec           | Throttle access with PAM rate limits |
| `sudo` used for shell access | `sudo bash`, `sudo su`                     | Disable interactive shell rules      |
| Unexpected runas target      | e.g., `sudo -u postgres` from non-DB users | Add `Runas_Alias` validation         |

---

## Hardening Checklist

* [x] Use named aliases and centralized rule management
* [x] Restrict shell access with `!requiretty` where applicable
* [x] Log all sudo usage with timestamps
* [x] Periodically grep for wildcard abuse:

```bash
grep -R 'ALL' /etc/sudoers* | grep -v '#' | grep -i NOPASSWD
```

* [x] Monitor sudo policy changes:

```bash
auditctl -w /etc/sudoers.d/ -p wa -k sudopolicychange
```

---

## Conclusion

At the L3 level, `sudo` is no longer just a command — it is a governed system interface that enforces trust, audits activity, supports compliance, and enables granular privilege access across teams. Mastery of `sudo` operations is essential for cybersecurity and infrastructure leads.

---

Maintained by: **[Vaishnavu C V](https://github.com/vaishnavucv)**
Principal CyberSecurity Engineer | Ethical Hacker | Cyber Range Mentor
