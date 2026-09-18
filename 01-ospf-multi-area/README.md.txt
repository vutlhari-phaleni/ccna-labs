# Multi-Area OSPF Network with Redundancy

## Overview
A four-router OSPF network built in Cisco Packet Tracer. Demonstrates multi-area OSPF, redundant paths, and load balancing.

## Topology
![Topology](topology.png)

## Devices
- 4x Cisco 1941 Routers (R1, R2, R3, R4)
- 2x Cisco 2960 Switches (SW-A, SW-B)
- 6x PCs
- Serial DCE links between routers
- Copper straight-through links to switches

## IP Addressing
| Router | Interface | IP Address | Network |
|--------|-----------|------------|---------|
| R1 | Serial0/1/0 | 10.0.0.1/30 | R1-R2 link |
| R1 | Serial0/1/1 | 10.0.0.5/30 | R1-R3 link |
| R2 | Serial0/1/0 | 10.0.0.2/30 | R1-R2 link |
| R2 | Serial0/1/1 | 10.0.0.9/30 | R2-R4 link |
| R3 | Serial0/1/0 | 10.0.0.6/30 | R1-R3 link |
| R3 | Serial0/1/1 | 10.0.0.13/30 | R3-R4 link |
| R4 | Serial0/1/0 | 10.0.0.10/30 | R2-R4 link |
| R4 | Serial0/1/1 | 10.0.0.14/30 | R3-R4 link |
| R3 | Gi0/0 | 172.16.10.1/24 | VLAN 10 LAN |
| R4 | Gi0/0 | 172.16.20.1/24 | VLAN 20 LAN |

## What Was Configured
- OSPF process 1 on all routers (Area 0)
- Loopback interfaces for stable router IDs
- Redundant paths between R1/R2/R3/R4
- DHCP on R3 (VLAN 10) and R4 (VLAN 20)
- SSH v2 on all routers
- VLANs 10 and 20 with trunks between switches and routers

## Verified
- OSPF neighbors: FULL on all adjacencies
- Load balancing: R4 loopback reachable via both R2 and R3
- DHCP: PCs received IPs, gateways, and DNS automatically
- SSH: Logged in from PC0 to all 4 routers remotely
- End-to-end ping: 100% success across all VLANs and routers

## What Broke and How I Fixed It
- Serial interface names were wrong — the 1941 uses Serial0/1/0 and Serial0/1/1, not Serial0/0/0. Checked with show ip interface brief.
- R1's ARP table was empty — R4 wasn't advertising correctly. Fixed with clear ip ospf process.
- Router-to-switch link didn't come up — router interfaces default to administratively down. Fixed with no shutdown.
- PC could not reach gateway — VLAN/native VLAN mismatch. Fixed by setting switchport trunk native vlan 10 (and 20 on the other side).

## Lessons Learned
- OSPF load balancing uses equal-cost paths automatically
- show ip ospf neighbor only shows directly connected routers
- DHCP binding tables in Packet Tracer sometimes display empty — verify on the client with ipconfig /all
- Native VLAN must match between the trunk port and the router's physical interface when using plain routed interfaces