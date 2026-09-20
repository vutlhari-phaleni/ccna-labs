# IPv6 Dual-Stack Routing Lab

## Overview
A two-router network running both IPv4 and IPv6 simultaneously (dual-stack) built in Cisco Packet Tracer. Demonstrates IPv6 addressing, link-local addresses, IPv6 static routing, and dual-stack operation on the same physical interfaces.

## Topology
![Topology](topology.png)

## Devices
- 2x Cisco Routers (R1, R2 / 2911)
- 2x Cisco 2960-24TT Switches (SW-A, SW-B)
- 4x PCs (PC0, PC1 on SW-A; PC2, PC3 on SW-B)

## IPv4 Addressing (Dual-Stack Half 1)
| Device | Interface | IP Address | Gateway |
|--------|-----------|------------|---------|
| R1 | Gi0/0 | 10.0.1.1/24 | - |
| R1 | Gi0/1 | 10.0.0.1/30 | - |
| R2 | Gi0/0 | 10.0.2.1/24 | - |
| R2 | Gi0/1 | 10.0.0.2/30 | - |
| PC0 | Fa0 | 10.0.1.10/24 | 10.0.1.1 |
| PC1 | Fa0 | 10.0.1.11/24 | 10.0.1.1 |
| PC2 | Fa0 | 10.0.2.10/24 | 10.0.2.1 |
| PC3 | Fa0 | 10.0.2.11/24 | 10.0.2.1 |

## IPv6 Addressing (Dual-Stack Half 2)
| Device | Interface | Global IPv6 | Link-Local | Gateway |
|--------|-----------|-------------|------------|---------|
| R1 | Gi0/0 | 2001:db8:1::1/64 | fe80::1 | - |
| R1 | Gi0/1 | 2001:db8:12::1/64 | fe80::1 | - |
| R2 | Gi0/0 | 2001:db8:2::1/64 | fe80::2 | - |
| R2 | Gi0/1 | 2001:db8:12::2/64 | fe80::2 | - |
| PC0 | Fa0 | 2001:db8:1::10/64 | auto | 2001:db8:1::1 |
| PC1 | Fa0 | 2001:db8:1::11/64 | auto | 2001:db8:1::1 |
| PC2 | Fa0 | 2001:db8:2::10/64 | auto | 2001:db8:2::1 |
| PC3 | Fa0 | 2001:db8:2::11/64 | auto | 2001:db8:2::1 |

Note: 2001:db8::/32 is the RFC 3849 documentation prefix — safe for lab use.

## What Was Configured

### IPv4 (baseline)
- IP addresses on router interfaces
- Static routes: R1 ? 10.0.2.0/24 via 10.0.0.2, R2 ? 10.0.1.0/24 via 10.0.0.1
- Static IP addresses on the PCs with default gateways

### IPv6
- `ipv6 unicast-routing` enabled on both routers (required — IPv6 routing is off by default)
- Global unicast IPv6 addresses assigned to all four router interfaces (`2001:db8:.../64`)
- Manual link-local addresses assigned to each router interface (`fe80::1` on R1, `fe80::2` on R2)
- Static IPv6 routes: R1 ? 2001:db8:2::/64 via 2001:db8:12::2, R2 ? 2001:db8:1::/64 via 2001:db8:12::1
- IPv6 addresses manually configured on the PCs (Packet Tracer requires manual config for this lab)

## Verified

### IPv4
- PC0 ? 10.0.1.1 (own gateway) — 4/4
- PC0 ? 10.0.2.10 (PC2, across routers) — 4/4

### IPv6
- R1 ? 2001:db8:12::2 (R2's link) — 5/5
- PC0 ? 2001:db8:1::1 (own gateway) — 4/4
- PC0 ? 2001:db8:2::10 (PC2, across routers) — 4/4

### Dual-Stack
Both IPv4 and IPv6 simultaneously active on the same physical interfaces.

## Key Differences Between IPv4 and IPv6

| Feature | IPv4 | IPv6 |
|---------|------|------|
| Address size | 32-bit | 128-bit |
| Address format | Dotted decimal (192.168.1.1) | Hex groups (2001:db8::1) |
| Subnet mask | Dotted decimal (255.255.255.0) | Prefix length (/64) |
| Routing on by default | Yes | **No** — must enable `ipv6 unicast-routing` |
| Auto address | DHCP optional | Link-local auto-generated always |
| Address resolution | ARP | Neighbor Discovery (ND) |
| Broadcast | Yes | No — replaced with multicast |
| Interface command | `ip address` | `ipv6 address` |

## Common IPv6 Pitfalls Hit in This Lab

### Forgetting `ipv6 unicast-routing`
Cisco routers do not route IPv6 by default. Without this command, the router accepts IPv6 addresses on interfaces but will not forward IPv6 packets between them. First thing to verify if IPv6 routing fails.

### Confusion about link-local vs global addresses
Every IPv6-enabled interface auto-generates a link-local address (fe80::/10). These are only valid on the local segment and are used for Neighbor Discovery, routing protocol adjacencies, and next-hop resolution. Global unicast addresses (2001:db8::/32 in this lab) are the routable addresses.

### PC IPv6 in Packet Tracer
Packet Tracer's PCs don't reliably auto-configure via SLAAC in all versions. Manual IPv6 configuration was used for stability.

## Lessons Learned
- `ipv6 unicast-routing` is mandatory on routers — the single most common IPv6 mistake
- Link-local addresses are always present and are used by routing protocols for next-hop resolution
- IPv6 static routes are configured the same way as IPv4 (`ipv6 route <prefix>/<len> <next-hop>`)
- Dual-stack means running both protocols independently — no translation required
- Verifying with `show ipv6 interface brief` and `show ipv6 route` is essential
- IPv6 addresses are case-insensitive and can be abbreviated with `::` for consecutive zero groups

## Commands Worth Remembering
- `ipv6 unicast-routing` — enable IPv6 packet forwarding (global config)
- `ipv6 address <addr>/<prefix>` — assign IPv6 address to interface
- `ipv6 address <addr> link-local` — set specific link-local address
- `ipv6 route <prefix>/<len> <next-hop>` — add IPv6 static route
- `show ipv6 interface brief` — verify IPv6 addresses and status
- `show ipv6 route` — view IPv6 routing table
- `show ipv6 neighbors` — view IPv6 equivalent of ARP cache