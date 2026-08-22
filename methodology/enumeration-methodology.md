# Enumeration Methodology

## Purpose

This document records my developing approach to enumeration during practical cybersecurity work.

Enumeration is not simply the process of running tools. Its purpose is to progressively build an understanding of a target and use discovered information to determine the next investigation step.

## 1. Establish Scope

Before interacting with a target, establish what is authorised and what systems are within scope.

In training environments this normally means confirming the assigned target and any restrictions defined by the lab.

## 2. Start Broad

Begin by identifying the basic information available about the target.

Questions may include:

- Is the host reachable?
- Which ports are exposed?
- Which protocols are in use?
- What services appear to be running?

At this stage the objective is discovery rather than exploitation.

## 3. Verify Services

A port number may suggest a likely service, but assumptions should be verified.

For each interesting port:

Port
  ↓
Protocol
  ↓
Service
  ↓
Version / implementation
  ↓
Configuration / exposed information

The information gathered determines whether deeper enumeration is justified.

## 4. Follow the Evidence

Each discovered service creates a potential investigation path.

For example:

Open TCP ports
   ↓
22 / SSH      80 / HTTP
   ↓              ↓
SSH enum       Web enum
   ↓              ↓
Version        Application
Configuration  Endpoints
Credentials?   Inputs
   ↓              ↓
Further investigation

The objective is not to test everything against everything.

The objective is to allow evidence to narrow the investigation.

## 5. Enumerate the Service

Once a service is identified, move from general network enumeration to service-specific investigation.

Possible questions include:

- What implementation is running?
- Is version information available?
- Does the service expose configuration information?
- Are authentication mechanisms present?
- Are additional resources or endpoints discoverable?
- Does information from another service provide useful context?

## 6. Correlate Findings

Individual findings may become more useful when considered together.

For example:

Username discovered
        +
Remote authentication service
        +
Additional application information
        ↓
New investigation path

Information should therefore be recorded even when its immediate significance is unclear.

## 7. Form Hypotheses

Enumeration should progressively produce testable questions.

Examples:

- Does this web application expose additional directories?
- Does this service version require further research?
- Is a discovered username valid elsewhere?
- Does information from one service relate to another?

A hypothesis gives the next action a purpose.

## 8. Record Findings

For useful discoveries, record:

- What was found
- How it was discovered
- Why it may matter
- What should be investigated next

This prevents repeated work and makes the reasoning behind later actions easier to reconstruct.

## 9. Avoid Premature Exploitation

Finding an open service does not mean exploitation should immediately begin.

My preferred sequence is:

Discover
   ↓
Identify
   ↓
Verify
   ↓
Enumerate
   ↓
Correlate
   ↓
Form hypothesis
   ↓
Test appropriately

Thorough enumeration can reveal information that makes later testing more focused and understandable.

## Current Principle

**Don't ask: "What tool should I run next?"**

Ask:

**"What have I learned, and what question does that information allow me to investigate next?"**

This methodology will evolve as I gain experience with additional protocols, services and offensive-security techniques.
