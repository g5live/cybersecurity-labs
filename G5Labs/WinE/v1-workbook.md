# WinE v1 - Workbook

> **At a glance**
>
> **Goal:** Follow Windows services, credentials and permissions from a first share to proven privilege boundaries.
> **Route:** [WinE](README.md) · [G5Labs](../README.md)

|Target|Attacker|Baseline|
|---|---|---|
|WinE `192.168.56.217`|Kali `192.168.56.204`|`WinE_v1_Ready`|

> **Before You Start**
>
> Confirm the addresses and snapshot. For Flag 1, `svc_backup` is the supplied starting identity; use the supplied synthetic password in the lab README. Keep the walkthrough closed while recording evidence.

## Flag 1 — Windows Service Reconnaissance & File Shares

#### The Lesson

SMB is the shared filing cabinet; RPC helps Windows services communicate. List the shares, identify the account used, and check what that identity can read. Authenticated access is not anonymous access. See SMB.

#### Your Investigation

- What transport protocol and port are primarily responsible for Windows file sharing? ____________________
- What tool and arguments did you use to list the available shares on the target? ____________________
- What custom non-administrative share was exposed? ____________________
- Which service account credentials granted authenticated read access to this share? ____________________
- **Flag 1 Value:** `FLAG{________________________________}`

## Flag 2 — Secret Storage & Configuration File Exposure

#### The Lesson

A backup configuration can become a spare-key cupboard. Record the file and the exposed account, then verify what access the credential grants. Finding a password is not yet proof that it works.

#### Your Investigation

- What specific configuration file did you download from the target share? ____________________
- Is there an exposed file containing readable credentials? (Y/N) _____
- What service account credentials or database secrets were exposed inside the file? ____________________
- What remote administrative protocol can be accessed using these discovered credentials? ____________________
- **Flag 2 Value:** `FLAG{________________________________}`

## Flag 3 — Hash Identification & Offline Recovery

#### The Lesson

The Windows NT password hash is MD4 of the UTF-16LE password. Its 32-character hex appearance alone does not identify it: use the record format and source. Hashcat mode 1000 or John format NT fits a confirmed NT hash. See Hashcat.

#### Your Investigation

- In which user profile directory were legacy audit notes discovered? ____________________
- Which account name was paired with the exposed NTLM hash string? ____________________
- What is the 32-character hexadecimal NTLM hash string? ____________________
- What Hashcat mode (`-m`) is specifically used to crack Windows NTLM hashes? ____________________
- What plaintext password was recovered from the hash? ____________________
- **Flag 3 Value:** `FLAG{________________________________}`

## Flag 4 — Remote Management Foothold & Operator Identity

#### The Lesson

WinRM provides a remote PowerShell route, usually on 5985 (HTTP) or 5986 (HTTPS). A listening port, valid credentials and endpoint permission are separate checks. Confirm identity and groups after login.

#### Your Investigation

- Which service port allowed an interactive remote PowerShell session from Kali? ____________________
- What command-line utility did you use from Kali to establish the interactive remote shell? ____________________
- What command did you run to confirm your current user identity on Windows? ____________________
- Where on the target filesystem was Flag 4 located? ____________________
- **Flag 4 Value:** `FLAG{________________________________}`

## Flag 5 — Access Control List (ACL) Enumeration & Weak Permissions

#### The Lesson

`icacls` and `Get-Acl` show who can read, write or modify a path. Check the script itself as well as its folder, then find out which service or task actually uses it. See Windows Privilege Escalation.

#### Your Investigation

- What custom directory under `C:\Program Files\` contains loose permissions? ____________________
- What specific `icacls` permission mask indicates that standard users can modify files in that folder? ____________________
- What log file in that folder reveals maintenance routine metadata and the flag? ____________________
- **Flag 5 Value:** `FLAG{________________________________}`

## Flag 6 — Service Hijacking & Lateral Movement

#### The Lesson

A writable script only crosses a privilege boundary when a different, more privileged identity runs it. Launching it yourself keeps your identity. Establish the service account, trigger permissions and resulting identity before claiming a pivot.

#### Your Investigation

- What is the registered service name associated with the maintenance batch script? ____________________
- Which target user account (`StartName` / `obj`) does this service execute as? ____________________
- What file did you modify or replace to alter the service's execution behavior? ____________________
- How did you trigger execution of your replacement script? ____________________
- In which user's profile space was Flag 6 subsequently generated or discovered? ____________________
- **Flag 6 Value:** `FLAG{________________________________}`

## Flag 7 — Filtering Decoys & Administrative / SYSTEM Proof

#### The Lesson

Reading an Administrator-named folder does not prove administrator or SYSTEM execution. Record `whoami`, group membership, privilege state and a meaningful privileged action. Administrator and SYSTEM are distinct identities.

#### Your Investigation

- What decoy files or folders did you encounter during root-level enumeration? ____________________
- Why was the decoy file in `C:\Decoys\` invalid as a real escalation path? ____________________
- What command proves administrative or SYSTEM-level control on Windows? ____________________
- Where was the true final administrative flag located? ____________________
- **Flag 7 Value:** `FLAG{________________________________}`

## Lab Reflection & Capability Check

- **I recognise:** Did I recognize how Windows services (SMB/WinRM) differ from Linux network services during initial scans?

- **I know:** Do I understand how NTLM hashes function and why GPU dictionary attacks crack unsalted hashes rapidly?

- **I can choose:** How did I choose between SMB file transfers and WinRM remote execution?

- **I can interpret:** How did I interpret Windows Access Control Lists (`icacls`) to identify writable application paths?

- **I can adapt:** When guest authentication was restricted, how did I adapt my initial foothold using discovered service credentials?
## Walkthrough

<details>
<summary>Answers and Troubleshooting</summary>

Compare your findings with [WinE v1 — Walkthrough](v1-walkthrough.md) after attempting the objectives.

</details>
