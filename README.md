# Enterprise Network Lab
This project is a large-scale lab designed to simulate a real enterprise network environment.

The lab includes:
IPv4 and IPv6, 
static routes, 
VLANs, 
spanning tree, 
OSPF, 
EtherChannel, 
DHCP, 
DNS, 
NAT, 
SSH, 
security features, 
wireless,
and more...

---

# Table of Contents

- [Project Overview](#project-overview)
- [Objectives](#objectives)
- [Network Topology](#network-topology)
- [Connection and IPv4 Addresses](#Connection-and-IPv4-Addresses)
- [Configuration Sections](#configuration-sections)
  - [VLAN Configuration](#vlan-configuration)
  - [Inter-VLAN Routing](#inter-vlan-routing)
  - [Spanning Tree Protocol](#spanning-tree-protocol)
  - [EtherChannel Configuration](#etherchannel-configuration)
  - [Static Routing](#static-routing)
  - [OSPF Configuration](#ospf-configuration)
  - [DHCP Configuration](#dhcp-configuration)
  - [DNS Configuration](#dns-configuration)
  - [NAT Configuration](#nat-configuration)
  - [SSH Configuration](#ssh-configuration)
  - [Wireless Configuration](#wireless-configuration)
  - [Security Features](#security-features)
  - [IPv6 Configuration](#ipv6-configuration)
- [Verification and Testing](#verification-and-testing)
- [Conclusion](#conclusion)

---

# Project Overview

This project is a comprehensive CCNA mega lab built using Cisco Packet Tracer to simulate a real enterprise network environment.

The lab focuses on core networking technologies including routing, switching and network security.

The purpose of this project is to understand how a computer network works in enterprise organizations.

---

# Objectives

The main goals of this project are:

- Design a scalable enterprise network
- Implement VLAN segmentation
- Configure inter-VLAN routing
- Deploy dynamic routing protocol(OSPF)
- Configure DHCP,DNS and NAT services
- Apply ACL-based security
- Implement SSH remote management

---

# Network Topology

The network topology for this lab is a three-tier LAN consisting of two offices, Office A on the left and Office B on the right. The two offices are connected via a core layer, and R1 provides internet access with two redundant connections.

<img width="1853" height="1059" alt="image" src="https://github.com/user-attachments/assets/7dfc03ca-d263-4591-b02e-0206c3dd1ce7" />

---

# Connection and IPv4 Addresses

<img width="1597" height="933" alt="image" src="https://github.com/user-attachments/assets/4b3bdc34-6dd8-4851-8e16-1d27cdedb937" />

---

# Configuration Sections

### PART 1 - Initial Setup

1. Configure the appropriate hostname on each router/switch.
2. Configure the enable secret jeremysitlab on each router/switch. Use type 9 hashing if available; otherwise, use type 5.
3. Configure the user account cisco with secret ccna on each router/switch. Use type 9 hashing if available; otherwise, use type 5.
4. Configure the console line to require login with a local user account. Set a 30-minute inactivity timeout. Enable synchronous logging.

