# Small Office / Helpdesk Network Lab

## Overview
A complete Small Office/Home Office (SOHO) network built in Cisco Packet Tracer. This lab simulates a typical IT Helpdesk environment, demonstrating routing between a wired LAN and a wireless network, centralized DHCP, and end-to-end connectivity.

## Topology
![Topology](topology.png)

## Devices
- 1x Cisco PT8200 Router (Gateway)
- 1x Cisco 2960 Switch
- 1x Cisco WRT300N Wireless Router
- 1x Server-PT (DHCP & File Server)
- 3x PCs (2 wired, 1 wireless)

## Network Design
| Device | Interface | IP Address | Role |
|--------|-----------|------------|------|
| R1 | Gi0/0/0 | 192.168.10.1/24 | Wired LAN Gateway |
| Server1 | Fa0 | 192.168.10.10/24 | DHCP & File Server |
| Wired PCs | Fa0 | 192.168.10.100-150 | DHCP Clients |
| WRT300N | Internet Port | 192.168.10.x | WAN (connects to office switch) |
| WRT300N | LAN Port | 192.168.20.1/24 | Wireless Gateway |
| Wireless PC | Wireless0 | 192.168.20.100+ | DHCP Client |

## What Was Configured

### Router (R1)
- Configured GigabitEthernet0/0/0 as the default gateway for the office network.

### Server (Server1)
- Configured a static IP (192.168.10.10).
- Enabled the **DHCP Service** to hand out IPs in the 192.168.10.0/24 range.

### Wireless Router (WRT300N)
- Configured the WAN port to get an IP from the office switch.
- Configured the LAN IP as 192.168.20.1 (creating a separate Wi-Fi network).
- Configured WPA2-PSK Wi-Fi security.

### PCs
- Wired PCs set to DHCP and successfully received IP addresses from the Server.
- Wireless PC connected to the "Office-WiFi" SSID and received an IP from the WRT300N.

## Verified
- Wired PCs can ping the router (192.168.10.1) and the server (192.168.10.10).
- Wireless PC can ping the router, server, and wired PCs across different subnets.

## What Broke and How I Fixed It

### DHCP Failure (APIPA)
When I first switched PC0 to DHCP, it failed and self-assigned a 169.254.x.x (APIPA) address.
**Fix:** I checked the Server and realized the DHCP service was not turned "ON". Once I enabled the service and created the pool, the PC successfully received an IP.

### Packet Tracer Freezing on WRT300N
Packet Tracer crashed when trying to save settings in the WRT300N GUI.
**Fix:** I bypassed the GUI and configured the wireless router using the **Config tab** instead, which is more stable.

## Lessons Learned
- 169.254.x.x is an APIPA address, meaning the PC cannot reach a DHCP server.
- Always verify physical connections and service statuses (like DHCP) before assuming a complex routing issue.
- The first ping usually drops a packet due to ARP resolution; subsequent pings will succeed.

## Commands Used
- `show ip interface brief` — verify router and switch interfaces.
- `ipconfig` — verify PC IP addresses.
- `ping` — test end-to-end connectivity.