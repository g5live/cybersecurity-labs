# Network Enumeration Basics

## Purpose

This document records the networking concepts I use when approaching practical cybersecurity labs.

The aim is to understand what information a network exposes, what that information means, and how it can guide further investigation.

## Core Concepts

### IP Addresses

An IP address identifies a device or network interface using the Internet Protocol.

During enumeration, identifying the target IP establishes where subsequent investigation will be directed.

I am currently developing my understanding of:

- IPv4 addressing
- Private and public address ranges
- Network and host portions
- Subnets and subnet masks
- Default gateways

### Ports

Ports allow different network services to communicate through the same host.

Common examples include:

- 22 — SSH
- 53 — DNS
- 80 — HTTP
- 443 — HTTPS

A port number can suggest what service might be present, but the actual service should be verified rather than assumed.

### TCP and UDP

TCP and UDP provide different methods of transporting data across networks.

**TCP** is connection-oriented and provides mechanisms for reliable delivery.

**UDP** is connectionless and does not provide the same delivery guarantees.

Understanding which protocol a service uses affects how it can be discovered and enumerated.

## Enumeration Approach

My current basic workflow is:

Target
  ↓
Identify network information
  ↓
Discover exposed ports
  ↓
Identify protocols and services
  ↓
Verify what is actually running
  ↓
Choose appropriate further enumeration

## Relationship to Nmap

Nmap provides a practical way to investigate many of these concepts.

Rather than treating Nmap output as a list of ports, I want to understand the network behaviour responsible for the results.

For example:

22/tcp open ssh

This tells me more than simply "port 22 is open":

- TCP communication is available on that port.
- A service has responded.
- Nmap believes the service is SSH.
- Further enumeration may establish the implementation and version.
- The information may create another investigation path.

## Current Development

Networking is a current development priority and this document will expand as I work through routing, subnetting, protocols, DNS and other network services.

I will add concepts as I use and understand them rather than attempting to create a complete networking reference in advance.
