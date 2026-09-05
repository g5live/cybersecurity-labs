# Linux Incident Triage

## Overview

Linux incident triage is the structured collection and interpretation of evidence needed to answer immediate questions: what happened, when it happened, which identities and systems were involved, whether activity is continuing and what should be preserved or contained next.

Triage is not the same as full forensic examination. On a live system, every command can change state. Collection should follow the incident-response plan, use trusted tools where possible and record times, commands, output locations and analyst actions.

## First Principles

1. Confirm authorisation and the scope of the investigation.
2. Record host identity, current time and time-zone context.
3. Preserve volatile evidence according to organisational procedure.
4. Start with read-only collection where practical.
5. Build a timeline and correlate independent sources.
6. Separate observed facts, working hypotheses and confirmed conclusions.

Containment may be urgent, but disconnecting a host, killing a process or stopping a service can destroy volatile evidence. That trade-off belongs in the incident-response decision process.

## Log Sources

Log availability depends on the distribution, services, retention configuration and whether the host uses the systemd journal, traditional text logs or both.

| Source | Typical purpose |
|---|---|
| `journalctl` | Queries the systemd journal by time, unit, boot, priority and other fields. |
| `/var/log/auth.log` | Authentication and authorisation events on many Debian-derived systems. |
| `/var/log/secure` | Similar security and authentication events on many Red Hat-derived systems. |
| `/var/log/messages` or `/var/log/syslog` | General system and service messages, depending on distribution. |
| `/var/log/kern.log` | Kernel messages where configured as a separate text log. |
| `/var/log/boot.log` | Boot-related output on systems that create it. |
| `utmp` | Binary data representing current sessions, commonly exposed through `who`. |
| `wtmp` | Binary historical login/logout records, commonly read with `last`. |
| `btmp` | Binary failed-login records, commonly read with `lastb` and appropriate privileges. |

`utmp`, `wtmp` and `btmp` are not ordinary text logs and should not be interpreted with `cat`. Their presence, paths and retention are system-dependent.

## Time-Bounded Journal Review

```bash
journalctl --since "2026-01-01 10:00:00" --until "2026-01-01 11:00:00"
journalctl -b
journalctl -b -1
journalctl -u SERVICE --since "1 hour ago"
journalctl -p warning..alert
```

Time filtering reduces noise, but a narrow window can omit preparation or follow-on activity. Confirm the system clock and time zone, widen the interval when needed, and preserve relevant output rather than relying only on terminal history.

## Authentication and Session Review

```bash
who
w
last -F
sudo lastb -F
```

These sources can reveal session timing, origin and failed authentication patterns. They are not complete proof of user activity: records may rotate, be disabled, be altered by an attacker or represent automated and expected access. Correlate them with SSH, privilege-escalation, application and network telemetry.

Useful questions include:

- Is the account expected to log in to this host?
- Is the source address recognised?
- Do failures precede a successful session?
- Was privilege elevation recorded soon afterwards?
- Does process or file activity align with the session window?

## Scheduled Tasks and Services

Persistence checks should cover the mechanisms actually used by the host:

```bash
systemctl list-unit-files --state=enabled
systemctl list-timers --all
systemctl cat SERVICE
cat /etc/crontab
ls -la /etc/cron.d /etc/cron.hourly /etc/cron.daily
sudo crontab -l -u USER
```

Per-user crontab storage differs across distributions, so commands such as `crontab -l -u USER` are often safer than assuming one spool path. Unexpected entries should be correlated with file ownership, package installation, change history and the intended role of the host.

## Application Artefacts

Application evidence can provide context that central system logs do not contain.

```bash
dpkg -l                    # Debian-derived systems
rpm -qa                    # RPM-based systems
find /home -type f -name '.viminfo' 2>/dev/null
```

Package listings help establish installed software but not necessarily when or why it ran. `.viminfo` may contain editor histories, marks and search terms; it is not a chronological record of shell commands and may contain sensitive data.

Browser artefact parsers such as Dumpzilla can extract information from supported Firefox profiles:

```bash
python3 dumpzilla.py PROFILE_DIRECTORY --Summary --Verbosity CRITICAL
python3 dumpzilla.py PROFILE_DIRECTORY --Cookies
```

Analyse a preserved copy where possible. Browser databases may be locked or changed while the application is running, and their contents require privacy-aware handling and timeline correlation.

## Rootkit Scanners

```bash
sudo chkrootkit
sudo rkhunter --check
```

`chkrootkit` and `rkhunter` perform signature, anomaly and configuration checks. They may produce false positives and cannot prove that a system is clean. Neither should be described as a reliable removal tool. On a compromised host, locally executed scanners and the utilities they depend on may themselves be untrustworthy; validate results against known-good offline or centrally collected evidence.

## Correlation and Interpretation

A single event rarely provides enough context. A stronger timeline might connect:

```text
Authentication event
        ↓
New process or service execution
        ↓
File creation or inode change
        ↓
Network activity
        ↓
Persistence change
```

The sequence is an investigation model, not an assumption that every incident follows it. Record the source of each timestamp and account for clock skew, rotation, retention gaps and possible tampering.

## Triage Record

For each finding, record:

- Time and time zone
- Host and data source
- Exact command or collection method
- Relevant output and preserved artefact location
- Hashes for collected files where appropriate
- Observation
- Interpretation and confidence
- Alternative explanations
- Recommended next action

## Key Lessons

- Log paths and contents vary between distributions and configurations.
- Login databases require purpose-built readers such as `who`, `last` and `lastb`.
- Application artefacts provide context but must not be overinterpreted.
- Rootkit scanners are supporting signals, not proof of compromise or cleanliness.
- A defensible conclusion comes from a preserved, correlated timeline rather than one suspicious line.
