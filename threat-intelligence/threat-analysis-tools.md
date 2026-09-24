# Threat Analysis Tools

A practical reference for threat intelligence enrichment and SOC investigations.

The purpose of threat intelligence enrichment is not simply to determine whether an indicator is "malicious" or "benign". The goal is to collect context from multiple sources, correlate the findings, and make an evidence-based decision.

A useful basic workflow is:

**Verify → Enrich → Correlate → Decide**

---

# File & Hash Analysis

## VirusTotal

**Website:** https://www.virustotal.com/

VirusTotal aggregates information from multiple security vendors and other data sources.

### Useful for

- Searching file hashes
- Checking detection results
- Identifying malware family labels
- Reviewing file metadata
- Finding contacted domains and IP addresses
- Reviewing behavioral information
- Pivoting between related indicators

### Useful indicators

When investigating a file, check:

- SHA-256 hash
- Detection ratio
- Threat labels
- First submission / first seen
- File type
- Digital signature
- Relations
- Contacted domains/IPs
- Behavioral activity

### Important

A detection ratio alone should not determine whether a file is malicious.

A file with few detections may still be malicious, especially if it is new. Always correlate VirusTotal results with other evidence.

---

## MalwareBazaar

**Website:** https://bazaar.abuse.ch/

MalwareBazaar is a malware intelligence and sample-sharing platform.

### Useful for

- Searching malware by hash
- Identifying malware families
- Finding malware tags
- Finding related samples
- Malware hunting
- Finding associated YARA rules
- Investigating malware campaigns

### Example search

A SHA-256 hash can be used to pivot from an unknown file to information about its malware family and related samples.

### Analyst note

MalwareBazaar is especially useful when VirusTotal results are unclear or when additional malware-specific context is needed.

---

# Sandbox Analysis

Static information tells us what a file looks like.

Sandbox analysis helps us understand what the file **does when executed**.

Typical information obtained from a sandbox includes:

- Spawned processes
- Command lines
- Registry modifications
- Dropped files
- Network connections
- Domains and IP addresses
- Extracted strings
- MITRE ATT&CK techniques

Never execute suspected malware directly on your normal system.

---

## Hybrid Analysis

**Website:** https://www.hybrid-analysis.com/

Hybrid Analysis is an automated malware analysis sandbox.

### Useful for

- Observing runtime behavior
- Process trees
- Network connections
- Extracted indicators
- Suspicious behavior
- MITRE ATT&CK mapping
- Searching existing malware reports

### Investigation idea

When analyzing a suspicious hash:

1. Search for an existing report.
2. Check the threat verdict.
3. Inspect the process tree.
4. Review command-line activity.
5. Look for contacted domains and IP addresses.
6. Check dropped files.
7. Review mapped MITRE ATT&CK techniques.

---

## Joe Sandbox

**Website:** https://www.joesandbox.com/

Joe Sandbox is a malware analysis platform that provides detailed dynamic analysis reports.

### Useful for

- File and URL analysis
- Process behavior
- System activity
- Network activity
- Strings
- Memory-related artifacts
- Detailed malware behavior analysis

Joe Sandbox can provide deeper technical information that may be useful during malware investigation or reverse engineering.

---

# Domain Analysis

A domain by itself provides limited information.

During enrichment, try to answer:

- When was the domain registered?
- Who owns or operates it?
- What IP addresses does it resolve to?
- Has the domain changed infrastructure?
- Does it have suspicious subdomains?
- What certificates are associated with it?
- Do threat intelligence sources report malicious activity?

---

## WHOIS

**Website:** https://lookup.icann.org/

WHOIS/RDAP information can provide registration information about a domain.

### Useful information

- Registration date
- Registrar
- Expiration date
- Nameservers
- Registration status

### Possible red flags

A very recently registered domain can be suspicious when combined with other evidence.

However:

**New domain ≠ malicious domain**

Always correlate registration information with other indicators.

---

## DNS Lookup

Useful DNS records include:

| Record | Purpose |
|---|---|
| A | Domain → IPv4 |
| AAAA | Domain → IPv6 |
| MX | Mail servers |
| NS | Authoritative nameservers |
| TXT | Text information / verification / email security data |
| CNAME | Alias to another hostname |

DNS information can help connect domains to infrastructure and identify additional indicators.

---

# IP Address Analysis

IP enrichment helps answer questions such as:

- Who owns this IP?
- Which ASN does it belong to?
- Where is it approximately located?
- Which services are exposed?
- Has it been associated with malicious activity?
- Is it a VPN, proxy, hosting server, or Tor exit node?

---

## VirusTotal

**Website:** https://www.virustotal.com/

VirusTotal can also be used for IP and domain enrichment.

### Look for

- Security vendor detections
- Communicating files
- Related domains
- Historical information
- Community information

This can help pivot from:

**IP → Domain → File → Hash**

or in the opposite direction.

---

## Shodan

**Website:** https://www.shodan.io/

Shodan is a search engine for Internet-connected systems and services.

### Useful for

- Identifying open ports
- Discovering exposed services
- Identifying software
- Inspecting service banners
- Finding Internet-facing infrastructure

### Example

An IP address may expose:

```text
22/tcp  SSH
80/tcp  HTTP
443/tcp HTTPS
3389/tcp RDP
```

This information provides additional context about the system behind an indicator.

### Important

An exposed service does not automatically mean that the host is malicious.

It is another piece of evidence.

---

## Censys

**Website:** https://search.censys.io/

Censys provides information about Internet-facing hosts, services, and certificates.

### Useful for

- Host investigation
- Open services
- TLS certificates
- Infrastructure discovery
- Domain/IP relationships

