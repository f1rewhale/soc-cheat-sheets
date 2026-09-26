# Windows Logon Types

Quick reference for Windows logon types commonly encountered during SOC investigations.

| Logon Type | Name | Meaning | SOC Example |
|---|---|---|---|
| 2 | Interactive | Local logon | User signs in directly to workstation |
| 3 | Network | Network authentication | SMB / access to network resource |
| 4 | Batch | Batch logon | Scheduled task |
| 5 | Service | Service logon | Windows service starts |
| 7 | Unlock | Workstation unlock | User unlocks existing session |
| 8 | NetworkCleartext | Credentials passed in cleartext form to authentication package | Some web/network authentication scenarios |
| 9 | NewCredentials | Uses different credentials for outbound connections | `runas /netonly` |
| 10 | RemoteInteractive | Remote interactive session | RDP |
| 11 | CachedInteractive | Cached domain credentials | Domain user logs in while DC unavailable |
