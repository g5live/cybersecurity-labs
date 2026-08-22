# Nmap Reconnaissance Methodology

## Purpose

Nmap is one of the tools I use during practical cybersecurity labs to identify hosts, exposed services and potential areas for further investigation.

This document records my developing approach to Nmap and network enumeration. It is intended to demonstrate methodology and understanding rather than act as a command reference or walkthrough for individual training environments.

## 1. Start With the Objective

Before running a scan, establish what information is actually required.

Initial questions might include:

* Is the target host reachable?
* Which TCP ports are exposed?
* What services appear to be running?
* Can service versions be identified?
* Does the information discovered suggest a useful next enumeration step?

The aim is to gather information progressively rather than immediately running every available scan option.

## 2. Initial Enumeration

A basic scan can provide an initial view of the target:

```bash
nmap <target>
```

This checks Nmap's default set of commonly used TCP ports.

The results provide a starting point for deciding what requires further investigation.

## 3. Full TCP Port Scan

When appropriate, I can expand the scan to all TCP ports:

```bash
nmap -p- <target>
```

A full port scan helps identify services operating on ports that would not appear during the default scan.

Rather than treating an open port as a vulnerability, I treat it as information:

**open port → probable service → verify service → enumerate further**

## 4. Service and Version Detection

Once interesting ports have been identified, service detection can provide additional information:

```bash
nmap -sV -p <ports> <target>
```

The objective is to understand what is actually listening on those ports rather than relying only on the conventional service associated with a port number.

Version information may then help determine what further research or enumeration is appropriate.

## 5. Default NSE Scripts

Nmap's default scripts can provide additional information about identified services:

```bash
nmap -sC -sV -p <ports> <target>
```

I use this as a more focused enumeration step after identifying relevant ports rather than assuming that additional scanning automatically produces useful information.

## 6. Interpreting Results

The important part of enumeration is not simply producing output but deciding what that output means.

For example:

```text
22/tcp   open   ssh
80/tcp   open   http
```

This creates separate investigation paths.

**SSH / Port 22**

Possible questions:

* What SSH implementation/version is exposed?
* Does the service reveal useful configuration information?
* Have credentials or usernames been discovered elsewhere?
* Is further SSH-specific enumeration justified?

**HTTP / Port 80**

Possible questions:

* What web application is being served?
* What technology is being used?
* Are there additional directories or endpoints?
* Does the application expose information that creates another enumeration path?

The scan therefore informs the next action rather than being the end of reconnaissance.

## 7. Working Method

My current approach can be summarised as:

```text
Identify target
      ↓
Initial scan
      ↓
Identify exposed ports
      ↓
Verify services
      ↓
Gather service information
      ↓
Choose targeted enumeration
      ↓
Record findings
      ↓
Form next hypothesis
```

The key principle is to allow discovered information to determine the next step.

## Lessons Learned

* An open port is information, not automatically a vulnerability.
* Port numbers alone should not be treated as proof of the service running behind them.
* Broad enumeration is useful, but targeted follow-up produces more meaningful information.
* Scan output should lead to questions and hypotheses.
* Recording why a command was used is more valuable than simply collecting commands.
* Enumeration should become progressively more focused as information is gathered.

## Development Notes

This methodology will be updated as I gain experience with additional Nmap techniques, network protocols and practical environments.

Commands and techniques will only be added when I have used them and can explain their purpose and output confidently.
