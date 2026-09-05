# Linux Process Analysis

## Overview

Process analysis helps explain what is running, which account launched it, how it relates to other processes and which files or network resources it uses. A command name alone is weak evidence; lineage, executable path, arguments, open resources, start time and persistence mechanism provide the context needed for a defensible assessment.

## Core Process Fields

| Field | Meaning |
|---|---|
| PID | Unique process identifier within the current PID namespace. |
| PPID | Identifier of the parent process. |
| USER / UID | Account or numeric identity under which the process runs. |
| TTY | Controlling terminal, if one is associated. `?` often indicates none. |
| TIME | Cumulative CPU time, not elapsed wall-clock runtime. |
| CMD / COMMAND | Command name or command line, depending on the selected output format. |

## Building a Process View

`ps` is a snapshot tool. Useful Procps forms include:

```bash
ps -ef
ps -eF --forest
ps -fp PID
ps --ppid PID -f
ps -u USER -f
```

- `ps -ef` lists all processes in full-format output.
- `ps -eF --forest` adds extra detail and displays parent-child relationships as a tree.
- `ps -fp PID` selects a specific PID.
- `ps --ppid PID -f` selects the direct children of a parent PID.
- `ps -u USER -f` selects processes by effective user.

The `-s` option selects session IDs; it is not the option for selecting a parent process. Because processes can exit quickly and command-line arguments can be altered or truncated, a single snapshot is not a complete history.

## Following Process Lineage

```bash
pstree -p
pstree -p -s PID
```

`pstree -p -s PID` shows the ancestors of the selected process and includes PIDs. Unexpected parentage can be significant—for example, a service account's process descending from an unusual shell—but must be compared with normal application behaviour.

## Inspecting Open Resources

```bash
sudo lsof -p PID
sudo lsof -Pan -p PID -i
readlink -e /proc/PID/exe
tr '\0' ' ' < /proc/PID/cmdline
```

`lsof -p PID` lists resources opened by the process, including files, libraries, pipes and sockets. The network-focused form avoids hostname and service-name resolution. `/proc` can reveal the executable target and raw argument list, subject to permissions and races if the process exits.

Analytical questions include:

- Does the executable path match the expected package or service?
- Is the binary running from a temporary or user-writable directory?
- Are its parent, user and command-line arguments expected?
- Which files, libraries and network endpoints are open?
- Has the executable been deleted while the process remains running?

## Examining a Suspicious Script Safely

Avoid executing an unknown file simply to learn what it does. Record its metadata and hash, identify its type and review content with a non-executing viewer:

```bash
stat SCRIPT
sha256sum SCRIPT
file SCRIPT
less SCRIPT
```

Shell scripts should be read as evidence. Look for unexpected network listeners or connections, command interpreters, named pipes, credential access, destructive operations and attempts to maintain execution. Individual fragments may also appear in legitimate administration or testing, so intent depends on provenance and context.

## Observing Short-Lived Processes

Periodic snapshots can miss rapidly executed commands. Tools such as `pspy` monitor process events through `/proc` and can often operate without root privileges:

```bash
./pspy64
```

Root access may expose more information, but running third-party tooling on a live host changes system state and the binary must be obtained and validated through an approved source. `pspy` output is observation data; it does not classify a process as malicious.

For longer-term monitoring, appropriately configured audit or endpoint telemetry is generally stronger than starting an ad hoc observer after an incident begins.

## Services and Persistence

Systemd services can be inspected without changing their state:

```bash
systemctl status SERVICE
systemctl cat SERVICE
systemctl show SERVICE
systemctl list-unit-files --state=enabled
journalctl -u SERVICE
```

Starting, stopping, restarting, enabling or disabling a service changes the host and should not be an initial evidence-gathering action.

Common persistence locations include:

- `/etc/systemd/system/` and distribution-managed unit directories
- `/etc/init.d/` and `/etc/rc.d/` on systems that use those mechanisms
- `/etc/crontab`, `/etc/cron.d/` and `/etc/cron.{hourly,daily,weekly,monthly}/`
- Per-user crontabs, commonly under `/var/spool/cron/` or `/var/spool/cron/crontabs/`
- Desktop autostart entries such as `~/.config/autostart/`
- Shell startup files and other application-specific launch mechanisms

Locations vary by distribution. Prefer read-only inspection and compare entries with package records, configuration management and an established baseline.

## Analytical Workflow

```text
Capture a broad process snapshot
        ↓
Identify unusual users, paths or arguments
        ↓
Trace parents and children
        ↓
Inspect executable and open resources
        ↓
Check service and persistence configuration
        ↓
Correlate with filesystem and log evidence
        ↓
Document confidence and alternative explanations
```

## Key Lessons

- Process names are easy to imitate; examine paths, lineage and resources.
- `TIME` in standard `ps` output is CPU time, not process age.
- `--ppid` selects children; `-s` selects sessions.
- Short-lived processes require historical or real-time telemetry.
- Initial collection should avoid actions that unnecessarily change process or service state.
