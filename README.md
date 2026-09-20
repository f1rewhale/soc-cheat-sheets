# SOC & Blue Team Security Cheat Sheets

Welcome to my personal Security Operations Center (SOC) reference repository. This space contains structured cheat sheets, Event ID mappings, and quick-reference guides built while training in Threat Detection, Incident Response, and Log Analysis.

## Available Cheat Sheets

### Windows Security
* **[Windows Logging & Event IDs](windows-security/windows-logging-cheat-sheet.md)** — Core Windows Security Event Logs, Sysmon, and PowerShell auditing parameters.

#### Windows Threat Detection Series
  * **[Part 1: Initial Access](windows-security/windows-threat-detection-1.md)** — RDP brute-force, phishing execution chains, and removable media threats.
  * **[Part 2: Post-Exploitation](windows-security/windows-threat-detection-2.md)** — Discovery commands, Data Collection/Staging, and Ingress Tool Transfer (Certutil, BITSAdmin).
  * **[Part 3: Persistence & C2](windows-security/windows-threat-detection-3.md)** — Scheduled Tasks, Service creation, Run Key modifications, and Shadow Copy deletion.

### Linux Security
  * **[Linux Logging for SOC](linux-security/linux-logging-cheat-sheet.md)** — Analyzing `/var/log`, `journalctl` queries, and `auditd` rules.

---

## Purpose

These guides are optimized for quick lookup during threat hunting, log analysis, and investigation challenges.

*Created and maintained as part of continuous Blue Team learning.*
