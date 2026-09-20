# Two-LAN Routing Lab

## Overview
A small two-LAN network built in Cisco Packet Tracer. Demonstrates basic IP addressing, subnetting, and inter-network routing through a single router with two physical LAN interfaces.

## Topology
![Topology](topology.png)

## Devices
- 1x Cisco Router (Router0 / 2911)
- 2x Cisco 2960-24TT Switches (Switch0, Switch1)
- 6x PCs (PC0-PC2 on LAN 1, PC3-PC5 on LAN 2)

## IP Addressing
| Device | Interface | IP Address | Gateway |
|--------|-----------|------------|---------|
| Router0 | Gi0/0/0 | 192.168.1.1/24 | - |
| Router0 | Gi0/0/1 | 192.168.2.1/24 | - |
| PC0, PC1, PC2 | Fa0 | 192.168.1.10-12/24 | 192.168.1.1 |
| PC3, PC4, PC5 | Fa0 | 192.168.2.10-12/24 | 192.168.2.1 |

## Network Design
Each LAN is a separate subnet. Router0 acts as the default gateway for both LANs and routes traffic between them.

## What Was Configured
- Router interfaces Gi0/0/0 and Gi0/0/1 assigned IP addresses and brought up with `no shutdown`
- Each PC given an IP in the correct subnet plus the router's interface as its default gateway
- No VLANs configured — this is a flat Layer 2 network per LAN
- No routing protocol needed — the router automatically knows both networks as directly connected

## Verified
- PC0 ? 192.168.1.1 (own gateway) — 4/4
- PC0 ? 192.168.2.1 (other gateway) — 4/4
- PC0 ? PC3 (192.168.2.10) — 4/4 after first ARP resolution

## Key Concepts Demonstrated

### Directly Connected Routes
The router automatically adds both LANs to its routing table because it has interfaces in each subnet:No static or dynamic routing required — the router knows both networks natively.

### Why ARP Causes the First Ping to Sometimes Drop
The first packet from PC0 to PC3 is lost because PC0 has to ARP for its gateway, and Router0 has to ARP for PC3. Once both ARP caches are populated, all subsequent pings succeed at 100%.

This is normal behavior in real networks too.

### Default Gateway
Each PC needs a default gateway pointing to the router interface on its subnet. Without it, the PC can reach other hosts on its own LAN but has no way to reach other networks.

## Lessons Learned
- The router only needs interfaces on each network to route between them — no routing protocol required for directly connected networks
- Each subnet needs its own router interface (or subinterface) to act as a gateway
- ARP resolution is why the first ping often fails — this is not a bug
- Switches are Layer 2 only — they don't participate in routing between subnets
- `show ip route` will show directly connected networks with a `C` code

## Commands Worth Remembering
- `show ip interface brief` — verify router interfaces have IPs and are up
- `show ip route` — check the routing table for connected networks
- `ipconfig` (on PC) — verify IP, subnet mask, and gateway
- `ping <ip>` — test connectivity
- `arp -a` (on PC) — view the ARP cache