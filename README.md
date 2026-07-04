# Advanced Internetworking: DHCP Relay & NAT-PT Protocol Translation

A comprehensive network engineering project focused on multi-protocol routing, enterprise DHCP deployment via cross-subnet relays, and bidirectional IPv4-to-IPv6 header translation (NAT-PT). Implemented using Cisco Packet Tracer and GNS3 with QEMU Microcore Linux nodes and automated Wireshark data capture.

## 🛠 Technologies & Tools Used
* **Network Emulators:** GNS3 (v0.8.3.1), Cisco Packet Tracer
* **Traffic Analysis:** Wireshark Packet Analyzer
* **Operating Systems:** QEMU Microcore Linux, Cisco IOS (c3745 / c3700)
* **Core Protocols:** NAT64 / NAT-PT, PAT (NAT Overload), DHCP Relay, ICMP, ICMPv6

---

## 🌐 PART 1: Cisco Packet Tracer Infrastructure
### Cross-Subnet DHCP Relay & PAT Validation

### 1. Objective
Establish a secure enterprise edge where multiple internal private endpoints automatically obtain dynamic IP assignments from a centralized DHCP architecture hosted across an offsite branch.

### 2. Key Implementation Details
* Configured **Port Address Translation (PAT / NAT Overload)** allowing internal subnets (`192.168.1.0/24` and `192.168.2.0/24`) to safely route out via shared public IP interfaces.
* Deployed Cisco **DHCP Relay Agents (`ip helper-address`)** on the local gateway to convert layer 2 configuration broadcasts into layer 3 unicast streams, mapping data securely to the remote server.
* [cite_start]Included analysis of asymmetric NAT translation behaviors causing ICMP session mapping limits during direct cross-branch boundary pings.

### 3. Endpoint Node Configurations
Endpoints in this branch were configured to use dynamic addressing. Using the **DORA process**, they broadcast discovery frames to locate an operational assignment server across the gateway boundaries.

* **PC0, PC1, PC2 Addressing:** Dynamic (DHCP Assigned) 
* **Assigned Subnet Mask:** `255.255.255.0`
* **Default Gateway:** `192.168.1.254`

---

## 🚀 PART 2: GNS3 Dual-Stack Infrastructure
### Core Protocol Translation via Static NAT-PT

### 1. Objective
Enable seamless, transparent bidirectional communication between a legacy IPv4-only Local Area Network and a modern IPv6-only infrastructure.

![Network Topology](assets/topology_diagram.png)

### 2. Addressing Matrix & Fixed Translations
* **IPv4 Subnet:** `192.168.1.0/24` (Gateway: `192.168.1.254`)
* **IPv6 Subnet:** `2000::/64` (Gateway: `2000::1000`)
* **NAT64 Bridging Prefix:** `2001::/96` used to algorithmically embed native IPv4 data fields into IPv6 frame transits.

| Source Host | Native Address | Target Translated Mapping | Destination Network |
| :--- | :--- | :--- | :--- |
| **PC1 (IPv4)** | `192.168.1.1` | `2001::1` | IPv6 LAN |
| **PC2 (IPv4)** | `192.168.1.2` | `2001::2` | IPv6 LAN |
| **PC3 (IPv6)** | `2000::1` | `192.168.2.1` | IPv4 LAN |
| **PC4 (IPv6)** | `2000::2` | `192.168.2.2` | IPv4 LAN |

### 3. Endpoint Node Configurations
[cite_start]The QEMU Microcore Linux nodes were configured using native shell commands to establish static IP paths toward their respective interfaces on Router R1.

#### Linux Terminal Configuration Reference (QEMU Nodes)
[cite_start]To document operational hands-on Linux system administration skills, endpoints were brought online using the following standard shell parameters:

* **IPv4 Nodes (PC1 & PC2 examples):**
  ```bash
  # PC1 configuration syntax
  sudo ifconfig eth0 192.168.1.1 netmask 255.255.255.0 up
  sudo route add default gw 192.168.1.254

## ⚙️ Core Router Configurations

### R1 NAT-PT Engine Script
```cisco
ipv6 unicast-routing
!
interface FastEthernet0/0
 ip address 192.168.1.254 255.255.255.0
 ipv6 nat
 no shutdown
!
interface FastEthernet0/1
 ipv6 address 2000::1000/64
 ipv6 nat
 no shutdown
!
ipv6 nat prefix 2001::/96
ipv6 nat v4v6 source 192.168.1.1 2001::1
ipv6 nat v4v6 source 192.168.1.2 2001::2
ipv6 nat v6v4 source 2000::1 192.168.2.1
ipv6 nat v6v4 source 2000::2 192.168.2.2
