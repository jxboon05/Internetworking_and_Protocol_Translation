# Advanced Internetworking: DHCP Relay & NAT-PT Protocol Translation

A comprehensive network engineering project focused on multi-protocol routing, enterprise DHCP deployment via cross-subnet relays, and bidirectional IPv4-to-IPv6 header translation (NAT-PT). Implemented using Cisco Packet Tracer and GNS3 with QEMU Microcore Linux nodes and automated Wireshark data capture.

## 🛠 Technologies & Tools Used
* **Network Emulators:** GNS3 (v0.8.3.1), Cisco Packet Tracer
* **Traffic Analysis:** Wireshark Packet Analyzer
* **Operating Systems:** QEMU Microcore Linux, Cisco IOS (c3745)
* **Core Protocols:** NAT64 / NAT-PT, PAT (NAT Overload), DHCP Relay, ICMP, ICMPv6

---

## 📐 Project Architecture

### Part 1: Cross-Subnet DHCP Relay & PAT Validation
* **Objective:** Establish a secure enterprise edge where multiple internal private endpoints automatically obtain dynamic IP assignments from a centralized DHCP architecture hosted across an offsite branch.

![Network Topology](topology_diagram1.png)

* **Key Implementation Details:**
  * Configured **Port Address Translation (PAT / NAT Overload)** allowing internal subnets (`192.168.1.0/24` and `192.168.2.0/24`) to safely route out via shared public IP interfaces.
  * Deployed Cisco **DHCP Relay Agents (`ip helper-address`)** on the local gateway to convert layer 2 configuration broadcasts into layer 3 unicast streams, mapping data securely to the remote server.
  * Included analysis of asymmetric NAT translation behaviors causing ICMP session mapping limits during direct cross-branch boundary pings.

### Part 2: Dual-Stack Infrastructure via Static NAT-PT
* **Objective:** Enable seamless, transparent bidirectional communication between an legacy IPv4-only Local Area Network and a modern IPv6-only infrastructure.

![Network Topology](topology_diagram2.png)

* **Addressing Matrix & Fixed Translations:**
  * **IPv4 Subnet:** `192.168.1.0/24` (Gateway: `192.168.1.254`)
  * **IPv6 Subnet:** `2000::/64` (Gateway: `2000::1000`)
  * **NAT64 Bridging Prefix:** `2001::/96` used to algorithmically embed native IPv4 data fields into IPv6 frame transits.

| Source Host | Native Address | Target Translated Mapping | Destination Network |
| :--- | :--- | :--- | :--- |
| **PC1 (IPv4)** | `192.168.1.1` | `2001::1` | IPv6 LAN |
| **PC2 (IPv4)** | `192.168.1.2` | `2001::2` | IPv6 LAN |
| **PC3 (IPv6)** | `2000::1` | `192.168.2.1` | IPv4 LAN |
| **PC4 (IPv6)** | `2000::2` | `192.168.2.2` | IPv4 LAN |

---

## 🔍 Wireshark Header Verification & Evidence

### 1. IPv4 Local Segment Capture (PC1 ↔ R1)
Before reaching the translation engine on the router, frames traversing the local access link segment remain entirely inside native IPv4 layers:
* **Frame Type:** `Internet Protocol Version 4`
* **Source:** `192.168.1.1` | **Destination:** `192.168.2.1` (Mapped Representation of PC3)

### 2. IPv6 Core Segment Capture (R1 ↔ PC3)
Upon entering the NAT64 translation boundary, headers are completely stripped, converted, and restructured to traverse the destination media:
* **Frame Type:** `Internet Protocol Version 6`
* **Source:** `2001::1` (Mapped Representation of PC1) | **Destination:** `2000::1`

---

## 💻 Endpoint Node Configurations

### Network Scenario 1: DHCP Clients
[cite_start]Endpoints in this branch were configured to use dynamic addressing[cite: 7, 276]. [cite_start]Using the **DORA process**, they broadcast discovery frames to locate an operational assignment server across the gateway boundaries[cite: 90].

* [cite_start]**PC0, PC1, PC2 addressing:** Dynamic (DHCP Assigned) [cite: 7, 14, 88]
* [cite_start]**Assigned Subnet Mask:** `255.255.255.0` [cite: 14, 66]
* [cite_start]**Default Gateway:** `192.168.1.254` [cite: 14, 66]

---

### Network Scenario 2: Dual-Stack Endpoints (GNS3)
[cite_start]The QEMU Microcore Linux nodes were configured using native shell commands to establish static IP paths toward their respective interfaces on Router R1[cite: 116, 118, 131].

| Device Node | IP Protocol | Static IP Address | Subnet / Prefix | Default Gateway |
| :--- | :--- | :--- | :--- | :--- |
| **PC1** | IPv4 | `192.168.1.1` | `/24` | `192.168.1.254` |
| **PC2** | IPv4 | `192.168.1.2` | `/24` | `192.168.1.254` |
| **PC3** | IPv6 | `2000::1` | `/64` | `2000::1000` |
| **PC4** | IPv6 | `2000::2` | `/64` | `2000::1000` |

#### Linux Terminal Configuration Reference (QEMU Nodes)
[cite_start]To document your hands-on Linux administration skills, the endpoints were brought online using the following standard shell parameters[cite: 118]:

* **IPv4 Nodes (PC1/PC2):**
  ```bash
  sudo ifconfig eth0 192.168.1.1 netmask 255.255.255.0 up
  sudo route add default gw 192.168.1.254
