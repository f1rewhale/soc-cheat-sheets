# Linux Threat Detection: Post-Exploitation & Discovery Cheat Sheet

A technical reference guide for SOC analysts covering detection techniques for **Discovery (TA0007)**, post-compromise activity, and unauthorized resource utilization (Cryptomining) in Linux environments.

---

## 1. Internal System & Network Discovery

Attackers execute native Linux utilities to map out system configurations, network topology, and user privileges immediately after gaining entry.

### Core Discovery Commands & Detection Indicators
| Category | Native Utilities / Commands | Auditd / Log Artifacts | Detection Pattern |
| :--- | :--- | :--- | :--- |
| **System & Kernel** | `uname -a`, `cat /etc/os-release`, `lscpu` | `auditd` (`EXECVE`) | System enumeration prior to local privilege escalation |
| **User & Groups** | `id`, `who`, `w`, `cat /etc/passwd`, `last` | `auditd` (`EXECVE`) | Rapid user context and logged-in user checks |
| **Network Discovery** | `ip a`, `ifconfig`, `netstat -tulnp`, `ss -tulnp` | `auditd` (`EXECVE`) | Mapping listening ports and internal interface ranges |
| **Process Enumeration** | `ps aux`, `top`, `htop` | `auditd` (`EXECVE`) | Enumerating active processes and security agents |

---

## 2. Resource Hijacking & Cryptomining Detection

A frequent motive for Linux server breaches is deploying unauthorized cryptocurrency miners (e.g., XMRig).

### Detection Vectors & Indicators
| Artifact / Metric | Indicator | Analysis Command |
| :--- | :--- | :--- |
| **CPU / Memory Usage** | Sustained high CPU utilization (>90-100%) by unfamiliar processes | `top`, `htop`, `pidstat 1` |
| **Process Disguising** | Process masquerading under system names (e.g., `./systemd`, `./kworker`) executing out of `/tmp` or `/dev/shm` | `ls -l /proc/<PID>/exe` |
| **Outbound C2 / Stratum** | Network connections to mining pools over non-standard ports (e.g., `3333`, `4444`, `14444`) or Stratum protocol | `ss -tpn`, `netstat -anp` |
| **Hidden Directories** | Execution of binaries located in world-writable paths (`/tmp/`, `/var/tmp/`, `/dev/shm/`) | `find /tmp /var/tmp /dev/shm -type f -executable` |

---

## SOC Analyst Notes

1. **World-Writable Path Execution:** Legitimate system binaries almost never execute out of `/tmp` or `/dev/shm`. Flag any process running from these directories as malicious.
2. **Process Executable Verification:** Always inspect `/proc/<PID>/exe` using `ls -l` to verify the actual disk location of a suspicious process, as attackers often rename binaries to mimic legitimate daemons.
3. **Automated Discovery Scripting:** A high-frequency cluster of discovery commands (`id` -> `uname -a` -> `ip a` -> `netstat`) within seconds indicates automated reconnaissance scripts (e.g., LinPEAS).