Censys and Shodan can complement each other when investigating Internet-facing infrastructure.

---

# VPN / Proxy Detection

Attackers may use VPNs, proxies, Tor nodes, hosting providers, or residential proxies to hide their original source.

However:

**VPN/proxy usage ≠ malicious activity**

It should be treated as an additional risk signal rather than proof.

---

## IP2Proxy

**Website:** https://www.ip2location.com/database/ip2proxy

IP2Proxy provides information about IP addresses associated with anonymization infrastructure.

### Can identify

- VPN
- Tor exit nodes
- Public proxies
- Web proxies
- Data-center/hosting IPs
- Residential proxies

### Useful for

Determining whether a suspicious connection may originate from anonymized or proxy infrastructure.

---

## Spur

**Website:** https://spur.us/

Spur provides intelligence about VPN, proxy, and anonymization infrastructure.

### Useful for

- VPN identification
- Proxy identification
- Residential proxy context
- Anonymization infrastructure analysis

Use this information together with other threat intelligence sources rather than treating proxy usage as a malicious verdict.

---

# IP Reputation

## AbuseIPDB

**Website:** https://www.abuseipdb.com/

AbuseIPDB is a crowdsourced database of IP addresses reported for abusive activity.

### Useful for

- Checking previous abuse reports
- Identifying reported scanning activity
- Brute-force activity
- Spam
- Other suspicious network behavior

### Important

Reports are crowdsourced.

Do not treat an AbuseIPDB score as definitive proof that an IP address is malicious.

Consider:

- Number of reports
- Recency
- Type of activity
- Number of independent reporters
- Other threat intelligence sources

---

## Cisco Talos Intelligence

**Website:** https://www.talosintelligence.com/reputation_center

Cisco Talos provides reputation and threat intelligence information.

### Useful for

- IP reputation
- Domain reputation
- Network ownership
- Email reputation
- Additional threat context

---

# Hashing Commands

Hashes allow analysts to identify a file regardless of its filename.

SHA-256 is generally preferred for identifying files during investigations.

## Windows CMD

```cmd
certutil -hashfile suspicious.exe SHA256
```

## PowerShell

```powershell
Get-FileHash -Algorithm SHA256 .\suspicious.exe
```

## Linux

```bash
sha256sum suspicious.exe
```

### Remember

Changing even one byte of a file changes its hash.

Also consider hashing both an archive and the extracted suspicious file when relevant.

---

# IOC Types

Common Indicators of Compromise include:

| Type | Example |
|---|---|
| File hash | SHA-256 |
| IPv4 / IPv6 | Suspicious host |
| Domain | C2 or phishing domain |
| URL | Malware download location |
| Filename | Suspicious executable |
| File path | Unusual execution location |
| Email address | Phishing infrastructure |

An IOC should include context whenever possible.

Instead of recording only:

```text
example.com
```

record why it matters:

```text
Domain: example.com
Observed: PowerShell network connection
Source: EDR alert
Role: suspected payload hosting
```

Context makes threat intelligence useful.

---

# File & Path Heuristics

Before using external intelligence sources, inspect the basic artifact.

Potentially suspicious patterns include:

### Double extensions

```text
invoice.pdf.exe
```

### System binary impersonation

```text
scvhost.exe
```

instead of:

```text
svchost.exe
```

### Suspicious locations

Examples:

```text
C:\Users\Public\
C:\Windows\Temp\
C:\ProgramData\
```

These locations are not malicious by themselves.

The combination of **filename + path + execution context + behavior** matters.

---

# Practical SOC Workflow

When investigating an unknown indicator:

```text
                ALERT
                  │
                  ▼
              VERIFY
                  │
        Is the artifact correct?
                  │
                  ▼
              ENRICH
                  │
       ┌──────────┼──────────┐
       ▼          ▼          ▼
    File/Hash    IP       Domain
       │          │          │
       ▼          ▼          ▼
 VirusTotal    Shodan      WHOIS
 MalwareBazaar Censys       DNS
 Sandbox       IP2Proxy     VT
       │          │          │
       └──────────┼──────────┘
                  ▼
              CORRELATE
                  │
          Compare evidence
                  │
                  ▼
               DECIDE
                  │
       ┌──────────┼──────────┐
       ▼          ▼          ▼
     Benign    Suspicious  Malicious
```

The important part is **correlation**.

Never make a decision based only on:

```text
VirusTotal says malicious
```

or:

```text
AbuseIPDB score is high
```

Instead, build the conclusion from several pieces of evidence.

---

# Quick Reference

| Artifact | Start With | Then Pivot To |
|---|---|---|
| File | Hash + filename/path | VirusTotal → MalwareBazaar → Sandbox |
| Hash | VirusTotal | MalwareBazaar → Sandbox |
| IP | VirusTotal | Shodan/Censys → proxy/reputation checks |
| Domain | WHOIS + DNS | VirusTotal → IP → Shodan/Censys |
| Unknown malware | Static indicators | Sandbox → network IOCs → ATT&CK |

---

# Key Lessons

- Threat intelligence is context, not just a list of IOCs.
- One source should not determine the final verdict.
- File names and paths provide useful initial clues but are not proof of maliciousness.
- Generate hashes early during file investigations.
- Dynamic analysis can reveal behavior that static indicators cannot.
- IP and domain enrichment should combine reputation, infrastructure, DNS, registration, and service information.
- VPN or proxy usage is a signal, not proof of malicious activity.
- Pivoting between indicators can reveal additional infrastructure.
- Map behavior to MITRE ATT&CK when possible.
- Always preserve the context of where an IOC came from.
