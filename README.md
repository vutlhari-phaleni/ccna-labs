# CCNA Labs Portfolio

A collection of Cisco Packet Tracer labs documenting my CCNA learning journey. Each lab includes the Packet Tracer project file, a topology diagram, and a README covering what was configured, what broke, and what I learned.

**Author:** Vutlhari Samuel Phaleni
**Focus:** CCNA (Cisco Certified Network Associate)
**Tool:** Cisco Packet Tracer 9.x

---

## Lab Index

| # | Lab | Topics Covered |
|---|-----|----------------|
| 01 | [Multi-Area OSPF Network](./01-ospf-multi-area) | OSPF, redundancy, load balancing, DHCP, SSH |
| 02 | [VLAN, EtherChannel, Port Security](./02-vlan-etherchannel-portsecurity) | VTP, LACP EtherChannel, STP, Port Security, Layer 3 switching |
| 03 | [NAT and ACL](./03-nat-acl) | NAT Overload (PAT), standard ACLs, extended ACLs |
| 04 | [VLAN and Inter-VLAN Routing](./04-vlan-inter-vlan-routing) | VLANs, 802.1Q trunking, inter-VLAN routing |
| 05 | [Two-LAN Routing](./05-two-lan-routing) | Basic routing, directly connected networks, IP addressing |

---

## Skill Coverage

### Routing
- OSPF (multi-router, multi-area, load balancing)
- Static routing
- Inter-VLAN routing (traditional + Layer 3 switching)

### Switching
- VLANs and access ports
- 802.1Q trunking
- VTP (server/client)
- EtherChannel (LACP)
- Spanning Tree Protocol
- Port Security (sticky MAC, violation modes)
- Layer 3 switching with SVIs

### Services
- DHCP (pools, excluded addresses)
- SSH v2 (RSA keys, VTY lines, local authentication)
- NAT Overload (PAT)

### Security
- Standard ACLs
- Extended ACLs
- Port Security
- SSH hardening

---

## Repository Structure

Each lab folder contains:

- `README.md` — documentation (overview, config, verification, lessons learned)
- `topology.png` — network diagram screenshot
- `*.pkt` — Cisco Packet Tracer project file

---

## Purpose

This repository is a working portfolio of hands-on networking practice. Each lab was built from scratch, debugged when things broke, and documented honestly. The "What Broke" sections exist specifically to record real troubleshooting — not just the final config.

---

## Related Links

- [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer)
- [Cisco CCNA Certification](https://www.cisco.com/c/en/us/training-events/training-certifications/certifications/associate/ccna.html)