# G5Labs — Build · Understand · Apply

My own Windows and Linux lab exercises, designed around one question: **what does this finding tell me to check next?**

These lessons bring together the networking, security and Python skills I’m developing. They include guided workbooks, clearly marked walkthroughs and defensive lessons. A flag is a receipt for the investigation; explaining how you found it is the useful part.

## Choose a Lab

|Lab|What it teaches|Start here|
|---|---|---|
|Meta2 v1|Discover services, inspect files and follow a clue between protocols|[Workbook](Meta2/v1-workbook.md) · [Walkthrough — answers](Meta2/v1-walkthrough.md)|
|Meta2 v2|Investigate secrets, hashes, authentication, cryptography and Linux permissions|[Workbook](Meta2/v2-workbook.md) · [Walkthrough — answers](Meta2/v2-walkthrough.md)|
|WinE v1|Investigate Windows shares, credentials, remote access and service permissions|[Workbook](WinE/v1-workbook.md) · [Walkthrough — answers](WinE/v1-walkthrough.md)|

Read the [Meta2 setup and status](Meta2/README.md) or [WinE setup and status](WinE/README.md) first. Attempt the workbook before opening the walkthrough.

## What Is Ready?

These are **labs in development**, not a downloadable VM package. VM images and repeatable build scripts are not included. The documents describe my own isolated setup; the example addresses, credentials and flags are synthetic lab material.

Checks on **7 October 2026** confirmed:

- Both target VMs were running on the isolated CyberLab network.
- WinE’s starting account could read the two initial SMB lesson files, including Flags 1 and 2.
- Meta2’s documented TCP service ports responded; its public web clues matched v2.
- Anonymous FTP and SMB worked on Meta2, but the current SMB share lacked the v1 clue files.

The full exercises still need end-to-end validation. WinRM timed out from the host, and the Windows hash/login match, service trigger and final privileged identity need checking. Meta2 v1 needs its intended snapshot. Each walkthrough identifies what was observed and what remains an intended route.

## How to Use the Lessons

1. Confirm your target, network and starting snapshot.
2. Record the question before choosing the command.
3. Keep the useful output and explain what it supports.
4. After an account change, prove the actual identity and permissions.
5. Explain a defensive fix, then reset before repeating.

Work only on systems you own or have permission to test. These are my own exercises; third-party training answers and personal credentials do not belong here.

## As My Skills Grow

The lab READMEs include optional next ideas: less guided repeats, packet and log analysis, remediation checks and repeatable builds. They are directions to revisit, not commitments or claims of completed work.

For reusable methods, see the [enumeration notes](../methodology/enumeration-methodology.md) and [Nmap notes](../reconnaissance/nmap-methodology.md).
