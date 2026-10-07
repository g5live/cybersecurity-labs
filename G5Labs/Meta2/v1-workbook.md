# Meta2 v1 - Workbook

> **At a glance**
>
> **Goal:** Map an unfamiliar host, investigate the services and follow the evidence across them.
> **Route:** [Meta2](README.md) · [G5Labs](../README.md) · [enumeration method](../../methodology/enumeration-methodology.md)

|Target|Attacker|Baseline|Objectives|
|---|---|---|---|
|Meta2 `192.168.56.189`|Kali `192.168.56.204`|`Meta2-Enumeration-v1`|Six flags|

> **Think Like an Investigator**
>
> Build the map before following the first shiny clue. For each command, ask: **what question am I trying to answer?** Keep the walkthrough closed while gathering your own evidence.

## Start — Map the Services

- Date and snapshot used: ____________________
- Target and attacker addresses confirmed: ____________________
- Scan command and TCP ports discovered: ____________________
- Service identification and supporting evidence: ____________________
- First three services to investigate, with reasons: ____________________

For every flag, record the command/output, account used, interpretation and defensive fix. Permission checks should describe what you actually tested; do not assume write access from read access.

## Flag 1 — HTTP Enumeration

What exists beyond the front page? `robots.txt`, backups and old directories can reveal useful paths. A path containing a flag may be exposed content rather than a software exploit. See HTTP.

#### Your Investigation

- Server/version and supporting evidence: ____________________
- Discovered paths and why one stands out: ____________________
- Discovery file and what it revealed: ____________________
- What you could read; exposure or exploit; defensive fix: ____________________
- Flag value: `FLAG{________________________________}`
- Command/output saved at: ____________________
- Defensive fix: ____________________

## Flag 2 — FTP Enumeration

Treat FTP as a file-transfer question first: who can log in, and what can that account read? An ordinary text file may be the useful clue. See FTP.

#### Your Investigation

- Connection and authentication methods tested: ____________________
- Anonymous login result: ____________________
- Files listed or downloaded and why they matter: ____________________
- Read/write permissions actually tested; clues to other services: ____________________
- Flag value: `FLAG{________________________________}`
- Command/output saved at: ____________________
- Defensive fix: ____________________

## Flag 3 — SMB Enumeration

List shares, test access, then inspect interesting files. A share name is a clue; permissions and contents decide its value. See SMB.

#### Your Investigation

- Shares discovered: ____________________
- Guest/no-password access result: ____________________
- Files retrieved and evidence of permissions: ____________________
- What the exposure reveals and its likely cause: ____________________
- Flag value: `FLAG{________________________________}`
- Command/output saved at: ____________________
- Defensive fix: ____________________

## Flag 4 — NFS Enumeration

NFS turns a remote directory into a mounted filesystem. Check the advertised export and access policy before mounting it read-only.

#### Your Investigation

- Exports and permitted clients: ____________________
- Exact export mounted and command used: ____________________
- Interesting directories/files and why: ____________________
- What was exposed without normal local authentication: ____________________
- Flag value: `FLAG{________________________________}`
- Command/output saved at: ____________________
- Defensive fix: ____________________

## Flag 5 — Database Enumeration

Ask which databases and tables your account can see. Narrow the search using names and context rather than dumping everything.

#### Your Investigation

- Service and authentication evidence: ____________________
- Account and permissions: ____________________
- Relevant database/table and selection reason: ____________________
- Information retrieved; weakness and defensive fix: ____________________
- Flag value: `FLAG{________________________________}`
- Command/output saved at: ____________________
- Defensive fix: ____________________

## Flag 6 — Chained Enumeration

A finding on one service can point to another: shared note → web path → useful content. Revisit the unresolved clues rather than hunting for a flag number.

#### Your Investigation

- Original clue and its source: ____________________
- Service or path referenced: ____________________
- Next action and what it revealed: ____________________
- Complete chain and why each step followed from the last: ____________________
- Flag value: `FLAG{________________________________}`
- Command/output saved at: ____________________
- Defensive fix: ____________________

## Debrief

- Which discovery was most useful, and why? ____________________
- What looked useful but turned out to be a dead end? ____________________
- Which tool did you choose to answer which question? ____________________
- What would you change on the next attempt? ____________________
- Without commands, explain your priorities for FTP, SSH, HTTP, SMB, NFS and MySQL: ____________________

> **Completion Check**
>
> Explain how you discovered each flag, why you chose each next step and what would prevent the exposure. Finding all six is useful; explaining the trail is the aim.

## Walkthrough

<details>
<summary>Answers and Troubleshooting</summary>

Compare your evidence with [Meta2 v1 — Walkthrough](v1-walkthrough.md) after attempting the exercise.

</details>
