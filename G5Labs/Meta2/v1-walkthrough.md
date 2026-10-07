# Meta2 v1 — Walkthrough

> **At a glance**
>
> **Goal:** Show how each discovery leads to the next question across six services and clues.
> **Workbook:** [Meta2 v1 - Workbook](v1-workbook.md) · **Project:** [Meta2](README.md) · **Domain:** [G5Labs](../README.md)

|Target|Attacker|Baseline|
|---|---|---|
|Meta2 `192.168.56.189`|Kali `192.168.56.204`|`Meta2-Enumeration-v1`|

> **Read After Your Attempt**
>
> Think of each flag as a receipt: keep the command, relevant output, interpretation and defensive fix that explain how you reached it.

## Start — Build the Map

```bash
nmap -sV 192.168.56.189
nmap -p- 192.168.56.189
nmap -sC -sV -p 21,22,23,25,53,80,139,445,2049,3306,5432 192.168.56.189
```

Use the full scan to choose the ports for follow-up. The final line is this lab's example set, not proof that every port is open. Separate port hints from protocol/version evidence. See [Nmap method](../../reconnaissance/nmap-methodology.md).

## Flag 1 — HTTP Enumeration

**Clue:** HTTP is exposed. Inspect the site and crawler directives:

```bash
curl -s http://192.168.56.189/robots.txt
curl -s http://192.168.56.189/maintenance/
curl -s http://192.168.56.189/maintenance/flag.txt
```

**Expected:** `FLAG{meta2-http-enumeration-01}`. `robots.txt` is a roadmap, not access control. On the running v2 state it points to `/internal_dev_notes/`; use the v1 snapshot for the original exercise.

## Flag 2 — FTP

**Clue:** FTP may expose files without a normal account.

```text
ftp 192.168.56.189
Name: anonymous
Password: lab@example.invalid
ftp> ls
ftp> cd pub
ftp> ls
ftp> get backup_notes.txt
ftp> bye
```

Read the downloaded file on Kali with `cat backup_notes.txt`.

**Expected:** `FLAG{meta2-ftp-enumeration-02}`. Record which identity can read which file; anonymous access is only a weakness when the exposed content or permissions make it one.

## Flag 3 — SMB

**Clue:** SMB exposes shares; listing a share and reading its contents are separate tests.

```bash
smbclient -L //192.168.56.189 -N
smbclient //192.168.56.189/tmp -N
```

Inside `smbclient`:

```text
ls
get ops-note.txt
exit
```

Read `ops-note.txt` on Kali. **Expected:** `FLAG{meta2-smb-enumeration-03}`.

<details>
<summary>Old Samba Compatibility</summary>

If negotiation fails because this legacy lab requires SMB1, retry only this command with `--option='client min protocol=NT1' -m NT1`. Keep the change local to the lab command; do not weaken your global client configuration. See [smbclient documentation](https://www.samba.org/samba/docs/current/man-html/smbclient.1.html).

</details>

## Flag 4 — NFS

**Clue:** An export can expose the filesystem from another angle.

```bash
showmount -e 192.168.56.189
sudo mkdir -p /mnt/meta2
```

If the advertised export is `/`, mount it read-only:

```bash
sudo mount -t nfs -o ro 192.168.56.189:/ /mnt/meta2
ls -la /mnt/meta2
cat /mnt/meta2/var/backups/system-audit.txt
sudo umount /mnt/meta2
```

Use the export actually listed by `showmount`; the command is conditional, not a guessed path. **Expected:** `FLAG{meta2-nfs-enumeration-04}`. Explain the export policy and file permissions that permit the read.

## Flag 5 — MySQL

**Clue:** The database listener is an enumeration route. Connect with the access configured in your own lab setup:

```bash
mysql -h 192.168.56.189 -u <lab-user> -p
```

Replace `<lab-user>` before running. Within MySQL:

```sql
SHOW DATABASES;
USE ops_archive;
SHOW TABLES;
SELECT * FROM notes;
```

**Expected:** `FLAG{meta2-mysql-enumeration-05}`. Choose the database/table from observed names; record the account's permissions and why the information should be restricted.

## Flag 6 — Chained Enumeration

**Clue:** Revisit the SMB `tmp` share and retrieve `handover.txt`. It points back to HTTP:

```bash
smbclient //192.168.56.189/tmp -N -c 'get handover.txt'
cat handover.txt
curl -s http://192.168.56.189/diagnostics-old/system-check.txt
```

**Expected:** `FLAG{meta2-chained-enumeration-06}`. The chain is **SMB → note → web path → HTTP content**. A discovery can matter later even if it does not immediately contain a flag.

## Explain and Reset

For each result, describe the exposed service, access conditions, information found and defensive fix. Unmount NFS, close sessions and restore the intended snapshot before repeating.

> **What Was Checked**
>
> On 2026-10-07 the host reached FTP, SSH, HTTP, SMB, NFS and MySQL. The maintenance directory and chained HTTP flag still exist, but the running web directives match v2. Anonymous FTP works and `backup_notes.txt` is listed under `pub`. Guest SMB access works, but `ops-note.txt` and `handover.txt` were absent from the current `tmp` share. NFS reads and database queries were not tested; use the v1 snapshot to validate this walkthrough end to end.
