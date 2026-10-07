# Meta2 v2 - Workbook

> **At a glance**
>
> **What it is:** Meta2 v2 extends the lab into credentials, hashes, authentication, cryptography, decoys and privilege boundaries.
> **Route:** [Meta2](README.md) · [G5Labs](../README.md)

---

**Scope Target:** `192.168.56.189` (Meta2)  
**Assigned Platform:** Kali Linux  
**Baseline:** `Meta2_Enumeration_v2` · **Attacker:** Kali `192.168.56.204`

> **Keep the Evidence**
>
> For each flag, record the command, result, account used, interpretation and defensive fix. A flag is the receipt; the reasoning is the lesson.

---

## Flag 1 — Web Reconnaissance & Unindexed Discovery

#### The Lesson

`robots.txt` tells crawlers which paths to avoid; it does not stop people opening those paths. Treat it as a clue, then check the response and content. See HTTP.

#### Your Investigation  
* What standard web file directs search engine crawlers on what not to index? ____________________
* What CLI utility did you use to pull the HTTP response headers/body directly? ____________________
* What non-standard directory stands out in the server response? ____________________
* What is the HTTP status code returned when querying that discovered path directly? ____________________

---

## Flag 2 — Secret Storage & Exposure in Configs

#### The Lesson

A backup config in the web root can reveal readable secrets. A hidden URL is not access control, and Unix file permissions alone do not prevent a web server serving a file it can read.

#### Your Investigation  
* What specific configuration file did you locate within the hidden path? ____________________
* Is there an exposed file containing readable text? (Y/N) _____
* What credentials or accounts were exposed inside the file? ____________________
* What capability did these recovered credentials grant you? ____________________
* **Flag 2 Value:** `FLAG{________________________________}`

---

## Flag 3 — Hash Identification & Offline Recovery

#### The Lesson

Encoding, hashing and encryption are different. Hash length narrows the possibilities; the source and format decide which cracking mode fits. A 32-character hex string alone does not prove MD5. See Hashing and John.

#### Your Investigation  
* In which user's home directory did you discover legacy system notes? ____________________
* What tool or methodology did you use to verify the hash format? ____________________
* What hash algorithm or crypt type was identified? ____________________
* What command-line syntax did you run to execute the offline dictionary attack? ____________________
* What cleartext value was recovered from the hash? ____________________
* **Flag 3 Value:** `FLAG{________________________________}`

---

## Flag 4 — Network Authentication Attacks & Privilege Boundaries

#### The Lesson

Online password tests contact the service; offline cracking does not. Choose a small, evidence-led lab list, record failures, then check `sudo -l` after login. See Hydra and Privilege Escalation.

#### Your Investigation  
* Which user account was targeted for the network authentication attack? ____________________
* Which service daemon did you target, and what tool conducted the attack? ____________________
* What password successfully authenticated against the target service? ____________________
* When running `sudo -l` as this operator, what command are you permitted to execute? ____________________
* As which target user can you run that privileged binary? ____________________
* **Flag 4 Value:** `FLAG{________________________________}`

---

## Flag 5 — Password Policy Analysis & Symmetric Cryptography

#### The Lesson

A predictable password policy shrinks the candidate list. AES can be strong while its passphrase is weak. Decryption must match the original cipher, digest and key-derivation settings; readable output is stronger evidence than an exit code.

#### Your Investigation  
* Where on the filesystem was the encrypted archive discovered? ____________________
* According to documentation or policy notes, what pattern must the passphrase satisfy? ____________________
* What command or technique did you use to generate your targeted candidate list? ____________________
* Was an encryption or cipher algorithm specified? (Y/N) _____ If yes, which one? ____________________
* How did you automate testing the candidate keys against the encrypted file? ____________________
* What was the recovered key/passphrase? ____________________
* **Flag 5 Value:** `FLAG{________________________________}`

---

## Flag 6 — Private Key Discovery & Privilege Escalation

#### The Lesson

A copied private key can become an authentication route if the matching public key is trusted for that account. Check ownership, permissions and identity; do not infer the owner from the filename alone. See SSH.

#### Your Investigation  
* What file path contained the misplaced private key file? ____________________
* Which user identity does the key belong to? ____________________
* What local file permissions were present on the discovered key file? ____________________
* What error does the SSH client output if you attempt to use the key without altering its local permissions? ____________________
* What flag or parameter specifies an identity file in OpenSSH? ____________________
* **Flag 6 Value:** `FLAG{________________________________}`

---

## Flag 7 — Dissecting Decoys & Root Escalation

#### The Lesson

A tempting file is only useful if you can affect the privileged execution path. Check the file, parent directory, called programs and actual sudo rights before calling a route viable or a decoy.

#### Your Investigation  
* What decoy files or artifacts did you encounter during root-level enumeration? ____________________
* Why was the decoy backup script unusable for privilege escalation? ____________________
* What sudo capability or binary execution permission grants a direct administrative path? ____________________
* What breakout syntax or parameter was passed to achieve root execution? ____________________
* **Flag 7 Value:** `FLAG{________________________________}`

---

## Lab Reflection & Capability Check

* **I recognise:** Did I immediately spot the difference between readable assets, crackable hashes, and non-exploitable decoys?
* **I know:** Do I understand why world-readable configurations and loose sudo delegations violate basic security boundaries?
* **I can choose:** How did I select between Hydra, offline cracking tools, and manual GTFOBin escapes?
* **I can interpret:** How did analyzing the stated password policy save time over running a generic brute-force dictionary?
* **I can adapt:** When network ciphers or command parameters failed, how did I alter my commands to gain shell access?

---

## Walkthrough

> **Spoilers**
>
> Complete and record the workbook before opening [Meta2 v2 — Walkthrough](v2-walkthrough.md).
