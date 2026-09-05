# Linux Filesystem Analysis

## Overview

Filesystem analysis can help establish what changed on a Linux host, which account owns an artefact and whether a file is consistent with its expected purpose. The strongest conclusions come from combining ownership, permissions, timestamps, type, hashes and surrounding system evidence rather than relying on one attribute.

Live investigation can change access times and other state. Where evidence may be required for formal forensic work, preserve it first and follow the organisation's evidence-handling procedure.

## Establishing a Trusted Toolset

On a potentially compromised host, commands found through the host's normal `PATH` and shared libraries loaded through `LD_LIBRARY_PATH` may not be trustworthy. A response environment can instead use known-good tools from controlled, preferably read-only, media.

```bash
export PATH=/mnt/usb/bin:/mnt/usb/sbin
export LD_LIBRARY_PATH=/mnt/usb/lib:/mnt/usb/lib64
```

These variables affect command and library resolution; they do not make the host itself trusted. Exporting `LD_LIBRARY_PATH` can also produce incompatible or unexpected library loading. Statically linked response tools, explicit absolute paths and a documented validation process are preferable where available. `check-env` is not a standard Linux command, so it should only be used if it is a known component of the response toolkit.

## Finding Relevant Files

A useful first pass is to examine locations writable by exposed services, such as an application's upload directory, then expand the search according to the evidence.

```bash
ls -la /var/www/html/uploads
find / -user www-data -type f 2>/dev/null | less
find / -group GROUPNAME 2>/dev/null | less
find / -perm -o+w 2>/dev/null | less
find / -type f -cmin -5 2>/dev/null | less
```

These commands answer different questions:

| Command | Purpose |
|---|---|
| `find / -user www-data -type f` | Finds regular files owned by `www-data`. Ownership alone does not prove the service created them. |
| `find / -group GROUPNAME` | Finds entries whose owning group matches the supplied group. |
| `find / -perm -o+w` | Finds world-writable entries; expected shared locations must be separated from unsafe permissions. |
| `find / -type f -cmin -5` | Finds files whose inode status changed in the last five minutes. It does **not** mean the files were created then. |

`2>/dev/null` suppresses error output, commonly permission-denied messages. This makes interactive output easier to read but can hide useful failures, so those errors should be retained when completeness matters. `less` provides paged review; press `q` to exit.

## File Timestamps

Linux commonly exposes several timestamps:

- **Modification time (`mtime`)** — when file contents were last modified.
- **Change time (`ctime`)** — when inode status last changed, including content writes, ownership or permission changes. It is not creation time.
- **Access time (`atime`)** — when content was last accessed, subject to filesystem mount options such as `relatime` or `noatime`.
- **Birth time (`btime`)** — creation time, when supported by the filesystem and tools.

```bash
ls -l FILE       # mtime
ls -lc FILE      # ctime
ls -lu FILE      # atime
stat FILE        # available timestamps and inode metadata
```

Timestamps can be altered, may have different precision, and should be interpreted with time zone, clock accuracy and filesystem behaviour in mind. A timeline is most useful when compared with logs, process activity and other independent artefacts.

## Type and Metadata

File extensions are only labels. Inspect the detected content and metadata instead:

```bash
file FILE
stat FILE
exiftool FILE
```

`file` identifies likely content from signatures and structure. `stat` reports filesystem metadata. ExifTool supports many formats and can expose embedded metadata, but displayed values are descriptive evidence rather than proof of origin or authenticity.

## Hashing and Integrity

Hashes provide stable identifiers for exact byte sequences and support integrity checks, deduplication and comparison with approved baselines or threat-intelligence sources.

```bash
sha256sum FILE
md5sum FILE
```

SHA-256 is the better general-purpose integrity choice. MD5 remains useful when matching legacy indicators, but known collision weaknesses make it unsuitable where collision resistance matters. A hash match shows that two byte sequences are identical; a hash mismatch shows only that they differ, not why.

## Analytical Workflow

```text
Define the question and time window
        ↓
Preserve evidence where required
        ↓
Use known-good tools
        ↓
Search relevant paths, owners and permissions
        ↓
Inspect type, metadata, timestamps and hashes
        ↓
Correlate with processes, persistence and logs
        ↓
Record observations separately from conclusions
```

## Key Lessons

- File ownership, location or recency can guide investigation but does not establish maliciousness by itself.
- `ctime` records inode-status change, not file creation.
- Suppressing errors improves readability at the cost of visibility.
- Live analysis can alter evidence.
- Conclusions should be based on correlated evidence and an understood system baseline.
