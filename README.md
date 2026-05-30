# Enterprise Infrastructure
This project is a large-scale lab designed to simulate a real enterprise infrastructure environment.

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
  - [PART 3 - IP Adresses, Layer 3 EtherChannel, HSRP](#PART-3---IP-Adresses-Layer-3-EtherChannel-HSRP)
  - [PART 4 - Rapid Spanning Tree Protocol](#Part-4---Rapid-Spanning-Tree-Protocol)
  - [PART 5 - Static and Dynamic Routing](#PART-5---Static-And-Dynamic-Routing)
  - [PART 6 - Network Services: DHCP, DNS, NTP, SNMP, Syslog, FTP, SSH, NAT](#part-6---network-services-dhcp-dns-ntp-snmp-syslog-ftp-ssh-nat)
  - [PART 7 - Security: ACLs and Layer-2 Security Features](#part-7---security-acls-and-layer-2-security-features)
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
   int range g0/1-2
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
   int range g0/1-2
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

  do this processes for each devices.
  

4. Configure one of each office’s Distribution switches as a VTPv2 server. Use domain name JeremysITLab.
a. Verify that other switches join the domain.
b. Configure all Access switches as VTP clients.

   This task aims to do Cisco's proprietary VTP (VLAN Trunking Protocol) concept, which enables the automation of VLAN management from a central point instead of visiting each switch on the network individually. The task involves making a Distribution switch a VTP Server to manage all VLAN changes from a single point, and setting Access switches to VTP Client mode to automatically synchronize these changes over a common domain (VTP Domain: JeremysITLab), saving time and eliminating human configuration errors. However, while this protocol offers ease of management in a lab environment, it poses a significant cybersecurity and accessibility risk in real-world networks. A VTP bomb – a higher-revision number on an older or test switch added later – could overwrite the entire live network's VLAN database in seconds. Therefore, VTPv3 or VTP Transparent modes are generally preferred over these.

   in the picture, we see that there is no vtp domain name. Default version is 1 and default operating mode is server.
   <img width="950" height="443" alt="image" src="https://github.com/user-attachments/assets/9695c3d5-c6fe-4fa9-8c00-0fd1ab90df20" />

   Now we need to configure a domain and set the version to 2.
   <img width="1090" height="195" alt="image" src="https://github.com/user-attachments/assets/7c7bfad4-a81d-4469-9bd5-17b923ed02fa" />

   Performing these configurations should cause DSW-A1 to send VTP messages and for other switches to join the domain. Lets check the ASW-A3 switch.
   <img width="980" height="502" alt="image" src="https://github.com/user-attachments/assets/f175eeab-ccc1-44b4-91a7-7c826e128dc7" />

   Then I will do vtp mode client for all access layer switches.
   <img width="567" height="139" alt="image" src="https://github.com/user-attachments/assets/bc83e3d1-8c54-4e59-8c47-7cb69d70653f" />
   <img width="540" height="153" alt="image" src="https://github.com/user-attachments/assets/048a126c-ceb6-4d1d-a571-dfd403dee9c9" />
   <img width="644" height="199" alt="image" src="https://github.com/user-attachments/assets/11eeae43-6419-40cd-aae5-380ed564360b" />

   These all was for office A. I did same configs for office B too. VTP messages are only transmitted through trunk ports, and we haven't yet configured the connections between the distribution and core layers. Therefore, VTP messages from Office A will not reach Office B.
   

   








8. In Office A, create and name the following VLANs on one of the Distribution switches. Ensure that VTP propagates the changes.
a. VLAN 10: PCs
b. VLAN 20: Phones
c. VLAN 40: Wi-Fi
d. VLAN 99: Management

    In tasks 5 and 6, we will configure the required VLANs on the DSW-A1 and DSW-B1 switches, which we previously set up as VTP servers. These VTP servers will then distribute the VLANs to all switches on the network.
   <img width="510" height="307" alt="image" src="https://github.com/user-attachments/assets/7dcaf7c7-8bf1-4bda-ab48-4f1a5720f5c6" />

   verify on DSW-A1:
   <img width="1097" height="519" alt="image" src="https://github.com/user-attachments/assets/ca80827f-92ae-488c-9f16-13a1ac6f92e6" />

   verify on ASW-A3:
   <img width="1083" height="451" alt="image" src="https://github.com/user-attachments/assets/5d0f7e0a-8148-46c4-a628-c50eadd9b0f2" />





6. In Office B, create and name the following VLANs on one of the Distribution switches. Ensure that VTP propagates the changes.
a. VLAN 10: PCs
b. VLAN 20: Phones
c. VLAN 30: Servers
d. VLAN 99: Management

    same process with task 5:
    DSW-B1:
    <img width="1106" height="802" alt="image" src="https://github.com/user-attachments/assets/fe6054e4-59df-49aa-95f5-5fb75f9a415a" />

    ASW-B3:
    <img width="1056" height="482" alt="image" src="https://github.com/user-attachments/assets/b7474a95-d164-4c19-bd82-05972d625f8f" />


7. Configure each Access switch’s access port. 
a. LWAPs will not use FlexConnect
b. PCs in VLAN 10, Phones in VLAN 20
c. SRV1 in VLAN 30
d. Manually configure access mode and explicitly disable DTP

   ASW-A and B1 are both connected to LWAPs. 7A states that the LWAPs will not use FlexConnect. This means all traffic will be tunneled through the Management VLAN to WLC1. Therefore, these connections to the LWAPs can be access ports. They just need to support the Management VLAN. Incidentally, this is also why office B doesn't have a Wi-Fi VLAN. Traffic from wireless clients in office B is tunneled to WLC1 in office A and only then is it assigned to VLAN 40, which is the Wi-Fi VLAN.

   ASW-A1:
   <img width="576" height="342" alt="image" src="https://github.com/user-attachments/assets/d98e73a6-b08a-4cda-8ba7-eef1e1fdef2d" />

   ASW-B1:
   <img width="587" height="258" alt="image" src="https://github.com/user-attachments/assets/0ab0cdcf-40b0-4b69-9aad-1be525c49eb5" />

   ASW-A2, ASW-A3, ASW-B2:
   ```
   int f0/1
   switchport mode access
   switchport nonegotiate
   switchport access vlan 10
   switchport voice vlan 20
   ```
   <img width="731" height="412" alt="image" src="https://github.com/user-attachments/assets/87cfe8ef-7620-4879-87bb-a2a01b9d79b1" />
   <img width="890" height="571" alt="image" src="https://github.com/user-attachments/assets/2bb4175a-0d9e-4325-a4ed-0491b48c45f8" />
   <img width="813" height="487" alt="image" src="https://github.com/user-attachments/assets/8b897a77-0c57-45d5-b6cb-3cf512c2e0ca" />

   ASW-B3:
   <img width="781" height="377" alt="image" src="https://github.com/user-attachments/assets/0d5be577-1d1c-49ab-875d-a625270bc5f1" />
   

8. Configure ASW-A1’s connection to WLC1:
a. It must support the Wi-Fi and Management VLANs.
b. The Management VLAN should be untagged.
c. Disable DTP.

  ASW-A1:
  <img width="1112" height="595" alt="image" src="https://github.com/user-attachments/assets/f0dd8e6e-50ca-4f99-aa9d-0cab0a778a70" />


15. Administratively disable all unused ports on Access and Distribution switches.
    DSW-A1, DSW-A2, DSW-B1, DSW-B2:
    <img width="1066" height="493" alt="image" src="https://github.com/user-attachments/assets/8e1596cf-c1ac-42ce-8978-0cea6416a515" />

    ASW-A1, ASW-A2, ASW-A3, ASW-B1, ASW-B2, ASW-B3:
    int f0/2-24
    shutdown
    

#### PART 3 - IP Adresses, Layer 3 EtherChannel, HSRP

1. Configure the following IP addresses on R1’s interfaces and enable them:
a. G0/0/0: DHCP client
b. G0/1/0: DHCP client
c. G0/0: 10.0.0.33/30
d. G0/1: 10.0.0.37/30
e. Loopback0: 10.0.0.76/32

g0/0/0 and g0/1/0 are connected to internet. They will take IPs from DHCP.
<img width="1098" height="361" alt="image" src="https://github.com/user-attachments/assets/385f4b70-faf3-4b59-a3b3-d6e9a99e3626" />

next, g0/0 is connected to CSW1 and g0/1 is connected to CSW2.
<img width="1100" height="432" alt="image" src="https://github.com/user-attachments/assets/c6a33e17-4fb3-4ba6-8cc5-9c11d62cebf3" />

next, loopback address. We dont have to do "no shut" bc loopback address is logical address.
<img width="1002" height="236" alt="image" src="https://github.com/user-attachments/assets/124a15ec-26e2-4628-bafc-dc0e3f3ec3b4" />

verify:
<img width="1009" height="255" alt="image" src="https://github.com/user-attachments/assets/1a9e42c5-0211-4d65-b2f0-d624ce5203d5" />


2. Enable IPv4 routing on all Core and Distribution switches.
   CSW1, CSW2, DSW-A1, DSW-A2, DSW-B1, DSW-B2:
   ip routing
   
3. Create a Layer-3 EtherChannel between CSW1 and CSW2 using a Cisco-proprietary protocol. Both switches should actively try to form an EtherChannel. Configure the following IP addresses:
a. CSW1 PortChannel1: 10.0.0.41/30
b. CSW2 PortChannel1: 10.0.0.42/30

   looking which interfaces connected to CSW2 from CSW1:
   <img width="938" height="267" alt="image" src="https://github.com/user-attachments/assets/6adaba58-5e43-4a72-b5d0-b22da24e3e31" />

   CSW1 conf:
   <img width="1311" height="870" alt="image" src="https://github.com/user-attachments/assets/dc51b3c7-3dfa-4437-83a9-f9fde374f7a9" />

   CSW2 conf:
   <img width="1266" height="881" alt="image" src="https://github.com/user-attachments/assets/eb4135e2-0a72-49d6-8931-16582d64f02b" />

   verify:
   <img width="848" height="467" alt="image" src="https://github.com/user-attachments/assets/f60077c3-0eb9-4b73-a381-d3f380c80af3" />




4. Configure the following IP addresses on CSW1. Disable all unused interfaces.
a. G1/0/1: 10.0.0.34/30
b. G1/1/1: 10.0.0.45/30
c. G1/1/2: 10.0.0.49/30
d. G1/1/3: 10.0.0.53/30
e. G1/1/4: 10.0.0.57/30
f. Loopback0: 10.0.0.77/32

CSW1:
  int g1/0/1 
  no switchport
  ip address 10.0.0.34 255.255.255.252
  int g1/1/1 
  no switchport
  ip address 10.0.0.45 255.255.255.252
  int g1/1/2 
  no switchport
  ip address 10.0.0.49 255.255.255.252
  int g1/1/3 
  no switchport
  ip address 10.0.0.53 255.255.255.252
  int g1/1/4 
  no switchport
  ip address 10.0.0.57 255.255.255.252
  int l0 
  no switchport
  ip address 10.0.0.77 255.255.255.255
  int range g1/0/4-24
  shutdown
  

5. Configure the following IP addresses on CSW2. Disable all unused interfaces.
a. G1/0/1: 10.0.0.38/30
b. G1/1/1: 10.0.0.61/30
c. G1/1/2: 10.0.0.65/30
d. G1/1/3: 10.0.0.69/30
e. G1/1/4: 10.0.0.73/30
f. Loopback0: 10.0.0.78/32

  CSW2:
  int g1/0/1 
  no switchport
  ip address 10.0.0.38 255.255.255.252
  int g1/1/1 
  no switchport
  ip address 10.0.0.61 255.255.255.252
  int g1/1/2 
  no switchport
  ip address 10.0.0.65 255.255.255.252
  int g1/1/3 
  no switchport
  ip address 10.0.0.69 255.255.255.252
  int g1/1/4 
  no switchport
  ip address 10.0.0.73 255.255.255.252
  int l0 
  no switchport
  ip address 10.0.0.78 255.255.255.255
  int range g1/0/4-24
  shutdown
  
6. Configure the following IP addresses on DSW-A1:
a. G1/1/1: 10.0.0.46/30
b. G1/1/2: 10.0.0.62/30
c. Loopback0: 10.0.0.79/32

  int g1/1/1
  no switchport
  ip address 10.0.0.46 255.255.255.252
  int g1/1/2
  no switchport
  ip address 10.0.0.62 255.255.255.252
  int l0
  ip address 10.0.0.79 255.255.255.255


7. Configure the following IP addresses on DSW-A2:
a. G1/1/1: 10.0.0.50/30
b. G1/1/2: 10.0.0.66/30
c. Loopback0: 10.0.0.80/32

    int g1/1/1
    no switchport
    ip address 10.0.0.50 255.255.255.252
    int g1/1/2
    no switchport
    ip address 10.0.0.66 255.255.255.252
    int l0
    ip address 10.0.0.80 255.255.255.255
  

8. Configure the following IP addresses on DSW-B1:
a. G1/1/1: 10.0.0.54/30
b. G1/1/2: 10.0.0.70/30
c. Loopback0: 10.0.0.81/32

    int g1/1/1
    no switchport
    ip address 10.0.0.54 255.255.255.252
    int g1/1/2
    no switchport
    ip address 10.0.0.70 255.255.255.252
    int l0
    ip address 10.0.0.81 255.255.255.255
    
9. Configure the following IP addresses on DSW-B2:
a. G1/1/1: 10.0.0.58/30
b. G1/1/2: 10.0.0.74/30
c. Loopback0: 10.0.0.82/32

    int g1/1/1
    no switchport
    ip address 10.0.0.58 255.255.255.252
    int g1/1/2
    no switchport
    ip address 10.0.0.74 255.255.255.252
    int l0
    ip address 10.0.0.82 255.255.255.255
    

10. Manually configure SRV1’s IP settings:
a. Default Gateway: 10.5.0.1
b. IPv4 Address: 10.5.0.4
c. Subnet Mask: 255.255.255.0
    SVR1:
    <img width="1254" height="657" alt="image" src="https://github.com/user-attachments/assets/01a24cfd-0f75-43bf-b972-54a06e8206c6" />

    <img width="1258" height="483" alt="image" src="https://github.com/user-attachments/assets/72ead372-9a0d-434f-9326-d47644d41b9d" />

11. Configure the following management IP addresses on the Access switches (interface VLAN 99), and configure the appropriate subnet’s first usable address as the default gateway.
a. ASW-A1: 10.0.0.4/28
b. ASW-A2: 10.0.0.5/28
c. ASW-A3: 10.0.0.6/28
d. ASW-B1: 10.0.0.20/28
e. ASW-B2: 10.0.0.21/28
f. ASW-B3: 10.0.0.22/28
    This task aims to teach the SVI (Switch Virtual Interface) concept, which is necessary for managing a network without going to the server room by remotely accessing Layer 2 access switches. Instead of physical ports that normally cannot obtain IP addresses, we assign a virtual management IP address to the device via interface VLAN 99, giving it an identity on the network; at this stage, we are expected to analyze the /28 subnet mask and calculate that Office A switches are on the 10.0.0.0 network and Office B switches are on the 10.0.0.16 network. Finally, in order for these switches, which only recognize their own local network, to respond to remote management requests from different IP blocks (e.g., from an administrator connecting from home via VPN), I aimed to define the first available IP addresses of these calculated subnets (10.0.0.1 and 10.0.0.17) as the Default Gateway to the device, thus charting an exit route for these devices that cannot perform routing.

    ASW-A1 default gateway and ip address:
    <img width="1013" height="289" alt="image" src="https://github.com/user-attachments/assets/ed0ee51f-6787-4ac5-bbd1-76b3eb369a85" />

    ASW-A2:
    <img width="1000" height="413" alt="image" src="https://github.com/user-attachments/assets/ccf692b5-5819-411a-8b06-26271d1a94c9" />

    ASW-A3:
    <img width="954" height="500" alt="image" src="https://github.com/user-attachments/assets/9a2f3fc6-6d41-48a5-a4c6-d78edc64a19b" />

    ASW-B1:
    <img width="970" height="440" alt="image" src="https://github.com/user-attachments/assets/59bbfb11-058a-4f8c-baa4-fac0bb88ba12" />

    ASW-B2:
    <img width="973" height="413" alt="image" src="https://github.com/user-attachments/assets/249fa3cb-6f93-4901-ba40-6e0d82af2b4d" />

    ASW-B3:
    <img width="1007" height="404" alt="image" src="https://github.com/user-attachments/assets/e371e388-10da-4ff0-ad1f-0eb033d57859" />








12. Configure HSRPv2 group 1 for Office A’s Management subnet (VLAN 99). Make DSW-A1 the Active router by increasing its priority to 5 above the default, and enable preemption on DSW-A1.
a. Subnet: 10.0.0.0/28
b. VIP: 10.0.0.1
c. DSW-A1: 10.0.0.2
d. DSW-A2: 10.0.0.3

    This task aims to eliminate "Single Point of Failure" in your network by utilizing HSRPv2 (Hot Standby Router Protocol) technology, which provides High Availability and redundancy. By assigning a Virtual IP address (VIP - 10.0.0.1) jointly provided by DSW-A1 and DSW-A2 as the default gateway for subnet devices (access switches), instead of a physical device's IP address, we ensure uninterrupted network traffic even if one device fails. While the device with the higher IP address handles routing by default, in this task you manually set the priority of DSW-A1 to 5 above the default value to activate it. You will also learn how to activate the Preemption feature, ensuring that even if the DSW-A1 device experiences a temporary malfunction and restarts, it immediately reclaims the "Active" role from the backup device (Standby) upon returning to the network, guaranteeing that traffic always flows through the main route you designed.

    DSW-A1:
    <img width="1026" height="720" alt="image" src="https://github.com/user-attachments/assets/550ccaba-6e21-4186-a442-8478c592abbd" />

    DSW-A2:
    <img width="1017" height="435" alt="image" src="https://github.com/user-attachments/assets/bec68d78-54bb-4db9-b0d5-7ea377b875cb" />



13. Configure HSRPv2 group 2 for Office A’s PCs subnet (VLAN 10). Make DSW-A1 the Active router by increasing its priority to 5 above the default, and enable preemption on DSW-A1.
a. Subnet: 10.1.0.0/24
b. VIP: 10.1.0.1
c. DSW-A1: 10.1.0.2
d. DSW-A2: 10.1.0.3
    Similar task with task 12.

    DSW-A1:
    <img width="1017" height="531" alt="image" src="https://github.com/user-attachments/assets/63ccce74-e5af-4e62-b779-37fd9d9405dd" />

    DSW-A2:
    <img width="965" height="346" alt="image" src="https://github.com/user-attachments/assets/dce5eed3-ad36-4310-b18f-f37367c8e346" />

14. Configure HSRPv2 group 3 for Office A’s Phones subnet (VLAN 20). Make DSW-A2 the Active router by increasing its priority to 5 above the default, and enable preemption on DSW-A2.
a. Subnet: 10.2.0.0/24
b. VIP: 10.2.0.1
c. DSW-A1: 10.2.0.2
d. DSW-A2: 10.2.0.3

    DSW-A1:
    <img width="1005" height="337" alt="image" src="https://github.com/user-attachments/assets/b232e541-e64c-4c35-9f8a-521d1e1f28e0" />

    DSW-A2:
    <img width="989" height="406" alt="image" src="https://github.com/user-attachments/assets/1a514547-090c-4b7d-b432-aa48c6d8c331" />

    
15. Configure HSRPv2 group 4 for Office A’s Wi-Fi subnet (VLAN 40). Make DSW-A2 the Active router by increasing its priority to 5 above the default, and enable preemption on DSW-A2.
a. Subnet: 10.6.0.0/24
b. VIP: 10.6.0.1
c. DSW-A1: 10.6.0.2
d. DSW-A2: 10.6.0.3

    DSW-A1:
    <img width="1007" height="335" alt="image" src="https://github.com/user-attachments/assets/24f149b1-052e-4c36-a5dc-f61349eead8b" />

    DSW-A2:
    <img width="1009" height="405" alt="image" src="https://github.com/user-attachments/assets/5c213f1d-948b-4a5e-a3ff-985c0a133196" />


    
16. Configure HSRPv2 group 1 for Office B’s Management subnet (VLAN 99). Make DSW-B1 the Active router by increasing its priority to 5 above the default, and enable preemption on DSW-B1.
a. Subnet: 10.0.0.16/28
b. VIP: 10.0.0.17
c. DSW-B1: 10.0.0.18
d. DSW-B2: 10.0.0.19

    DSW-B1:
    ```
    interface vlan 99
    ip add 10.0.0.18 255.255.255.240
    standby ver 2
    standby 1 ip 10.0.0.17
    standby 1 priority 105
    standby 1 preempt
    ```


    DSW-B2:
    ```
    interface vlan 99
    ip add 10.0.0.19 255.255.255.240
    standby ver 2
    standby 1 ip 10.0.0.17
    ```
    
18. Configure HSRPv2 group 2 for Office B’s PCs subnet (VLAN 10). Make DSW-B1 the Active router by increasing its priority to 5 above the default, and enable preemption on DSW-B1.
a. Subnet: 10.3.0.0/24
b. VIP: 10.3.0.1
c. DSW-B1: 10.3.0.2
d. DSW-B2: 10.3.0.3

    DSW-B1:
    ```
    interface vlan 10
    ip add 10.3.0.2 255.255.255.0
    standby ver 2
    standby 2 ip 10.3.0.1
    standby 2 priority 105
    standby 2 preempt
    ```

    DSW-B2:
    ```
    int vlan 10
    ip add 10.3.0.3 255.255.255.0
    standby ver 2
    standby 2 ip 10.3.0.1
    ```

    
19. Configure HSRPv2 group 3 for Office B’s Phones subnet (VLAN 20). Make DSW-B2 the Active router by increasing its priority to 5 above the default, and enable preemption on DSW-B2.
a. Subnet: 10.4.0.0/24
b. VIP: 10.4.0.1
c. DSW-B1: 10.4.0.2
d. DSW-B2: 10.4.0.3

    DSW-B1:
    ```
    interface vlan 20
    ip add 10.4.0.2 255.255.255.0
    standby ver 2
    standby 3 ip 10.4.0.1
    ```

    DSW-B2:
    ```
    interface vlan 20
    ip add 10.4.0.3 255.255.255.0
    standby ver 2
    standby 3 ip 10.4.0.1
    standby 3 priority 105
    standby 3 preempt
    ```

    
21. Configure HSRPv2 group 4 for Office B’s Servers subnet (VLAN 30). Make DSW-B2 the Active router by increasing its priority to 5 above the default, and enable preemption on DSW-B2.
a. Subnet: 10.5.0.0/24
b. VIP: 10.5.0.1
c. DSW-B1: 10.5.0.2
d. DSW-B2: 10.5.0.3

    DSW-B1:
    ```
    interface vlan 30
    ip add 10.5.0.2 255.255.255.0
    standby ver 2
    standby 4 ip 10.5.0.1
    ```


    DSW-B2:
    ```
    interface vlan 30
    ip add 10.5.0.3 255.255.255.0
    standby ver 2
    standby 4 ip 10.5.0.1
    standby 4 priority 105
    standby 4 preempt
    ```

    

#### PART 4 - Rapid Spanning Tree Protocol
1. Configure Rapid PVST+ on all Access and Distribution switches.
a. Ensure that the Root Bridge for each VLAN aligns with the HSRP Active router by configuring the lowest possible STP priority.
b. Configure the HSRP Standby Router for each VLAN with an STP priority one increment above the lowest priority.

    This task aims to teach you the principles of Symmetric Network Design, which ensures traffic flows via the shortest and most accurate path while preventing loops in your network. By using Rapid PVST+ instead of the slowness of traditional STP, we enable network convergence within seconds after interruptions; and by setting the STP priority of the device you designated as HSRP Active (main router) in the previous task to the lowest possible value (0), we also declare it as the Root Bridge of the network, preventing unnecessary suboptimal routing. Furthermore, by setting the priority of the HSRP Standby device one full step up (4096) as per the structural rule of STP, we guarantee that both the routing task and the Root Bridge role are seamlessly transferred to the standby device in the event of a main device failure.

    DSW-A1: <br>
    <img width="635" height="146" alt="image" src="https://github.com/user-attachments/assets/a995858a-3f00-40fa-a757-2394ff05a3c0" />

    do the same process with all other switches.

    DSW-A1:<br>
   <img width="993" height="639" alt="image" src="https://github.com/user-attachments/assets/4bb73126-b0ec-46a6-bbe8-6857dcc8de1f" />
   <img width="739" height="166" alt="image" src="https://github.com/user-attachments/assets/f7d6d3ba-534b-4e6a-bcf6-a1ce2b4227c7" />

    DSW-A2:<br>
   <img width="730" height="268" alt="image" src="https://github.com/user-attachments/assets/fa1a840e-2be6-4f90-800c-dcb0cc43b7fe" />

   DSW-B1:<br>
   <img width="747" height="242" alt="image" src="https://github.com/user-attachments/assets/d776ab8f-f2e8-4c9a-84b4-4c816a5f81da" />

   DSW-B2: <br>
   <img width="722" height="249" alt="image" src="https://github.com/user-attachments/assets/2e6e8eb1-b966-4dbd-8680-3cd06dc6acbf" />





2. Enable PortFast and BPDU Guard on all ports connected to end hosts (including WLC1). Perform the configurations in interface config mode.

    This task aims to maximize the network connection speed of end-user devices (PCs, IP phones, WLCs) while preventing configuration risks from endpoints at the hardware level. By activating PortFast on ports going to end-users, we bypass the 30-second waiting time (listening/learning) that STP normally spends searching for loops, allowing devices to instantly obtain IP addresses and connect to the network; however, to prevent the risks posed by this open door, we activate the BPDU Guard shield on the same ports. Thus, if an employee in the office connects an unauthorized switch to a wall socket and this device sends an STP message (BPDU) to the network, your main switch will immediately detect this situation and lock (err-disable) the port for security purposes, preventing the network from collapsing due to a broadcast storm.

   
    ASW-A1:
    The ASW-A1's f0/2 interface is connected to WLC1, which is a trunk port. By default, portfast does not work on interfaces in trunk mode. We can see this from the information provided. Here, we add an extra command. <br>
    <img width="1018" height="1020" alt="image" src="https://github.com/user-attachments/assets/9b43b910-0b05-4e86-a3f9-b18a3bcb7c2f" />

    other access switches similar configuration:

   ASW-A2, ASW-A3, ASW-B1, ASW-B2, ASW-B3:
   ```
    int f0/1
    spanning-tree portfast
    spanning-tree bpduguard enable
   ```
  

#### PART 5 - Static and Dynamic Routing

1. Configure OSPF on R1 (LAN-facing interfaces) and all Core and Distribution switches (all Layer-3 interfaces).
a. Use process ID 1 and Area 0.
b. Manually configure each device’s RID to match the loopback interface IP.
c. On switches, use the network command to match the exact IP address of each interface.
d. On R1, enable OSPF in interface config mode.
e. Make sure OSPF is enabled on all loopback interfaces, too. Loopback interfaces should be passive.
f. Each Distribution switch’s SVIs (except the Management VLAN SVI) should be passive, too.
g. Configure all physical connections between OSPF neighbors to use a network type that doesn’t elect a DR/BDR. NOTE: This doesn’t work on the Layer-3 PortChannel interfaces between CSW1/CSW2. Leave them as the default network type.

    This task teaches how to establish automatic network sharing between core switches, distribution switches, and edge routers (R1) over OSPF (Area 0), the standard dynamic routing protocol in enterprise networks, using best practice standards. We practice manually fixing Router ID (RID) values ​​using never-closing virtual interfaces (loopbacks) to improve network stability, employing both traditional network command-based exact matching and modern in-interface configuration methods. We also learn how to make VLANs and loopbacks containing end users passive interfaces, reducing unnecessary CPU load and the risk of route injection by cyber attackers; how to eliminate time-consuming DR/BDR selections by switching physical lines between neighboring devices to point-to-point network types; and how to ensure lightning-fast network convergence in the event of any disruption.

    R1:
   <img width="812" height="409" alt="image" src="https://github.com/user-attachments/assets/414894c5-20bc-4c6a-9d85-0613ed70987d" />

   CSW1:
   <img width="1761" height="875" alt="image" src="https://github.com/user-attachments/assets/e528da5a-e69e-4514-9451-2be6c5dbded2" />

   CSW2:
   <img width="1115" height="482" alt="image" src="https://github.com/user-attachments/assets/28bd05ad-e414-4a6f-90dd-4548cbd57c98" />

   DSW-A1:
   <img width="1100" height="541" alt="image" src="https://github.com/user-attachments/assets/7fd421fa-c594-449e-b3dd-16eb5733ddeb" />

   DSW-A2:
   <img width="1111" height="572" alt="image" src="https://github.com/user-attachments/assets/251310f1-9b07-4c4b-b880-001962d6c9e7" />

   DSW-B1:
   <img width="1094" height="565" alt="image" src="https://github.com/user-attachments/assets/c1a78438-277e-44b1-a911-e48a443a8217" />

   DSW-B2:
   <img width="1100" height="663" alt="image" src="https://github.com/user-attachments/assets/6ffb1b7b-7567-49f0-92bc-8d74dcaec063" />


2. Configure one static default route for each of R1’s Internet connections. They should be recursive routes.
a. Make the route via G0/1/0 a floating static route by configuring an AD value 1 greater than the default.
b. R1 should function as an OSPF ASBR, advertising its default route to other routers in the OSPF domain.

    This task aims to teach the concepts of Static Default Route and dynamic routing integration, which enable the company to access the internet securely, redundantly, and automatically. We will learn to write "recursive" routes on R1 targeting the telecommunications provider's IP (Next-Hop) so that devices on the internal network can find the outside world, and to create a Floating Static Route by increasing the reliability value of the backup line (Administrative Distance) above the default to 1 to create an automatic redundancy (failover) scenario in case of a main internet line failure. Finally, we will position R1 as an ASBR (Autonomous System Border Router) and reinforce the centralized management logic by automatically announcing (injecting) this internet exit route to all switches on the internal network with a single command via OSPF, eliminating the need to write static routes for each device individually.

  2a:
  <img width="1054" height="662" alt="image" src="https://github.com/user-attachments/assets/09caf8fa-2133-41d7-9c53-03a4c2974ecd" />

  2b:
  <img width="668" height="150" alt="image" src="https://github.com/user-attachments/assets/7fb0565a-ae08-44bf-abf5-e0b7bc9b3599" />

  now we can verify on any switch.
  
  verify on DSW-B2:
  <img width="952" height="200" alt="image" src="https://github.com/user-attachments/assets/74cc469b-384f-4dc6-af0d-53be330f7284" />



#### PART 6 - Network Services: DHCP, DNS, NTP, SNMP, Syslog, FTP, SSH, NAT

1. Configure the following DHCP pools on R1 to make it serve as the DHCP server for hosts in Offices A and B. Exclude the first ten usable host addresses of each pool; they must not be leased to DHCP clients.
a. Pool: A-Mgmt
i. Subnet: 10.0.0.0/28
ii. Default gateway: 10.0.0.1
iii. Domain name: jeremysitlab.com
iv. DNS server: 10.5.0.4 (SRV1)
v. WLC: 10.0.0.7
b. Pool: A-PC
i. Subnet: 10.1.0.0/24
ii. Default gateway: 10.1.0.1
iii. Domain name: jeremysitlab.com
iv. DNS server: 10.5.0.4 (SRV1)
c. Pool: A-Phone
i. Subnet: 10.2.0.0/24
ii. Default gateway: 10.2.0.1
iii. Domain name: jeremysitlab.com
iv. DNS server: 10.5.0.4 (SRV1)
d. Pool: B-Mgmt
i. Subnet: 10.0.0.16/28
ii. Default gateway: 10.0.0.17
iii. Domain name: jeremysitlab.com
iv. DNS server: 10.5.0.4 (SRV1)
v. WLC: 10.0.0.7
e. Pool: B-PC
i. Subnet: 10.3.0.0/24
ii. Default gateway: 10.3.0.1
iii. Domain name: jeremysitlab.com
iv. DNS server: 10.5.0.4 (SRV1)
f. Pool: B-Phone
i. Subnet: 10.4.0.0/24
ii. Default gateway: 10.4.0.1
iii. Domain name: jeremysitlab.com
iv. DNS server: 10.5.0.4 (SRV1)
g. Pool: Wi-Fi
i. Subnet: 10.6.0.0/24
ii. Default gateway: 10.6.0.1
iii. Domain name: jeremysitlab.com
iv. DNS server: 10.5.0.4 (SRV1)

R1:
<img width="884" height="383" alt="image" src="https://github.com/user-attachments/assets/731607c5-0888-41ed-bdf2-8da98a6ec721" />
<img width="698" height="863" alt="image" src="https://github.com/user-attachments/assets/b4472600-2d01-401a-a27f-ccd9007230c6" />


2. Configure the Distribution switches to relay wired DHCP clients’ broadcast messages to R1’s Loopback0 IP address.
   
   DSW-A1, DSW-A2:
   ```
    int vlan 10
    ip helper-address 10.0.0.76
    int vlan 20
    ip helper-address 10.0.0.76
    int vlan 40
    ip helper-address 10.0.0.76
    int vlan 99
    ip helper-address 10.0.0.76
   ```

   DSW-B1, DSW-B2:
   ```
    int vlan 10
    ip helper-address 10.0.0.76
    int vlan 20
    ip helper-address 10.0.0.76
    int vlan 30
    ip helper-address 10.0.0.76
    int vlan 99
    ip helper-address 10.0.0.76
   ```

   verify on PC1:
   <img width="791" height="547" alt="image" src="https://github.com/user-attachments/assets/6fafd1a3-0158-41b2-bc85-f2c5cd8ab2c8" />
   <img width="888" height="370" alt="image" src="https://github.com/user-attachments/assets/d1340f85-94ec-4164-b6ec-9eae371e3b31" />

   



3. Configure the following DNS entries on SRV1:
a. google.com = 172.253.62.100
b. youtube.com = 152.250.31.93
c. jeremysitlab.com = 66.235.200.145
d. www.jeremysitlab.com = jeremysitlab.com

    SVR1:
   <img width="1144" height="975" alt="image" src="https://github.com/user-attachments/assets/65e445f6-1bd6-4bc1-b497-fa6cbdb82710" />

   verify on PC1:
   Even though we can't access it yet because NAT isn't configured, we can see that DNS is resolving the IP address.
   <img width="763" height="366" alt="image" src="https://github.com/user-attachments/assets/72eb54fd-4378-4015-9535-7649d92edae2" />

4. Configure all routers and switches to use domain name jeremysitlab.com and use SRV1 as their DNS server.

   All Devices:
   ```
    ip domain name jeremysitlab.com
    ip name-server 10.5.0.4
   ```
   
5. Configure NTP on R1:
a. Make R1 a stratum 5 NTP server.
b. R1 should learn the time from NTP server 216.239.35.0.
c. NOTE: NTP takes a LONG time to sync, especially in Packet Tracer. After making the configurations, you can move on – don’t wait for the devices to sync.

R1:
<img width="785" height="103" alt="image" src="https://github.com/user-attachments/assets/87e0891a-1f5c-4fda-940b-c82e0acce393" />


6. All Core, Distribution, and Access switches should use R1’s loopback interface as their NTP server.
a. Clients should authenticate R1 using key number 1 and the password ccna.
    R1:
      <img width="862" height="203" alt="image" src="https://github.com/user-attachments/assets/f00f96af-df20-495c-9e6c-a47edd18d709" />

    all switches:
   ```
      ntp authentication-key 1 md5 ccna
      ntp trusted-key 1 
      ntp server 10.0.0.76 key 1
   ```

7. Configure the SNMP community string SNMPSTRING on all routers and switches. The string should allow GET messages, but not SET messages.
   all devices:
   ```
    snmp-server community SNMPSTRING ro
   ```
8. Configure Syslog on all routers and switches:
a. Send Syslog messages to SRV1. Messages of all severity levels should be logged.
b. Enable logging to the buffer. Reserve 8192 bytes of memory for the buffer.

  all devices:
  ```
    logging 10.5.0.4
    logging trap debugging
    logging buffered 8192
  ```

9. Use FTP on R1 to download a new IOS version from SRV1:
a. Configure R1’s default FTP credentials: username cisco, password cisco.
b. Use FTP to copy the file c2900-universalk9-mz.SPA.155-3.M4a.bin from SRV1 to R1’s flash drive.
c. Reboot R1 using the new IOS file, and then delete the old one from flash.

    R1:
   <img width="1046" height="711" alt="image" src="https://github.com/user-attachments/assets/79f3a640-42f9-45e5-b768-b53585f6aa35" />

    Deleting old flash:
   <img width="962" height="680" alt="image" src="https://github.com/user-attachments/assets/10ce6938-958e-4e4a-a5fa-4e2f7b1f221d" />


10. Configure SSH for secure remote access on all routers and switches.
a. Use the largest modulus size for the RSA keys.
b. Allow SSHv2 connections only.
c. Create standard ACL 1, only allowing packets sourced from Office A’s PCs subnet. Apply the ACL to all VTY lines to restrict SSH access.
d. Allow only SSH connections to the VTY lines.
e. Require users to log in with a local user account when connecting via SSH.
f. Configure synchronous logging on the VTY lines.

    All devices:
    ```
      crypto key generate rsa
      4096
      ip ssh version 2
      access-list 1 permit 10.1.0.0 0.0.0.255
      line vty 0 15
      access-class 1 in
      transport input ssh
      login local
      logging synchronous
    ```

    Verify on PC1:
    <img width="543" height="194" alt="image" src="https://github.com/user-attachments/assets/fe1f35ce-c5af-4516-bcfb-bc3109ad3f1a" />

    Verify on PC3:
    <img width="727" height="292" alt="image" src="https://github.com/user-attachments/assets/e9dfd633-d048-477f-8879-637dca9539bb" />


12. Configure static NAT on R1 to enable hosts on the Internet to access SRV1 via the IP address 203.0.113.113.

    R1:
    <img width="967" height="314" alt="image" src="https://github.com/user-attachments/assets/4d3f7e7f-9cf9-4166-b6f9-d094381c967d" />

    verify:
    before ping, do dns server address "10.5.0.4" from config tab. 
    <img width="1002" height="568" alt="image" src="https://github.com/user-attachments/assets/d593cabb-fcd0-4bec-b3db-ee460793fce3" />


13. Configure pool-based dynamic PAT on R1 to enable hosts in the Office A PCs, Office A Phones, Office B PCs, Office B Phones, and Wi-Fi subnets to access the Internet.
a. Use standard ACL 2 to define the appropriate inside local address ranges in the following order:
i. Office A PCs: 10.1.0.0/24
ii. Office A Phones: 10.2.0.0/24
iii. Office B PCs: 10.3.0.0/24
iv. Office B Phones: 10.4.0.0/24
v. Wi-Fi: 10.6.0.0/24
b. Define a range of inside global addresses called POOL1, specifying the range 203.0.113.200 to 203.0.113.207 with a /29 netmask.
c. Map ACL 2 to POOL1 and enable PAT. Confirm that hosts can access the Internet by pinging jeremysitlab.com.
d. Verify that Internet link failover works by disabling R1’s G0/0/0 interface and pinging again.
i. You will need to remove and re-configure the OSPF default-information originate command for this to work. In real Cisco routers, you can configure the default-information originate always command that supports failover like this, but the command isn’t available in Packet Tracer.
ii. Re-enable G0/0/0 (and remove and re-configure default-information originate once again).

12a:
<img width="840" height="176" alt="image" src="https://github.com/user-attachments/assets/27910f12-3e90-4ea9-b279-87ea8d8ab27f" />

12b:
<img width="1021" height="83" alt="image" src="https://github.com/user-attachments/assets/98c57649-969e-4fad-b7a9-22f1b4232b6f" />

12c:
<img width="887" height="73" alt="image" src="https://github.com/user-attachments/assets/b90445e4-dfc1-4381-93c5-feb1c8a2546c" />

verify on PC1:
<img width="838" height="374" alt="image" src="https://github.com/user-attachments/assets/d5b305e6-ef44-437c-80bf-50a80cac6bf2" />

12d:
<img width="1071" height="417" alt="image" src="https://github.com/user-attachments/assets/ce79a6b5-d6f7-4265-9177-e11bb501a563" />




13. Disable CDP on all devices and enable LLDP instead. 
a. Disable LLDP Tx on each Access switch’s access port (F0/1).

R1, CORE and DISTRIBUTION layer switches:
```
  no cdp run
  lldp run
```

Access layer switches:
```
  no cdp run
  lldp run
  int f0/1
  no lldp transmit
```
  


#### PART 7 - Security: ACLs and Layer-2 Security Features
1. Configure extended ACL OfficeA_to_OfficeB where appropriate:
a. Allow ICMP messages from the Office A PCs subnet to the Office B PCs subnet.
b. Block all other traffic from the Office A PCs subnet to the Office B PCs subnet.
c. Allow all other traffic.
d. Apply the ACL according to general best practice for extended ACLs.
2. Configure Port Security on each Access switch's F0/1 port:
a. Allow the minimum necessary number of MAC addresses on each port.
i. SRV1 does not use virtualization, so it uses a single MAC address.
b. Configure a violation mode that blocks invalid traffic without affecting valid traffic. The switches should send notifications when invalid traffic is detected.
c. Switches should automatically save the secure MAC addresses they learn to the running-config.
3. Configure DHCP Snooping on all Access switches.
a. Enable it for all active VLANs in each LAN.
b. Trust the appropriate ports.
c. Disable insertion of DHCP Option 82.
d. Set a DHCP rate limit of 15 pps on active untrusted ports.
e. Set a higher limit (100 pps) on ASW-A1’s connection to WLC1.
4. Configure DAI on all Access switches.
a. Enable it for all active VLANs in each LAN.
b. Trust the appropriate ports.
c. Enable all optional validation checks.

