# Sudo Command Guide for Intermediate (L2 Level)

This document is a structured reference for L2-level professionals to manage and secure `sudo` usage in Linux environments. It builds upon the fundamentals and introduces policy tuning, auditing, command restrictions, and real-world operational scenarios.

---

## What is `sudo`?

`sudo` allows a permitted user to execute commands as the superuser or another user, as defined by the policy in `/etc/sudoers` or under `/etc/sudoers.d/`. At the L2 level, users must understand its role in identity, privilege escalation, and control.

---

## Why Advanced `sudo` Understanding Matters

* Enforces **least privilege** across teams
* Enables secure delegation of admin roles
* Enhances **auditability** and **accountability** in multi-user systems
* Prevents **misuse of root powers** via command restrictions

---

## Syntax Refresher

```bash
sudo [options] [command]
```

**Example:**

```bash
sudo -u postgres psql
```

Run `psql` as the `postgres` user.

---

## Command Examples (with context)

| Task                          | Command                   | Context                                                             |
| ----------------------------- | ------------------------- | ------------------------------------------------------------------- |
| Run a command as another user | `sudo -u www-data whoami` | Debug permissions on web server processes                           |
| List allowed commands         | `sudo -l`                 | View what the current user can execute                              |
| Run command without password  | `sudo -n ls /root`        | Used in automation where interaction is not permitted               |
| View detailed sudo version    | `sudo -V`                 | Check version and compile options for compatibility/security review |

---

## `sudo` Workflow (with policy enforcement)

1. User issues `sudo` command
2. PAM + `/etc/sudoers`/`sudoers.d/*` policy is evaluated
3. TTY, time constraints, command whitelist, and group constraints checked
4. `sudo` executes command or returns error/logs rejection
5. Logs sent to `/var/log/auth.log` or syslog

---

## Managing Sudo Privileges (Intermediate)

### Grant Access to Specific Commands

```bash
sudo visudo
```

Example:

```bash
webadmin ALL=(ALL) NOPASSWD: /bin/systemctl restart apache2
```

User `webadmin` can only restart Apache, nothing else.

### Enforce Command Restrictions by Group

```bash
%devops ALL=(ALL) /usr/bin/htop, /usr/bin/systemctl status
```

Group-based access for DevOps users.

### Define Secure Aliases

```bash
Cmnd_Alias MONITORING = /usr/bin/top, /usr/bin/htop
```

```bash
sysuser ALL=(ALL) MONITORING
```

---

## Creating Users with Controlled Access

### With Command Restrictions

```bash
sudo adduser monitoruser
sudo usermod -aG sudo monitoruser
sudo visudo
```

Add:

```bash
monitoruser ALL=(ALL) /usr/bin/htop, /bin/journalctl
```

### Without Root Access

* Create user:

```bash
sudo adduser audituser
```

* No `sudo` group assignment

---

## Audit and Monitor `sudo` Activity

### View Logs

```bash
grep sudo /var/log/auth.log
```

### Tail in Real-Time

```bash
tail -f /var/log/auth.log | grep sudo
```

### Enable Persistent Auditing with auditd

```bash
sudo apt install auditd
sudo systemctl enable auditd
```

Track:

```bash
ausearch -m USER_CMD -x sudo
```

### Integrate with Centralized Log Systems

Forward logs to: Splunk, ELK, Wazuh, etc.

---

## Best Practices (L2 Operations)

* Never use `NOPASSWD` for critical actions
* Define minimal command sets per role/user
* Use `sudo -u` for service user impersonation
* Configure `/etc/sudoers.d/` instead of editing `sudoers` directly
* Run `visudo` to validate syntax

---

## Troubleshooting & Hardening

| Problem              | Cause              | Fix                                 |
| -------------------- | ------------------ | ----------------------------------- |
| Sudo hangs on script | TTY requirement    | Add `Defaults !requiretty` (RedHat) |
| Audit trail missing  | syslog not enabled | Check rsyslog or journald setup     |
| Excessive privilege  | Wildcards in rule  | Replace `ALL` with exact commands   |

---

## `sudo` vs. `doas`

* `doas` is a simpler alternative to `sudo`, used in hardened environments
* Not as flexible, but preferred in minimal OS setups (e.g., Alpine)

---

## Conclusion

An L2-level engineer should enforce, monitor, and audit `sudo` behavior with a focus on reducing risk and improving visibility. Mastering `sudo` policies prepares users for privilege management, role-based access, and enterprise-scale Linux administration.

---

Maintained by: **[Vaishnavu C V](https://github.com/vaishnavucv)**
Principal CyberSecurity Engineer | Ethical Hacker | Cyber Range Mentor
