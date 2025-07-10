# 📘 Basic Linux Commands for Beginners (L1 Level)

Welcome to the `cybersecurity-note` repository — this document provides a beginner-friendly breakdown of essential Linux commands. These commands are foundational for L1-level learners stepping into system administration, cybersecurity labs, or ethical hacking environments.

Each command includes:

* ✅ Basic definition
* 💡 Practical example
* 🎯 When and why to use
* 🖥️ Real-world usage reference

---

## 📂 1. Basic Linux Commands

| Command       | Example                    | Purpose                             |
| ------------- | -------------------------- | ----------------------------------- |
| `ls`          | `ls`                       | Lists directory contents            |
| `cd`          | `cd /var/log`              | Changes directory                   |
| `pwd`         | `pwd`                      | Shows current directory path        |
| `mkdir`       | `mkdir test_dir`           | Creates a new directory             |
| `rmdir`       | `rmdir test_dir`           | Deletes an empty directory          |
| `touch`       | `touch file.txt`           | Creates a new empty file            |
| `rm`          | `rm file.txt`              | Removes a file                      |
| `cp`          | `cp file.txt /tmp/`        | Copies a file                       |
| `mv`          | `mv file.txt backup.txt`   | Moves or renames a file             |
| `man`         | `man ls`                   | Opens the manual page for a command |
| `echo`        | `echo "Hello World"`       | Prints a message to terminal        |
| `chmod`       | `chmod 755 script.sh`      | Changes file permissions            |
| `chown`       | `chown user:user file.txt` | Changes file ownership              |
| `ps`          | `ps aux`                   | Lists active processes              |
| `kill`        | `kill 1234`                | Terminates process by PID           |
| `top`         | `top`                      | Real-time system monitoring         |
| `df`          | `df -h`                    | Shows disk space usage              |
| `du`          | `du -sh /etc/`             | Shows directory size                |
| `cat`         | `cat file.txt`             | Displays file content               |
| `nano` / `vi` | `nano file.txt`            | Edits a file in terminal            |
| `clear`       | `clear`                    | Clears terminal screen              |
| `exit`        | `exit`                     | Logs out from terminal              |

---

## 🌐 2. Networking Commands

| Command             | Example                 | Use Case                            |
| ------------------- | ----------------------- | ----------------------------------- |
| `ping`              | `ping google.com`       | Check connectivity to a remote host |
| `ifconfig` / `ip a` | `ip a`                  | View network interface details      |
| `netstat`           | `netstat -tuln`         | View active ports and services      |
| `traceroute`        | `traceroute google.com` | View route packets take             |
| `nslookup`          | `nslookup example.com`  | DNS lookup for domain/IP mapping    |
| `dig`               | `dig example.com`       | Advanced DNS queries                |
| `route`             | `route -n`              | View routing table                  |
| `ss`                | `ss -tuln`              | Analyze network socket connections  |
| `iwconfig`          | `iwconfig`              | Wireless interface settings         |

---

## ⚙️ 3. Service Management

| Command      | Example                 | Purpose                    |
| ------------ | ----------------------- | -------------------------- |
| `systemctl`  | `systemctl status ssh`  | Manage systemd services    |
| `service`    | `service apache2 start` | Init-based service control |
| `journalctl` | `journalctl -u ssh`     | View service logs          |
| `chkconfig`  | `chkconfig --list`      | Manage SysV services       |
| `ufw`        | `ufw allow 22/tcp`      | Firewall rule management   |

---

## 🧑‍💻 4. System Administration

| Command             | Example                            | Usage                    |
| ------------------- | ---------------------------------- | ------------------------ |
| `sudo`              | `sudo apt update`                  | Run command as superuser |
| `apt-get/yum/dnf`   | `sudo apt install nmap`            | Install packages         |
| `passwd`            | `passwd`                           | Change user password     |
| `useradd/userdel`   | `useradd testuser`                 | Manage user accounts     |
| `groupadd/groupdel` | `groupadd devs`                    | Manage groups            |
| `free`              | `free -m`                          | Show system memory       |
| `uname`             | `uname -a`                         | System information       |
| `lshw`              | `lshw -short`                      | Hardware summary         |
| `shutdown/reboot`   | `shutdown -h now`                  | Power off or reboot      |
| `crontab`           | `crontab -l`                       | View scheduled tasks     |
| `tar`               | `tar -czvf archive.tar.gz folder/` | Archive files            |
| `gzip/gunzip`       | `gzip file.txt`                    | Compress/decompress      |

---

## 🗂️ 5. Linux Directory Structure Explained

| Directory | Description                                 |
| --------- | ------------------------------------------- |
| `/`       | Root directory of the Linux filesystem      |
| `/bin`    | Essential user binaries (e.g., ls, cat)     |
| `/etc`    | Configuration files                         |
| `/home`   | User home directories                       |
| `/var`    | Log files, databases, spool directories     |
| `/usr`    | Secondary hierarchy (apps/libraries)        |
| `/lib`    | Essential shared libraries                  |
| `/dev`    | Device files                                |
| `/tmp`    | Temporary files                             |
| `/opt`    | Optional/additional software packages       |
| `/sbin`   | System binaries for root user               |
| `/srv`    | Site-specific service data                  |
| `/proc`   | Kernel and process information (virtual FS) |
| `/sys`    | Interface to kernel subsystems              |
| `/run`    | Runtime process data                        |
| `/boot`   | Bootloader and kernel files                 |
| `/mnt`    | Temporary mount point                       |
| `/media`  | Mount point for external devices            |

---

## 🔐 License

This repository is licensed under the MIT License. Use responsibly.

---

Maintained by: **@hackwithvyshu**  |  Contact: [Vaishnavcv978@gmail.com](mailto:Vaishnavcv978@gmail.com)
