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

## Table of Contents

- [Project Overview](#project-overview)
- [Objectives](#objectives)
- [Network Topology](#network-topology)
- [Connection and IPv4 Addresses](#Connection-and-IPv4-Addresses)
- [Configuration Sections](#configuration-sections)
  - [PART 1 - Initial Setup](#PART-1---Initial-Setup)
  - [PART 2 - VLANs,Layer 2 Etherchannel](#PART-2---VLANs-Layer-2-Etherchannel)
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

## Project Overview

This project is a comprehensive CCNA mega lab built using Cisco Packet Tracer to simulate a real enterprise network environment.

The lab focuses on core networking technologies including routing, switching and network security.

The purpose of this project is to understand how a computer network works in enterprise organizations.

---

## Objectives

The main goals of this project are:

- Design a scalable enterprise network
- Implement VLAN segmentation
- Configure inter-VLAN routing
- Deploy dynamic routing protocol(OSPF)
- Configure DHCP,DNS and NAT services
- Apply ACL-based security
- Implement SSH remote management

---

## Network Topology

The network topology for this lab is a three-tier LAN consisting of two offices, Office A on the left and Office B on the right. The two offices are connected via a core layer, and R1 provides internet access with two redundant connections.

<img width="1853" height="1059" alt="image" src="https://github.com/user-attachments/assets/7dfc03ca-d263-4591-b02e-0206c3dd1ce7" />

---

## Connection and IPv4 Addresses

<img width="1597" height="933" alt="image" src="https://github.com/user-attachments/assets/4b3bdc34-6dd8-4851-8e16-1d27cdedb937" />

---

## Configuration Sections

#### PART 1 - Initial Setup

1. Configure the appropriate hostname on each router/switch.
   
   We use hostname command for all switches and router. For example, "hostname R1". I do it for all devices
   <img width="781" height="193" alt="image" src="https://github.com/user-attachments/assets/9e91d961-4339-427b-890d-357a9d53c147" />


2. Configure the enable secret jeremysitlab on each router/switch. Use type 9 hashing if available; otherwise, use type 5.
   
   To configure Cisco devices, you need to switch from User EXEC mode to Privileged EXEC (enable) mode. If you don't protect this switch with a password, anyone connecting to the device via console or SSH can change all the settings. In the past, the enable password command was used for this process, but this command displays the password in plain text in the configuration file (show running-config). The enable secret command, on the other hand, saves the password by hashing it directly.
   Type 5 (MD5 Algorithm): This was used as the standard for many years. However, today, the MD5 algorithm can be relatively easily cracked with modern computers and password cracking tables called "Rainbow Tables".
   Type 9 (scrypt Algorithm): This is a much stronger hashing method offered by Cisco in modern iOS versions. It shows very high resistance to brute-force attacks.
   
   Since R1 and all access switches do not support the "algorithm-type" command, they will use algorithm type 5, while core and distribution switches will use algorithm type 9 as they do support that command.
   for R1 and all access switches:
   <img width="786" height="252" alt="image" src="https://github.com/user-attachments/assets/fd0200cc-b655-428e-9dd2-34baf63f9c16" />

   for all core and distribution switches:
   <img width="962" height="252" alt="image" src="https://github.com/user-attachments/assets/8c544575-f69f-4301-96aa-f7dfff8b28aa" />


3. Configure the user account cisco with secret ccna on each router/switch. Use type 9 hashing if available; otherwise, use type 5.
   
   Previously, a single shared password (password...) was assigned under line vty 0 4 or line con 0 to connect to devices remotely or via console. This is a significant security risk because you can't know who logged in to the device. You'll learn how to create a Local User Database using the command "username cisco secret ccna". This allows you to create separate accounts for different users on the company network in the future, and if a problem occurs, you can see exactly who logged in from the device logs (this is called Accountability in the IT world).
   for R1 and all access switches:
   <img width="823" height="304" alt="image" src="https://github.com/user-attachments/assets/0a01d694-87e2-47c2-b535-6dc03563acc6" />

   for all core and distribution switches:
   <img width="1044" height="309" alt="image" src="https://github.com/user-attachments/assets/10426edc-eb0a-4ada-b6c4-720e1200a618" />





4. Configure the console line to require login with a local user account. Set a 30-minute inactivity timeout. Enable synchronous logging.
   
   The Cisco user account you created in the previous task is useless on its own; the device needs to know where to request that account. Someone who simply plugs in the blue console cable shouldn't be taken directly to the configuration screen. The console line (line con 0) is the gateway used by those physically accessing the device. Under console configuration, using the "login local" command tells the device: "When someone wants to log in from the console, check the local database I created and request the username/password there before letting them in." This prevents unauthorized actions by someone entering the data center.

   Imagine you're in a server room connected to the device via console cable, but you leave your laptop open and go out because of an urgent phone call. If the screen remains open, anyone entering the room could use your high-privilege session to crash the network. The "exec-timeout 30 0" command (30 minutes, 0 seconds) automatically logs you out for your security if the keyboard remains untouched for 30 minutes.

   While you're typing a long and complex command on the Cisco CLI (command line), an event might occur in the background (for example, a port might close) and a system log (syslog) message might suddenly appear on the screen. This message interrupts your half-written command, creating confusion on the screen. The "logging synchronous" command prints your currently typed command again on a new line without any interruption, even when system messages appear on the screen. It prevents interruptions and errors while typing commands.

   We will use the same configuration on all devices:
   <img width="803" height="510" alt="image" src="https://github.com/user-attachments/assets/873e14b0-189e-4d30-8e5e-fc627d5db354" />


#### PART 2 - VLANs Layer 2 Etherchannel

1. In Office A, configure a Layer-2 EtherChannel named PortChannel1 between DSW-A1 and DSW-A2 using a Cisco-proprietary protocol. Both switches should actively try to form an EtherChannel.
   
   There are two cables between the DSW-A1 and DSW-A2 devices in Office A. Why do we run two cables between two switches? So that if one breaks, the other can continue to work. However, in the networking world, there's a traffic policeman called Spanning Tree Protocol (STP). To prevent infinite loops in the network, STP shuts down one of these two cables for safety reasons. So you want both cables to work simultaneously, doubling your bandwidth, but STP won't allow that. EtherChannel technology logically taps these two physical cables together, making them appear as a single thick cable (PortChannel). STP no longer sees two separate cables, but a single thick cable, and doesn't shut either down. The capacity of both cables (e.g., 1 Gigabit + 1 Gigabit) combines to create a giant 2 Gigabit highway. If one breaks, the system continues to operate uninterrupted over the remaining cable (at 1 Gigabit speed).

   Switches need to communicate and understand each other to connect these cables. There are two different protocols used to achieve this: LACP (Link Aggregation Control Protocol): This is the industry standard. You would use it if connecting a Cisco switch to an HP or Juniper switch. PAgP (Port Aggregation Protocol): This is a Cisco-invented protocol known only by Cisco devices. In this task, we will use the PAgP protocol to connect the devices. The task wants that both switches should actively try to form an etherchannel, so we should do mode as "desirable".

   I ran the command "sh cdp nei" to see which ports DSW-A1 uses for DSW-A2:
   <img width="1632" height="462" alt="image" src="https://github.com/user-attachments/assets/eaf62c72-0e61-424b-b20d-8bc34e3ce7fa" />

   g1/0/4-5 interfaces
   
   For DSW-A1:  
   <img width="1997" height="808" alt="image" src="https://github.com/user-attachments/assets/3743d7aa-3af4-4109-8e75-587256020df6" />

   For DSW-A2:
   <img width="2004" height="758" alt="image" src="https://github.com/user-attachments/assets/987a70b2-8cec-475a-a307-90fa700e99e6" />

   verify:
   <img width="1707" height="729" alt="image" src="https://github.com/user-attachments/assets/d33a0662-ad5c-4081-8d35-3ce04dec9916" />

   
2. In Office B, configure a Layer-2 EtherChannel named PortChannel1 between DSW-B1 and DSW-B2 using an open standard protocol. Both switches should actively try to form an EtherChannel.
   
   Here in the task, we should use open standard protocol, so it is LACP. LACP has 2 mode, active and passive. Here we should use active because both switches should actively try to form an etherchannel

   For DSW-B1:
   <img width="1980" height="619" alt="image" src="https://github.com/user-attachments/assets/aefe9051-27ac-4b4e-b5c5-bcc033e5a2f6" />

   For DSW-B2:
   <img width="1974" height="737" alt="image" src="https://github.com/user-attachments/assets/07224e5c-d68a-473b-9e31-583d12351d41" />

   Verify:
   <img width="1766" height="752" alt="image" src="https://github.com/user-attachments/assets/4acb3e77-d904-490e-af47-4461b974ab14" />



3. Configure all links between Access and Distribution switches, including the EtherChannels, as trunk links.
   
   Switches use "Access" mode when connecting to computers, and only a single VLAN (e.g., Accounting only) passes through these ports. However, when connecting two switches, all internal VLANs (Accounting, HR, Administration, etc.) need to be able to pass through this cable. The main task is to trunk the connections between Access and Distribution switches, turning these paths into multi-lane highways.

   a. Explicitly disable DTP on all ports.
   
   DTP (Dynamic Trunking Protocol) is a protocol that allows Cisco switches to communicate with each other and automatically establish trunks. While it sounds practical, it's a major security vulnerability in the real world. A malicious actor could connect a computer to the network, impersonate a switch, and send a DTP message. If the port is in automatic mode, it establishes a trunk connection, and the attacker gains access to all VLANs on the network. This is called a VLAN Hopping attack.

   b. Set each trunk’s native VLAN to VLAN 1000 (unused).
   
   Switches add "VLAN Tags" to packets to determine which VLAN the data passing through trunk connections belongs to. However, if there is untagged traffic passing through the trunk, it is automatically routed to VLAN 1 (Native VLAN). Hackers are aware of this and use VLAN 1 to carry out attacks called "Native VLAN Spoofing". We should move the Native VLAN from its default location (VLAN 1) to a "dead" or "black hole" VLAN (VLAN 1000) that has no function on the network. This way, untagged, rogue traffic will be trapped within VLAN 1000 and unable to go anywhere.

   c. In Office A, allow VLANs 10, 20, 40, and 99 on all trunks.

   d. In Office B, allow VLANs 10, 20, 30, and 99 on all trunks.
```
   DSW-A1, DSW-A2:
   int range g1/0/1-3
   sw mode trunk
   sw nonegotiate
   sw trunk native vlan 1000
   switchport trunk allowed vlan 10,20,40,99
   int po1
   sw mode trunk
   sw nonegotiate
   sw trunk native vlan 1000
   sw trunk allowed vlan 10,20,40,99
```
```
   ASW-A1, ASW-A2, ASW-A3:
   int range g1/0/1-2
   sw mode trunk
   sw nonegotiate
   sw trunk native vlan 1000
   switchport trunk allowed vlan 10,20,40,99
```
```
   DSW-B1, DSW-B2:
   int range g1/0/1-3
   sw mode trunk
   sw nonegotiate
   sw trunk native vlan 1000
   switchport trunk allowed vlan 10,20,30,99
   int po1
   sw mode trunk
   sw nonegotiate
   sw trunk native vlan 1000
   sw trunk allowed vlan 10,20,30,99
```
```
   ASW-B1, ASW-B2, ASW-B3:
   int range g1/0/1-2
   sw mode trunk
   sw nonegotiate
   sw trunk native vlan 1000
   switchport trunk allowed vlan 10,20,30,99
 ```  




   Looking which interfaces that we use for access layer switches in DSW-A1:
   <img width="948" height="295" alt="image" src="https://github.com/user-attachments/assets/8e539055-b344-456a-9b92-82ef509841dc" />

   make them trunk:
   <img width="1092" height="585" alt="image" src="https://github.com/user-attachments/assets/84f72764-cecc-40ae-ac8a-bf56e9f2c672" />

   also same processes for etherchannel port(po1) (tasks a,b,c also here in the screenshot):
   <img width="1634" height="787" alt="image" src="https://github.com/user-attachments/assets/15b14c96-2159-4fa4-93da-6d0b95cb5185" />

   DSW-A2:
   <img width="1587" height="1182" alt="image" src="https://github.com/user-attachments/assets/088829e2-adcb-43aa-bf68-e62edc1bdecb" />
   <img width="1630" height="1155" alt="image" src="https://github.com/user-attachments/assets/9da69500-44e1-4d7a-8685-900e775968d8" />



  Disable DTP on all ports for DSW-A1: <br>
<img width="597" height="100" alt="image" src="https://github.com/user-attachments/assets/324d63a9-7b0e-4981-9e28-46c679e011ce" />


  Doing native vlan 1000 for DSW-A1: <br>
<img width="659" height="135" alt="image" src="https://github.com/user-attachments/assets/9fc05ebd-3fb7-4f54-8428-599e459e0ef2" />

   allowed vlans for DSW-A1:
   <img width="1102" height="397" alt="image" src="https://github.com/user-attachments/assets/ed1a3fd8-dbaa-4ba1-ade5-4cc7c7dbbd86" />



6. Configure one of each office’s Distribution switches as a VTPv2 server. Use domain name JeremysITLab.
a. Verify that other switches join the domain.
b. Configure all Access switches as VTP clients.
7. In Office A, create and name the following VLANs on one of the Distribution switches. Ensure that VTP propagates the changes.
a. VLAN 10: PCs
b. VLAN 20: Phones
c. VLAN 40: Wi-Fi
d. VLAN 99: Management
8. In Office B, create and name the following VLANs on one of the Distribution switches. Ensure that VTP propagates the changes.
a. VLAN 10: PCs
b. VLAN 20: Phones
c. VLAN 30: Servers
d. VLAN 99: Management
9. Configure each Access switch’s access port. 
a. LWAPs will not use FlexConnect
b. PCs in VLAN 10, Phones in VLAN 20
c. SRV1 in VLAN 30
d. Manually configure access mode and explicitly disable DTP
10. Configure ASW-A1’s connection to WLC1:
a. It must support the Wi-Fi and Management VLANs.
b. The Management VLAN should be untagged.
c. Disable DTP.
11. Administratively disable all unused ports on Access and Distribution switches.

