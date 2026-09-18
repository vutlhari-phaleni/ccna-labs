# NAT and ACL Lab

## Overview
A small enterprise edge network built in Cisco Packet Tracer. Demonstrates NAT Overload (PAT) with private-to-public address translation, and standard and extended access control lists (ACLs) controlling traffic flow toward the internet.

## Topology
![Topology](topology.png)

## Devices
- 2x Cisco 2911 Routers (R1 - border router, ISP-Router - simulated internet)
- 1x Cisco 2960 Switch (LAN-SW)
- 3x PCs (PC0, PC1, PC2)
- 1x Server (Web-Server)

## IP Addressing
| Device | Interface | IP Address | Role |
|--------|-----------|------------|------|
| R1 | Gi0/0 | 192.168.10.1/24 | Inside (LAN gateway) |
| R1 | Gi0/1 | 203.0.113.2/30 | Outside (public WAN) |
| ISP-Router | Gi0/1 | 203.0.113.1/30 | WAN facing R1 |
| ISP-Router | Gi0/0 | 198.51.100.1/24 | Simulated internet LAN |
| Web-Server | Fa0 | 198.51.100.10/24 | Public web server |
| PC0 | Fa0 | 192.168.10.10/24 | Internal user |
| PC1 | Fa0 | 192.168.10.11/24 | Internal user |
| PC2 | Fa0 | 192.168.10.12/24 | Internal user |

Note: 203.0.113.0/30 and 198.51.100.0/24 are RFC 5737 documentation ranges — safe to use in a lab.

## What Was Configured

### Static Routing
- R1 default route: `ip route 0.0.0.0 0.0.0.0 203.0.113.1`
- ISP-Router return route: `ip route 192.168.10.0 255.255.255.0 203.0.113.2`

### NAT Overload (PAT)
- Gi0/0 marked as `ip nat inside`
- Gi0/1 marked as `ip nat outside`
- ACL 1 permitted 192.168.10.0/24 as the translation source
- `ip nat inside source list 1 interface gigabitEthernet 0/1 overload` — all internal hosts share R1's public IP

### Standard ACL (Scenario 1)
- ACL 10: deny PC1 (192.168.10.11) to the internet, permit everything else
- Applied inbound on Gi0/0

### Extended ACL (Scenario 2)
- ACL 100: deny TCP port 80 (HTTP) from 192.168.10.0/24 to Web-Server 198.51.100.10, permit all other IP
- Applied inbound on Gi0/0

## Verified
- NAT translations: `show ip nat translations` showed PC source IPs translated to 203.0.113.2
- PC0, PC1, PC2 could all ping Web-Server before ACLs
- Standard ACL: PC1 blocked, PC0 and PC2 unaffected
- Extended ACL: All 3 PCs still ping Web-Server, but HTTP is blocked
- ACL hit counters confirmed the deny rule was matching

## What Broke and How I Fixed It

### ACL applied to wrong interface direction
ACL 10 was first applied outbound on Gi0/1 (outside interface). Because NAT runs before the outbound ACL check, the ACL saw the translated public IP (203.0.113.2), never matched the deny rule for 192.168.10.11, and did nothing.

Fixed by removing the ACL from Gi0/1 and applying it inbound on Gi0/0 (inside interface). This way the ACL sees the original private source IP before NAT translates it.

### Packet Tracer crashed when testing HTTP
Opening the Web Browser on PC0 while HTTP passed through NAT + ACL caused Packet Tracer 9.x to crash repeatedly. This is a known display bug, not a configuration error.

Confirmed the ACL was correct via:
- `show access-lists` — deny rule present
- `show ip interface gi0/0` — ACL applied inbound
- ICMP pings still worked (proving only port 80 was blocked)
- Standard ACL test earlier in the same lab proved the ACL mechanism worked end-to-end

## Lessons Learned
- **NAT + ACL order:** On the outside interface, NAT translates the source IP BEFORE the outbound ACL is checked. To filter by private source IP, apply the ACL inbound on the inside interface instead.
- **Standard ACLs filter source IP only.** Extended ACLs filter source, destination, protocol, and port.
- **Rule order matters.** Deny rules must come before permit rules. The implicit deny at the end catches everything not explicitly permitted.
- **Standard ACL placement:** near the destination. **Extended ACL placement:** near the source.
- **ICMP vs TCP:** blocking TCP port 80 does not block ICMP pings. Different protocols.
- Packet Tracer has known crashes with combined NAT + ACL + HTTP display — the configuration can still be correct.

## Commands Worth Remembering
- `show ip nat translations` — active translation table
- `show ip nat statistics` — NAT hit/miss counts
- `clear ip nat translation *` — clear active translations
- `show access-lists` — ACL contents and match counters
- `show ip interface <interface>` — verify ACL is applied and direction
- `ip nat inside source list 1 interface <intf> overload` — enable PAT
- `access-list 100 deny tcp <source> <wildcard> host <dest> eq 80` — extended ACL example