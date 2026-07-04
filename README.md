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
[cite_start]Establish a secure enterprise edge where multiple internal private endpoints automatically obtain dynamic IP assignments from a centralized DHCP architecture hosted across an offsite branch[cite: 271, 276].

### 2. Key Implementation Details
* [cite_start]Configured **Port Address Translation (PAT / NAT Overload)** allowing internal subnets (`192.168.1.0/24` and `192.168.2.0/24`) to safely route out via shared public IP interfaces[cite: 17, 273].
* [cite_start]Deployed Cisco **DHCP Relay Agents (`ip helper-address`)** on the local gateway to convert layer 2 configuration broadcasts into layer 3 unicast streams, mapping data securely to the remote server[cite: 53, 55].
* [cite_start]Included analysis of asymmetric NAT translation behaviors causing ICMP session mapping limits during direct cross-branch boundary pings[cite: 103, 108].

### 3. Endpoint Node Configurations
[cite_start]Endpoints in this branch were configured to use dynamic addressing[cite: 7, 276]. [cite_start]Using the **DORA process**, they broadcast discovery frames to locate an operational assignment server across the gateway boundaries[cite: 90].

* [cite_start]**PC0, PC1, PC2 Addressing:** Dynamic (DHCP Assigned) [cite: 7, 14, 88]
* [cite_start]**Assigned Subnet Mask:** `255.255.255.0` [cite: 14, 66]
* [cite_start]**Default Gateway:** `192.168.1.254` [cite: 14, 66]

---

## 🚀 PART 2: GNS3 Dual-Stack Infrastructure
### Core Protocol Translation via Static NAT-PT

### 1. Objective
[cite_start]Enable seamless, transparent bidirectional communication between a legacy IPv4-only Local Area Network and a modern IPv6-only infrastructure[cite: 131, 134].

![Network Topology](assets/topology_diagram.png)

### 2. Addressing Matrix & Fixed Translations
* [cite_start]**IPv4 Subnet:** `192.168.1.0/24` (Gateway: `192.168.1.254`) [cite: 126, 130]
* [cite_start]**IPv6 Subnet:** `2000::/64` (Gateway: `2000::1000`) [cite: 127, 130]
* [cite_start]**NAT64 Bridging Prefix:** `2001::/96` used to algorithmically embed native IPv4 data fields into IPv6 frame transits[cite: 152, 153].

| Source Host | Native Address | Target Translated Mapping | Destination Network |
| :--- | :--- | :--- | :--- |
| **PC1 (IPv4)** | [cite_start]`192.168.1.1` [cite: 126] | [cite_start]`2001::1` [cite: 155] | IPv6 LAN |
| **PC2 (IPv4)** | [cite_start]`192.168.1.2` [cite: 126] | [cite_start]`2001::2` [cite: 155] | IPv6 LAN |
| **PC3 (IPv6)** | [cite_start]`2000::1` [cite: 127] | [cite_start]`192.168.2.1` [cite: 158] | IPv4 LAN |
| **PC4 (IPv6)** | [cite_start]`2000::2` [cite: 127] | [cite_start]`192.168.2.2` [cite: 158] | IPv4 LAN |

### 3. Endpoint Node Configurations
[cite_start]The QEMU Microcore Linux nodes were configured using native shell commands to establish static IP paths toward their respective interfaces on Router R1[cite: 116, 118, 131].

#### Linux Terminal Configuration Reference (QEMU Nodes)
[cite_start]To document operational hands-on Linux system administration skills, endpoints were brought online using the following standard shell parameters[cite: 118]:

* **IPv4 Nodes (PC1 & PC2 examples):**
  ```bash
  # PC1 configuration syntax
  sudo ifconfig eth0 192.168.1.1 netmask 255.255.255.0 up
  sudo route add default gw 192.168.1.254
