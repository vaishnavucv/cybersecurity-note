# Sudo Command Guide for Beginners (L1 Level)

This document provides a complete beginner-friendly guide to understanding and using the `sudo` command in Linux-based systems. It is tailored for Level 1 (L1) learners and junior cybersecurity/system administration professionals.

---

## What is `sudo`?

`sudo` stands for "superuser do". It allows a permitted user to execute a command as the superuser or another user, as specified by the security policy (usually `/etc/sudoers`).

**Use Case:** It is used to perform administrative tasks without switching to the root account.

---

## Why is `sudo` Important?

* Maintains system security by limiting root access
* Tracks which user ran privileged commands (audit-friendly)
* Prevents mistakes by enforcing controlled access to powerful commands
* Encourages least privilege principle

---

## Syntax of `sudo`

```bash
sudo [option] [command]
```

**Example:**

```bash
sudo apt update
```

This updates the package list using elevated permissions.

---

## Common `sudo` Command Examples

| Task             | Command                          | Scenario                         |
| ---------------- | -------------------------------- | -------------------------------- |
| Update packages  | `sudo apt update`                | Keeping system packages updated  |
| Install software | `sudo apt install nmap`          | Installing a new tool like Nmap  |
| Edit system file | `sudo nano /etc/hosts`           | Modify system-level config files |
| Reboot system    | `sudo reboot`                    | Restarting system remotely       |
| Change ownership | `sudo chown user:group file.txt` | Grant ownership of a file        |
| Add a user       | `sudo useradd devuser`           | Add new user to system           |

---

## Workflow of `sudo`

1. **User types command with `sudo`**
2. **System checks `/etc/sudoers` for permission**
3. **If permitted, the system prompts for the user’s password**
4. **If password is correct and permission granted, command runs as root**
5. **Audit logs are updated with execution details**

---

## Configure `sudo` Access

### Step 1: Add user to `sudo` group (Debian/Ubuntu)

```bash
sudo usermod -aG sudo username
```

### Step 2: Verify

```bash
sudo -l
```

Shows what commands the user is allowed to run with sudo.

---

## Creating Users: With and Without `sudo`

### Scenario A: Create a regular user **with** `sudo`

```bash
sudo adduser devadmin
sudo usermod -aG sudo devadmin
```

This user can now use `sudo`.

### Scenario B: Create a regular user **without** `sudo`

```bash
sudo adduser internuser
```

This user cannot run administrative commands unless explicitly added to `sudo` group.

---

## Best Practices

* Use `sudo` only when necessary
* Avoid running full shells as root: `sudo -i` or `sudo su`
* Monitor `/var/log/auth.log` for sudo activities
* Configure minimal privilege using `visudo`

---

## Enabling `sudo` Usage Tracking and Monitoring

Monitoring `sudo` usage is critical for auditing and maintaining security. Here’s how to enable and track sudo activities in a simple way:

### 1. View Sudo Logs

```bash
sudo cat /var/log/auth.log | grep sudo
```

**Purpose**: Displays all sudo activity including who used it and what command was run.

### 2. Tail Sudo Logs in Real Time

```bash
sudo tail -f /var/log/auth.log | grep sudo
```

**Purpose**: Continuously monitor sudo usage as it happens.

### 3. Add a Sudo Log Alias (Optional for L1)

Edit `.bashrc` to add:

```bash
alias sudolog='grep sudo /var/log/auth.log'
```

Then reload:

```bash
source ~/.bashrc
```

**Purpose**: Simplifies checking sudo logs with `sudolog` command.

### 4. Audit With `ausearch` (If auditd is enabled)

```bash
sudo ausearch -m USER_CMD -x sudo
```

**Purpose**: Queries auditd logs for all sudo commands.

> Note: `auditd` may need to be installed and started for this to work:

```bash
sudo apt install auditd
sudo systemctl enable auditd && sudo systemctl start auditd
```

---

## `sudo` vs `su`

| Feature                     | `sudo` | `su`                   |
| --------------------------- | ------ | ---------------------- |
| Runs single command as root | ✅      | ❌                      |
| Requires user’s password    | ✅      | ❌ (asks root password) |
| Audit logging               | ✅      | ❌                      |
| Temporary root shell        | ❌      | ✅                      |

---

## Grant Limited `sudo` Access (Example)

To allow a user to run only the reboot command:

```bash
sudo visudo
```

Add this line:

```bash
username ALL=(ALL) NOPASSWD: /sbin/reboot
```

---

## Troubleshooting `sudo`

| Issue                             | Cause                     | Resolution                        |
| --------------------------------- | ------------------------- | --------------------------------- |
| `user is not in the sudoers file` | Not part of `sudo` group  | Use root to add user to group     |
| `command not found`               | Misspelled or uninstalled | Double-check spelling and path    |
| `Permission denied`               | Wrong file permissions    | Use `ls -l` and correct ownership |

---

## Conclusion

Understanding `sudo` is critical for L1 Linux and cybersecurity practitioners. It enforces control, improves auditability, and limits damage from mistakes. Learn to use it wisely.

---

Maintained by: **[Vaishnavu C V](https://github.com/vaishnavucv)**
Principal CyberSecurity Engineer | Ethical Hacker | Cyber Range Mentor
