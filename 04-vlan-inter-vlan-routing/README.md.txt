# VLAN and Inter-VLAN Routing Lab

## Overview
A two-switch, one-router LAN built in Cisco Packet Tracer. Demonstrates VLAN segmentation across multiple switches, 802.1Q trunking between switches, and inter-VLAN routing using separate physical router interfaces.

## Topology
![Topology](topology.png)

## Devices
- 1x Cisco Router (Router0)
- 2x Cisco 2960-24TT Switches (Switch0, Switch1)
- 4x PCs (PC0, PC1, PC2, PC3)

## VLAN and IP Design
| VLAN | Name | Network | Gateway | Devices |
|------|------|---------|---------|---------|
| 10 | HR | 192.168.10.0/24 | 192.168.10.1 | PC0, PC1 |
| 20 | IT | 192.168.20.0/24 | 192.168.20.1 | PC2, PC3 |

## Router Design
This lab uses **one physical router interface per VLAN** — a valid alternative to router-on-a-stick:

| Router Interface | IP Address | Connected To | VLAN |
|------------------|------------|--------------|------|
| Gi0/0/0 | 192.168.10.1/24 | Switch0 Gi0/1 | VLAN 10 |
| Gi0/0/1 | 192.168.20.1/24 | Switch1 Gi0/1 | VLAN 20 |

Each VLAN's gateway is a dedicated physical interface on the router — no subinterfaces required.

## Switch Port Assignments

### Switch0
| Port | Mode | VLAN |
|------|------|------|
| Fa0/1 | Access | 10 (PC0) |
| Fa0/2 | Access | 10 (PC1) |
| Gi0/1 | Access | 10 (to Router0 Gi0/0/0) |
| Fa0/24 | Trunk | 802.1Q to Switch1 |

### Switch1
| Port | Mode | VLAN |
|------|------|------|
| Fa0/1 | Access | 20 (PC2) |
| Fa0/2 | Access | 20 (PC3) |
| Gi0/1 | Access | 20 (to Router0 Gi0/0/1) |
| Fa0/24 | Trunk | 802.1Q to Switch0 |

## What Was Configured
- VLANs 10 (HR) and 20 (IT) created on both switches
- Access ports assigned to the correct VLAN per switch
- 802.1Q trunk between Switch0 (Fa0/24) and Switch1 (Fa0/24)
- Router interfaces configured as gateways for each VLAN
- Inter-VLAN routing via the router's two physical interfaces

## Verified
- PC0 (192.168.10.2) can ping its gateway 192.168.10.1 — 4/4
- PC0 can ping the other VLAN's gateway 192.168.20.1 — 4/4
- Inter-VLAN routing is functioning through the router

## Design Notes

### Why this design works
Putting each VLAN on its own physical router interface is the traditional way to do inter-VLAN routing when the router has enough ports. It is simpler than router-on-a-stick because it does not require 802.1Q encapsulation or subinterfaces.

### Trade-offs vs router-on-a-stick
| Design | Router Ports Used | Complexity |
|--------|------------------|------------|
| One interface per VLAN | 1 per VLAN | Simple, no subinterfaces |
| Router-on-a-stick | 1 total (with subinterfaces) | Requires trunk + dot1Q config |

Both are valid. Router-on-a-stick is more common in modern networks because it scales to many VLANs without needing many physical router ports.

## Lessons Learned
- VLANs must be created on **every switch** they pass through — not just the one with the end device
- The inter-switch link must be a trunk for multiple VLANs to cross
- Each VLAN needs a gateway if it must talk to other networks
- The `show vlan brief` output confirms which ports belong to which VLAN
- `show interfaces trunk` confirms 802.1Q encapsulation and which VLANs are allowed on the trunk

## Commands Worth Remembering
- `show vlan brief` — VLAN-to-port mapping
- `show interfaces trunk` — trunk state and allowed VLANs
- `show interfaces status` — port status and VLAN membership
- `switchport mode access` + `switchport access vlan X` — configure an access port
- `switchport mode trunk` — configure a trunk port
- `show ip interface brief` — verify router interface IPs and status