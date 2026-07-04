# Advanced Internetworking: DHCP Relay & NAT-PT Protocol Translation

A networking project demonstrating enterprise DHCP Relay deployment, Port Address Translation (PAT), and IPv4-to-IPv6 protocol translation (NAT-PT). The project was implemented using **Cisco Packet Tracer** and **GNS3** to simulate real-world enterprise networking environments.

---

## Project Overview

This project consists of two major networking implementations:

- **Part 1:** Enterprise DHCP Relay and PAT configuration using Cisco Packet Tracer.
- **Part 2:** IPv4-to-IPv6 protocol translation using NAT-PT in GNS3 with QEMU Microcore Linux hosts.

The project demonstrates practical experience in enterprise routing, dynamic IP assignment, IPv4/IPv6 interoperability, Linux networking, and packet analysis using Wireshark.

---

## Technologies & Tools

### Network Simulation
- Cisco Packet Tracer
- GNS3

### Network Operating Systems
- Cisco IOS
- QEMU Microcore Linux

### Network Protocols
- DHCP Relay (`ip helper-address`)
- Port Address Translation (PAT / NAT Overload)
- NAT-PT (IPv4 ↔ IPv6 Translation)
- ICMP / ICMPv6
- Static Routing

### Packet Analysis
- Wireshark

---

# Part 1 — Cisco Packet Tracer

## Objective

Design and implement an enterprise network where multiple private subnets obtain dynamic IP addresses from a centralized DHCP server located on another network through DHCP Relay.

---

## Network Topology

![Cisco Packet Tracer Topology](assets/cisco_topology.png)

---

## Features Implemented

- DHCP Relay (`ip helper-address`)
- Dynamic IP allocation using DHCP
- Port Address Translation (PAT)
- Static routing
- Multi-router enterprise topology
- End-to-end connectivity validation

---

## Validation

Successfully verified:

- DHCP Discover → Offer → Request → Acknowledge (DORA Process)
- Automatic IP assignment from remote DHCP server
- Successful communication between internal networks
- Internet access through PAT
- Router forwarding using DHCP Relay

---

# Part 2 — GNS3

## Objective

Implement IPv4-to-IPv6 communication using NAT-PT to enable seamless communication between IPv4-only and IPv6-only networks.

---

## Network Topology

![GNS3 Topology](assets/gns3_topology.png)

---

## Features Implemented

- Static NAT-PT configuration
- IPv4 ↔ IPv6 protocol translation
- Dual-stack routing
- QEMU Linux host configuration
- Static IP addressing
- ICMP and ICMPv6 connectivity testing
- Wireshark packet capture and analysis

---

## Validation

Successfully verified:

- IPv4 host communication with IPv6 hosts
- IPv6 host communication with IPv4 hosts
- Static NAT translation mappings
- ICMP packet translation
- Successful packet forwarding across protocol boundaries
- Packet analysis using Wireshark

---

# Skills Demonstrated

- Enterprise Network Design
- Cisco Router Configuration
- DHCP Relay Deployment
- Port Address Translation (PAT)
- IPv4 and IPv6 Networking
- NAT-PT Protocol Translation
- Static Routing
- Linux Network Configuration
- Packet Analysis with Wireshark
- Network Troubleshooting

---

# Future Improvements

- Implement dynamic routing using OSPF or EIGRP
- Deploy DHCPv6
- Configure IPv6 ACLs
- Integrate DNS services
- Implement firewall security policies
- Add redundancy using HSRP or VRRP

---

# Author

**Boon Jia Xuan**

Bachelor of Computer Science (Honours)

Universiti Tunku Abdul Rahman (UTAR)
