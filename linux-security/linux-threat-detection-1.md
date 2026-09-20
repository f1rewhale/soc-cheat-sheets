# Linux Threat Detection: Initial Access Cheat Sheet

A technical reference guide focusing on detecting **Initial Access (TA0001)** vectors in Linux environments, including SSH brute-force attempts, web service exploitation, and compromised service accounts.

---

## 1. SSH Authentication Threats

SSH is the primary target for remote access attacks on Linux servers.

### Log Artifacts & Event Patterns
| Artifact / Log Source | Log Message Pattern | Threat Indicator |
| :--- | :--- | :--- |
| `/var/log/auth.log`<br>`/var/log/secure` | `Failed password for [user] from [IP] port [port] ssh2` | Authentication failure (brute-force / password spraying) |
| `/var/log/auth.log`<br>`/var/log/secure` | `Failed password for invalid user [user] from [IP]` | User enumeration / dictionary attack |
| `/var/log/auth.log`<br>`/var/log/secure` | `Accepted publickey for [user] from [IP]` | Successful login via SSH key (verify source IP and key footprint) |
| `/var/log/auth.log`<br>`/var/log/secure` | `Accepted password for [user] from [IP]` | Successful password login (flag if password auth is disabled by policy) |

---

## 2. Web Service Breaches & Web Shells

Attackers exploit vulnerable web applications (e.g., Apache, Nginx) to drop web shells and execute arbitrary commands.

### Detection Artifacts
| Log Source / Mechanism | Detection Rule | Threat Context |
| :--- | :--- | :--- |
| **Access Logs** (`/var/log/apache2/access.log`, `/var/log/nginx/access.log`) | High volume of `POST` requests to upload directories (`/uploads/`, `/wp-content/uploads/`) | Web shell deployment or file inclusion exploit |
| **Auditd Logs** (`/var/log/audit/audit.log`) | Process creation where `www-data` or `apache` spawns `sh`, `bash`, `python`, or `nc` | Web shell execution / remote command execution (RCE) |
| **File Integrity / Syslog** | Creation of executable files (`.php`, `.py`, `.sh`) in web root directories | Persistence via webshell |

---

## 3. Advanced Initial Access Techniques

| Vector | Artifact / Log Indicator | SOC Hunting Focus |
| :--- | :--- | :--- |
| **Compromised Service Accounts** | Interactive shell login (`/bin/bash`) by non-human accounts (`www-data`, `nobody`, `daemon`) | System accounts should typically have `/bin/false` or `/sbin/nologin` in `/etc/passwd` |
| **Exposed API / Management Ports** | Outbound/Inbound traffic anomalies on unstandardized ports (e.g., Docker API `2375`, Redis `6379`) | Direct exposure of internal management ports without authentication |

---

## SOC Analyst Notes

1. **Brute-Force Thresholds:** Monitor for a rapid spike (>20 events/min) of `Failed password` logs from a single IP, followed immediately by an `Accepted` event.
2. **Web Server Process Spawning:** Service accounts like `www-data` should **never** spawn interactive command interpreters (`/bin/bash`, `sh`). Treat any shell creation under a web server daemon as a critical incident.
3. **Authorized Keys Monitoring:** Watch for write events to `~/.ssh/authorized_keys` — adding an SSH key is a common persistence move immediately following initial access.
4. The process tree analysis!!!
