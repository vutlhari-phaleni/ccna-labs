# CCNA & IT Support Labs Portfolio

A collection of Cisco Packet Tracer labs and IT support simulations documenting my journey toward the CCNA certification and my first IT role. Each lab includes the project file, a topology diagram, and a README covering what was configured, what broke, and what I learned.

**Author:** Vutlhari Samuel Phaleni
**Focus:** CCNA (Cisco Certified Network Associate) & IT Support
**Tool:** Cisco Packet Tracer, Jira Service Management

---

## Lab Index

| # | Lab | Topics Covered |
|---|-----|----------------|
| 01 | [Multi-Area OSPF Network](./01-ospf-multi-area) | OSPF, redundancy, load balancing, DHCP, SSH |
| 02 | [VLAN, EtherChannel, Port Security](./02-vlan-etherchannel-portsecurity) | VTP, LACP EtherChannel, STP, Port Security, Layer 3 switching |
| 03 | [NAT and ACL](./03-nat-acl) | NAT Overload (PAT), standard ACLs, extended ACLs |
| 04 | [VLAN and Inter-VLAN Routing](./04-vlan-inter-vlan-routing) | VLANs, 802.1Q trunking, inter-VLAN routing |
| 05 | [Two-LAN Routing](./05-two-lan-routing) | Basic routing, directly connected networks, IP addressing |
| 06 | [IPv6 Dual-Stack Routing](./06-ipv6-dual-stack) | IPv6 addressing, dual-stack, IPv6 static routing |
| 07 | [EIGRP Dynamic Routing](./07-eigrp-routing) | EIGRP, AS configuration, neighbor adjacency, topology table |
| 08 | [Small Office Helpdesk Network](./08-helpdesk-office-network) | DHCP, Wireless Router, Inter-network routing |
| 09 | [IT Service Management (Jira)](./09-jira-service-management) | Ticketing, ITIL workflows, root cause analysis, helpdesk documentation |

---

## Skill Coverage

### Routing
- OSPF (multi-router, multi-area, load balancing)
- EIGRP (AS numbers, composite metrics)
- Static routing
- IPv6 addressing and static routing
- Inter-VLAN routing (traditional + Layer 3 switching)

### Switching
- VLANs and access ports
- 802.1Q trunking
- VTP (server/client)
- EtherChannel (LACP)
- Spanning Tree Protocol (STP)
- Port Security (sticky MAC, violation modes)
- Layer 3 switching with SVIs

### Services & Operations
- DHCP (pools, excluded addresses)
- SSH v2 (RSA keys, VTY lines, local authentication)
- NAT Overload (PAT)
- **Jira Service Management (Ticketing, Incident Workflows)**

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
- `*.pkt` or `.pdf` — Project file or practice record

---

## Purpose

This repository is a working portfolio of hands-on networking and IT support practice. Each lab was built from scratch, debugged when things broke, and documented honestly. The "What Broke" sections exist specifically to record real troubleshooting — not just the final config.

---

## Related Links

- [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer)
- [Cisco CCNA Certification](https://www.cisco.com/c/en/us/training-events/training-certifications/certifications/associate/ccna.html)