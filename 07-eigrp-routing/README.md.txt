# EIGRP Dynamic Routing Lab

## Overview
A two-router network built in Cisco Packet Tracer demonstrating EIGRP (Enhanced Interior Gateway Routing Protocol). Shows dynamic route learning between separate LANs, EIGRP neighbor adjacencies, topology table, and how EIGRP differs from OSPF.

## Topology
![Topology](topology.png)

## Devices
- 2x Cisco ISR 4331 Routers (R1, R2)
- 2x Cisco 2960-24TT Switches (SW-A, SW-B)
- 4x PCs (PC0, PC1 on SW-A; PC2, PC3 on SW-B)

## IP Addressing
| Device | Interface | IP Address | Network |
|--------|-----------|------------|---------|
| R1 | Gi0/0/0 | 10.1.1.1/24 | LAN A (left) |
| R1 | Gi0/0/1 | 10.0.0.1/30 | R1-R2 WAN link |
| R2 | Gi0/0/0 | 10.2.2.1/24 | LAN B (right) |
| R2 | Gi0/0/1 | 10.0.0.2/30 | R1-R2 WAN link |
| PC0 | Fa0 | 10.1.1.10/24 | Gateway 10.1.1.1 |
| PC1 | Fa0 | 10.1.1.11/24 | Gateway 10.1.1.1 |
| PC2 | Fa0 | 10.2.2.10/24 | Gateway 10.2.2.1 |
| PC3 | Fa0 | 10.2.2.11/24 | Gateway 10.2.2.1 |

## What Was Configured

### IPv4
- IP addresses on all router interfaces
- Static IPs and gateways on all 4 PCs

### EIGRP
- EIGRP process with **Autonomous System (AS) number 10** on both routers
- Advertised directly connected networks on each router
- `no auto-summary` to disable classful summarization

## Verified

### Routing
- R1 learned `D 10.2.2.0/24 [90/3072] via 10.0.0.2`
- R2 learned `D 10.1.1.0/24 [90/3072] via 10.0.0.1`
- The `D` code means "learned via EIGRP"

### Neighbor Adjacency
- R1 sees R2 as neighbor (10.0.0.2 on Gi0/0/1)
- R2 sees R1 as neighbor (10.0.0.1 on Gi0/0/1)

### End-to-End Connectivity
- PC0 (10.1.1.10) ? PC2 (10.2.2.10) — 4/4
- PC0 ? PC3 (10.2.2.11) — 4/4
- PC2 ? PC0 — 4/4

## Reading EIGRP Output

### show ip eigrp neighbors- **H** = Handle (neighbor index number)
- **Address** = neighbor's interface IP
- **Hold** = time before neighbor is declared down (default 15 sec)
- **SRTT** = Smooth Round-Trip Time
- **RTO** = Retransmission timeout
- **Q Cnt** = queued packets (0 = healthy)

### show ip eigrp topology- **P** = Passive (stable, converged state)
- **1 successors** = number of best paths
- **FD** = Feasible Distance (local metric to destination)
- **(3072/2816)** = (Feasible Distance / Reported Distance)
  - Reported Distance = what the neighbor advertised
- Successor = the best route (installed in routing table)
- Feasible Successor = backup route (in topology but not in routing table)

### show ip protocols
Reveals:
- AS number: 10
- Administrative Distance: **90 internal, 170 external**
- Metric weights (K-values): 1, 0, 1, 0, 0 (bandwidth + delay)
- Auto-summary disabled (because we set `no auto-summary`)

## EIGRP vs OSPF

| Feature | EIGRP | OSPF |
|---------|-------|------|
| Type | Advanced distance-vector | Link-state |
| Metric | Composite (bandwidth + delay) | Cost (bandwidth only) |
| Domain | Autonomous System (AS) | Area |
| Transport | IP protocol 88 | IP protocol 89 |
| Admin Distance | 90 (internal) | 110 |
| Protocol | Cisco proprietary (open since 2013) | Open standard |
| Convergence | Very fast | Fast |
| Multi-vendor | No (historically) | Yes |

**Key point:** If a router learns the same network from both EIGRP and OSPF, EIGRP wins because its AD (90) is lower than OSPF's (110).

## Lessons Learned
- EIGRP uses **Autonomous System numbers**, not areas like OSPF — both routers must use the same AS to become neighbors
- The `no auto-summary` command is important in modern networks — it prevents EIGRP from summarizing networks at classful boundaries
- The topology table contains more than the routing table — it holds successors AND feasible successors (backup routes)
- EIGRP uses a **composite metric** combining bandwidth and delay (with load and reliability optional)
- The metric value 3072 is a result of the bandwidth + delay calculation of the path
- EIGRP's administrative distance of 90 makes it more trusted than OSPF (110), RIP (120), or static routes to a point — but static routes still have AD of 1
- The neighbor table, topology table, and routing table are the three key tables EIGRP maintains

## Commands Worth Remembering
- `router eigrp <AS-number>` — enable EIGRP
- `network <network> <wildcard>` — advertise a network
- `no auto-summary` — disable classful summarization
- `show ip eigrp neighbors` — view neighbor adjacencies
- `show ip eigrp topology` — view full EIGRP topology table
- `show ip route eigrp` — view EIGRP-learned routes only
- `show ip protocols` — view protocol details (AS, AD, K-values)