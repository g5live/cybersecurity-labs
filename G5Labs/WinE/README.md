# WinE

> **At a glance**
>
> **What it is:** The Windows home lab for practising shares, credentials, remote access, permissions and privilege evidence.
> **Status:** Built and reachable from Kali according to my Kali connectivity test; the full seven-objective chain still needs validation.

**Parent:** [G5Labs](../README.md) · **System:** Windows · **Linux counterpart:** [Meta2](../Meta2/README.md)

> **About this release:** These are my own home-lab lesson designs and working notes. VM images and build scripts are not included yet. Addresses, snapshot names, accounts and flags are lab examples; adapt them to your own isolated setup. This is not a set of answers to someone else’s training rooms.

## Start Here

|Exercise|Lessons|Answers|Baseline|
|---|---|---|---|
|v1 — Windows investigation|[WinE v1 - Workbook](v1-workbook.md)|[WinE v1 — Walkthrough](v1-walkthrough.md)|`WinE_v1_Ready`|

> **Same Questions, Different Locks**
>
> SMB is the shared filing cabinet, WinRM is a remote workbench and ACLs decide who can use each key. Translate the Linux reasoning into Windows behaviour rather than copying the commands.

## Environment and Latest Check

|Item|Recorded state — 2026-10-07|
|---|---|
|Hypervisor / target VM|libvirt/QEMU · `Win11E` running; Windows 11 Enterprise lab|
|Target / attacker|WinE `192.168.56.217` · Kali `192.168.56.204`|
|Network|`CyberLab`, bridge `virbr1`, `192.168.56.0/24`; no forwarding configured|
|Host service check|TCP 135/RPC and 445/SMB open; 5985/WinRM timed out|
|Supplied account check|`svc_backup` authenticated to SMB; `PublicBackup` listed and both Flag 1/2 files downloaded/read successfully|
|User check|I can reach WinE through Kali; this does not yet verify every objective|
|Restore point|`WinE_v1_Ready` exists|
|Still to validate|WinRM from Kali, account access through WinRM, staged hash/account match, service trigger and final privileged identity|

Kali also has a separate default/NAT interface. The target has only its CyberLab interface; the network has no forwarding configured. These checks did not change guest services, files, firewall rules or snapshots.

## Objective Map

|Objective|What to prove|
|---|---|
|1 — Shares|Which shares the supplied `svc_backup` identity can read|
|2 — Secrets|What the downloaded config reveals and whether it grants access|
|3 — Hashes|Source, format and recovered value of the recorded NT hash|
|4 — Remote shell|WinRM login plus the actual user/groups|
|5 — Permissions|File/folder ACLs and the service that uses the writable path|
|6 — Service boundary|A privileged trigger executing the script under a different identity|
|7 — Privilege proof|Actual administrator or SYSTEM control, supported by token and action evidence|

> **Resolve Before Calling the Chain Complete**
>
> The current draft paired the `password` NT hash with `Dragon123!`, ran the maintenance script directly without proving the service identity, and supplied `secops` credentials without a recovery route. Reading an Administrator-named file is file-access evidence; it does not by itself prove administrator or SYSTEM execution.

See [WinE v1 — Walkthrough](v1-walkthrough.md#troubleshoot-winrm-from-kali) for the small connectivity check and read-only console diagnostics.

## Starting Account

For my v1 setup, the supplied SMB account is `svc_backup`, with the synthetic password `BackupPass2024!`. This is given to start the exercise; it is not a credential discovered through enumeration. Use a dedicated lab account if rebuilding the scenario.

## Run, Record, Reset

1. Confirm the baseline and addresses; use the supplied starting credential above.
2. Work through [WinE v1 - Workbook](v1-workbook.md) and record output before looking at answers.
3. After each account change, record identity, groups, relevant permissions and the action proving access.
4. Keep a note of modified files or triggers; restore `WinE_v1_Ready` through the VM manager before a fresh repeat. Restoring discards subsequent changes.

Use SMB, IAM, Hashcat and Windows Privilege Escalation for the underlying concepts. Keep the version-specific evidence here.

## Future Improvements — As Skills Grow

These are options to revisit, not current commitments.

|When comfortable with…|Possible next improvement|What it would teach|
|---|---|---|
|Windows identity and ACLs|Validate and document all seven objectives and unintended shortcuts|The difference between file access, user changes and elevated execution|
|PowerShell and services|Create a reproducible build/check script with a real service or task wrapper|Correct triggers, service identity and clean resets|
|Authentication|Separate supplied, discovered and recovered credentials in future exercises|An evidence trail without unexplained passwords|
|Windows logging|Review events for SMB reads, WinRM sessions and service/task execution|How an investigation looks from the defender's side|
|Remediation|Fix one ACL, leaked secret or remote-access rule and retest|Evidence that a change prevents the same access|
|Independent enumeration|Create a less guided repeat with fresh paths and synthetic credentials|Choosing tools and interpreting results without answer recall|
|Active Directory fundamentals|Add a separate domain-based version after the standalone route is stable|Local versus domain identity, groups and delegated rights|

> **Keep the Next Step Small**
>
> First make the current standalone chain reproducible and explainable. Richer scenarios can follow as Windows knowledge grows.
