# Meta2

> **At a glance**
>
> **What it is:** The Linux practice lab for discovering services, following clues and crossing verified permission boundaries.
> **Aim:** Choose the next action from the evidence rather than running a memorised tool list.

**Parent:** [G5Labs](../README.md) · **Evidence record:** [enumeration method](../../methodology/enumeration-methodology.md)

> **About this release:** These are my own home-lab lesson designs and working notes. VM images and build scripts are not included yet. Addresses, snapshot names, accounts and flags are lab examples; adapt them to your own isolated setup. This is not a set of answers to someone else’s training rooms.

## Choose the Exercise

|Version|Focus|Workbook|Answers|Snapshot|
|---|---|---|---|---|
|v1|HTTP, FTP, SMB, NFS, MySQL and a chained clue|[Meta2 v1 - Workbook](v1-workbook.md)|[Meta2 v1 — Walkthrough](v1-walkthrough.md)|`Meta2-Enumeration-v1`|
|v2|Secrets, hashes, authentication, crypto, SSH keys and privilege boundaries|[Meta2 v2 - Workbook](v2-workbook.md)|[Meta2 v2 — Walkthrough](v2-walkthrough.md)|`Meta2_Enumeration_v2`|

> **Start with the Workbook**
>
> Think of a service as a room and a discovered file as a clue to another room. Record the trail before opening the answer key; the flags are receipts for the investigation.

## Environment and Latest Check

|Item|Recorded state — 2026-10-07|
|---|---|
|Hypervisor / target VM|libvirt/QEMU · `Metasploitable2` running|
|Target / attacker|Meta2 `192.168.56.189` · Kali `192.168.56.204`|
|Network|`CyberLab`, bridge `virbr1`, `192.168.56.0/24`; no forwarding configured|
|Kali connectivity|CyberLab interface plus a separate default/NAT interface|
|Reachable from host|TCP 21, 22, 80, 139, 445, 2049, 3306|
|Guest access|Anonymous FTP login and `pub/backup_notes.txt` listing work; guest SMB `tmp` access works but v1 clue files were absent|
|HTTP evidence|`robots.txt` points to `/internal_dev_notes/`; the backup config matches the v2 lesson|
|Restore points|Both exercise snapshots listed above exist|
|Still to validate|Full flag chain, account transitions, crypto parameters and clean reset/repeat|

This was a bounded host check, not a full assessment from Kali. Existing v1 content remains on the running target, but the web clue is v2: restore the version you intend to practise. No snapshot was restored during this review.

## Run, Record, Reset

1. Confirm the intended snapshot and both addresses.
2. Map services, then investigate the evidence in the workbook.
3. Record the command, relevant output, interpretation, identity, flag and defensive fix.
4. Close sessions and unmount any NFS mount.
5. Restore the chosen snapshot through the VM manager when ready to repeat; this discards changes since that snapshot.

The underlying references belong in [Nmap method](../../reconnaissance/nmap-methodology.md), SMB, FTP, Hashing, SSH and Privilege Escalation. The lab pages explain their use in this particular exercise.

## Future Improvements — As Skills Grow

These are options to revisit, not current commitments.

|When comfortable with…|Possible next improvement|What it would teach|
|---|---|---|
|Evidence-led enumeration|A smaller-hint repeat with varied filenames and paths|Reasoning without memorising the answer key|
|Python and service validation|Use recon-helper alongside Nmap and compare evidence|Port hints versus confirmed protocols; tool limitations|
|Linux permissions and sudo|Test every intended route and obvious shortcut from each account|Why a boundary works, and where the lab accidentally bypasses it|
|Cryptography basics|Document vault creation options and compare weak/strong passphrases|Cipher strength versus key derivation and password quality|
|Packet capture and logs|Capture one FTP, SSH and web session; identify observable evidence|Plaintext versus protected traffic and detection opportunities|
|Automation and testing|Versioned build/check scripts with exact users, flags and snapshots|A repeatable lab that can be rebuilt and validated|
|Remediation|Fix one exposure, then repeat the same test|Proof that a defensive change closes the route|

> **Completion Standard**
>
> Explain where each clue came from, why the next action made sense and what would stop the exposure. A later repeat can test independence; the current workbook provides the structure.
