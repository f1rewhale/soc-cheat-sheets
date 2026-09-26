# Splunk Basic Queries

A quick reference for common Splunk Search Processing Language (SPL) queries used during SOC investigations.

## Basic Search

Search a specific index:

```spl
index=winenv
```

Search for a Windows Event ID:

```spl
index=winenv EventCode=4624
```

Search for a specific host:

```spl
index=winenv ComputerName=WINHOST05
```

## Search for Multiple Event IDs

```spl
index=winenv EventCode=4720 OR EventCode=4722
```

Useful when several related Windows events need to be investigated together.

## Wildcards

```spl
index=task4 Image="*SharePoint.exe"
```

`*` matches any sequence of characters.

## Display Selected Fields

```spl
index=winenv EventCode=3
| table _time ComputerName Image SourceIp DestinationIp DestinationPort
```

`table` displays only the fields that are useful for the investigation.

## Sort Events by Time

Oldest to newest:

```spl
| sort + _time
```

Newest to oldest:

```spl
| sort - _time
```

## Filter Search Results

```spl
| search "Accepted password" OR "Failed password"
```

This can be useful when narrowing results after the initial search.

## Count Events

```spl
| stats count by username
```

Useful for identifying how many events are associated with each user.

## Group Events into Time Windows

```spl
| bin _time span=5m
| stats count by clientip _time
```

Useful for identifying bursts of activity such as brute-force attempts.

## Filter by Count

```spl
| where count > 25
```

This can be combined with `stats` to focus on unusually frequent activity.

## Extract Fields with Regex

```spl
| rex field=_raw "..."
```

`rex` can extract information from raw log data into fields that can then be searched or grouped.

## Exclude a Value

```spl
useragent!="Mozilla/5.0 (Hydra)"
```

Useful for removing known activity from the current search and investigating what happened around it.

---

# SOC Investigation Examples

## Suspicious PowerShell

```spl
index=winenv EventCode=1 *powershell* AND *EncodedCommand*
| table _time ComputerName ParentUser ParentImage ParentCommandLine Image CommandLine
```

Look for:

- Encoded PowerShell commands
- Suspicious parent processes
- Unusual command lines
- User and host involved

## Network Connections

```spl
index=winenv EventCode=3 ComputerName=WINHOST05
| table _time ComputerName Image SourceIp SourcePort DestinationIp DestinationPort Protocol
```

Useful for investigating network connections associated with a host or process.

## Windows Logons

```spl
index="win-alert" EventCode=4624
| table _time Account_Name Logon_Type Workstation_Name Source_Network_Address
```

Useful for investigating successful Windows logons.

## Scheduled Task Creation

```spl
index="win-alert" EventCode=4698
| table _time EventCode user_name host Task_Name Message
```

Event ID `4698` indicates that a scheduled task was created.

## SSH Authentication

```spl
index=linux source="auth.log" process=sshd
| search "Accepted password" OR "Failed password"
```

Useful for reviewing successful and failed SSH authentication attempts.

## Web Brute Force

```spl
index=* method=POST uri_path="/wp-login.php"
| bin _time span=5m
| stats count by clientip _time
| where count > 25
```

This groups POST requests into five-minute windows and can help identify repeated login attempts from the same IP address.

---

## Quick Reference

| SPL | Purpose |
|---|---|
| `index=` | Select an index |
| `field=value` | Filter by field |
| `OR` | Match either condition |
| `AND` | Require both conditions |
| `*` | Wildcard |
| `table` | Display selected fields |
| `search` | Filter results |
| `sort` | Sort events |
| `stats` | Aggregate data |
| `bin` | Group data into time intervals |
| `where` | Filter calculated results |
| `rex` | Extract fields using regex |

> Field names and index names depend on the environment and data source.
