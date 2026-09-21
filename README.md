# Cybersecurity Labs

A curated record of practical cybersecurity methodology, investigation and lessons learned as I develop toward **offensive security and penetration testing**.

This repository connects Security+ and networking foundations with daily Linux use and authorised hands-on labs. It is not a mirror of all my private study notes or a collection of room solutions; material is included when it demonstrates a reusable process, useful evidence or a meaningful change in understanding.

## Where This Fits

```text
Security+ and networking concepts
                ↓
Controlled labs and system investigation
                ↓
Evidence, interpretation and repeatable methodology
                ↓
Python security projects and deeper practical work
                ↓
PEN-200 and OSCP preparation
```

The current focus is on Linux investigation, reconnaissance, network enumeration, web-security fundamentals and disciplined troubleshooting.

## Start Here

- [Enumeration Methodology](methodology/enumeration-methodology.md) — deciding what to test next from the evidence already collected.
- [Nmap Methodology](reconnaissance/nmap-methodology.md) — staged host, port, service and script scanning in authorised environments.
- [Network Enumeration Basics](networking/network-enumeration-basics.md) — connecting addresses, ports, protocols and services.
- [Web Security Fundamentals](web-security/web-security-fundamentals.md) — the request-and-response model behind later web testing.
- [Linux Incident Triage](linux/linux-incident-triage.md) — correlating logs, authentication, scheduled tasks and application artefacts.

## Repository Structure

Material is organised by reusable technical topic rather than by training platform:

```text
cybersecurity-labs/
├── linux/              # Host investigation and Linux case studies
├── methodology/        # Reusable investigation approaches
├── networking/         # Networking and enumeration foundations
├── reconnaissance/     # Authorised reconnaissance methodology
└── web-security/       # Web technologies and security concepts
```

### Linux Investigation

- [Linux Filesystem Analysis](linux/linux-filesystem-analysis.md) — targeted searches, metadata, timestamps, hashing and trusted tooling.
- [Linux Process Analysis](linux/linux-process-analysis.md) — process snapshots, lineage, open resources, services and persistence.
- [Linux Incident Triage](linux/linux-incident-triage.md) — log sources, authentication events, scheduled tasks and evidence correlation.
- [Linux System Freeze Investigation](linux/linux-system-freeze-investigation.md) — an evidence-led troubleshooting case study involving desktop and graphics instability.

### Methodology and Reconnaissance

- [Enumeration Methodology](methodology/enumeration-methodology.md) — a reusable observe, interpret and investigate-next workflow.
- [Troubleshooting Methodology](methodology/troubleshooting-methodology.md) — narrowing faults through hypotheses and controlled checks.
- [Nmap Methodology](reconnaissance/nmap-methodology.md) — progressive reconnaissance without treating scanner output as a conclusion.

### Networking and Web Security

- [Network Enumeration Basics](networking/network-enumeration-basics.md) — core network observations that support service enumeration.
- [Web Security Fundamentals](web-security/web-security-fundamentals.md) — HTTP concepts, attack surface and introductory testing logic.

## Learning and Documentation Approach

My working cycle is:

**Learn → Observe → Practise → Reinforce → Checkpoint**

Where appropriate, each published note answers six questions:

1. **Objective** — what am I trying to understand or investigate?
2. **Method** — why is this approach appropriate?
3. **Evidence** — what did the system or tool actually show?
4. **Interpretation** — what can and cannot be concluded from it?
5. **Next step** — which follow-up action does the evidence support?
6. **Lessons learned** — what would I repeat, change or verify next time?

> Tool output is evidence, not a verdict. A discovered service, unusual process or missing header is a reason to investigate further, not automatic proof of compromise or vulnerability.

For example, a port that does not answer may be closed, filtered or excluded by the scan method. A process name alone can also look suspicious without being malicious; its parent, executable path, user, open files and network activity provide the context needed to judge it.

## Related Projects

- [Recon Helper](https://github.com/g5live/recon-helper) applies early DNS, TCP and HTTP checks in a small Python workflow.
- [Security Command Lab](https://github.com/g5live/security-command-lab) turns security commands and concepts into timed practical questions.

These projects complement the notes: the labs develop the reasoning, while the applications reinforce Python and make that reasoning repeatable.

## Current Development Direction

- Extend practical coverage without publishing flags or full walkthroughs.
- Add concise lab evidence where it demonstrates a transferable technique.
- Strengthen the connection between Security+, CCNA-level networking and practical investigation.
- Progress from isolated commands toward complete reconnaissance, enumeration and validation workflows.
- Continue Python and OOP development through small, explainable security projects.

Sensitive information, credentials, flags and direct solutions to active training challenges are not published. All testing is limited to systems I own or environments where I have explicit permission.

---

*Practical learning, documented through methodology, investigation and reinforcement.*
