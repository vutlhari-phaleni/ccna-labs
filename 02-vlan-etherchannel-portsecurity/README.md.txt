# Multi-Switch VLAN, EtherChannel, and Port Security Lab

## Overview
A four-switch enterprise LAN built in Cisco Packet Tracer. Demonstrates VTP domain management, LACP EtherChannel, VLAN segmentation, STP root bridge design, Port Security, and Layer 3 switching with SVIs.

## Topology
![Topology](topology.png)

## Devices
- 1x Cisco 3650-24PS Multilayer Switch (Core-SW)
- 3x Cisco 2960-24TT Access Switches (SW-1, SW-2, SW-3)
- 9x PCs across 3 VLANs

## VLAN and IP Design
| VLAN | Name | Network | Gateway | Access Switch |
|------|------|---------|---------|---------------|
| 10 | HR | 172.16.10.0/24 | 172.16.10.1 | SW-1 |
| 20 | IT | 172.16.20.0/24 | 172.16.20.1 | SW-2 |
| 30 | Guest | 172.16.30.0/24 | 172.16.30.1 | SW-3 |
| 99 | Management | 172.16.99.0/24 | 172.16.99.1 | - |

## EtherChannel Design
Each access switch connects to the Core-SW with 2 links bundled via LACP.

| Port-Channel | Core-SW Ports | Access Switch Ports | Protocol |
|--------------|---------------|---------------------|----------|
| Po1 | Gi1/0/1, Gi1/0/2 | SW-1 Gi0/1, Gi0/2 | LACP (active) |
| Po2 | Gi1/0/3, Gi1/0/4 | SW-2 Gi0/1, Gi0/2 | LACP (active) |
| Po3 | Gi1/0/5, Gi1/0/6 | SW-3 Gi0/1, Gi0/2 | LACP (active) |

## What Was Configured

### VTP
- Core-SW: VTP Server, domain CORP, version 2, password cisco
- SW-1, SW-2, SW-3: VTP Clients
- VLANs 10, 20, 30, 99 created once on Core-SW and propagated to all access switches automatically

### EtherChannel
- LACP (mode active) on both ends of every uplink
- 2 physical links bundled into 1 logical link per access switch
- Provides bandwidth aggregation and link redundancy

### Spanning Tree
- Core-SW configured as root bridge for all VLANs (`spanning-tree vlan 1,10,20,30,99 root primary`)
- Verified: Core-SW is root, all access switches point to Core-SW via root port

### Port Security
- Enabled on all access ports (Fa0/1, Fa0/2, Fa0/3 on each access switch)
- Maximum 1 MAC address per port
- Sticky MAC learning (dynamically learned and saved)
- Violation mode: restrict (drops frames + logs, does not shut down)

### Layer 3 Switching
- SVIs configured on Core-SW for VLANs 10, 20, 30, 99
- `ip routing` enabled on Core-SW
- Inter-VLAN routing handled by Core-SW (no external router needed)

## Verified
- VTP: All VLANs propagated from Core-SW to SW-1, SW-2, SW-3
- EtherChannel: All 3 port-channels show (SU) — Layer 2, in use, with both ports bundled (P)
- STP: Core-SW is root bridge for every VLAN, no ports blocked
- Port Security: Each access port learned exactly 1 sticky MAC, zero violations
- Inter-VLAN ping: PC0 (VLAN 10) can reach PC3 (VLAN 20) and PC6 (VLAN 30)
- Same-VLAN ping: PCs within the same VLAN communicate via the switch fabric

## What Broke and How I Fixed It

### VTP version mismatch
SW-1 ran VTP v1 while Core-SW ran v2. VLANs would not propagate. Fixed by explicitly running `vtp version 2` on the client switches before joining the domain.

### Wrong EtherChannel members
First attempt applied `channel-group` to FastEthernet ports instead of GigabitEthernet. The uplinks are on Gi0/1 and Gi0/2. Removed the wrong config with `no channel-group 1` and reapplied on the correct ports.

### STP root bridge was wrong
SW-1 became root by accident (lowest MAC address won the tie). This caused suboptimal traffic paths. Fixed by setting Core-SW as root primary for all VLANs.

### Config lost after write erase
After erasing startup-config, some interface-level settings persisted in running-config. Had to manually reset ports before reapplying EtherChannel.

## Lessons Learned
- VTP version must match between server and clients, or nothing propagates
- VTP mode "client" cannot set its own version — it inherits from the server
- EtherChannel requires matching configuration on BOTH ends before the channel comes up
- STP root bridge should be the most central switch, not whichever one happens to have the lowest MAC
- Port Security sticky MACs populate only after the connected device sends traffic
- `write erase` does not always clear running-config — verify and clean up manually
- Access switches should never be the STP root in a well-designed network

## Commands Worth Remembering
- `show etherchannel summary` — check port-channel state
- `show vtp status` — verify VTP domain, version, mode
- `show spanning-tree` — identify root bridge and port roles
- `show port-security` — verify sticky MACs and violations
- `show vlan brief` — confirm VLAN-to-port assignments
- `spanning-tree vlan X root primary` — force a switch to be root
- `switchport trunk native vlan X` — set native VLAN on a trunk