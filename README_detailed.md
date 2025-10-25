# Cisco VLAN & Inter-VLAN Routing Project

**Built from scratch using Cisco Packet Tracer | implementing VLANs, Inter-VLAN routing, DNS, and Web Server (HTTP) functionality.**

---

## Overview

This project demonstrates the configuration of multiple VLANs, inter-VLAN routing using a **router with two physical interfaces**, static IP addressing, DNS resolution, and web access testing through an HTTP server. Built and verified in **Cisco Packet Tracer**.

---

## Objectives

- Configure VLANs and assign devices to each VLAN.  
- Enable inter-VLAN routing using two router interfaces.  
- Set up a **DNS Server** for hostname resolution.  
- Add a **Web Server (HTTP)** to serve web content accessible via DNS name.  
- Verify full end-to-end connectivity across VLANs and services.

---

## Device List

| Device Name | Function | Interface | IP Address | VLAN | Notes |
|--------------|-----------|------------|-------------|-------|--------|
| **Router1** | Inter-VLAN & Gateway Router | Fa0/0<br>Fa0/1 | 192.168.10.254<br>203.0.113.2 | 10<br>- | Connects internal VLANs and routes traffic |
| **Internet_Router** | Simulated Internet / Upstream | Fa0/0 | 203.0.113.1 | - | Upstream router (ISP simulation) |
| **Switch0** | Access Switch | - | - | 10, 20 | Connects PCs and Servers |
| **Switch1** | Distribution Switch | Trunk Port | - | 10, 20 | Connects to Router1 and Switch0 |
| **PC0** | Client | Fa0 | 192.168.10.10 | 10 |  |
| **PC1** | Client | Fa0 | 192.168.10.11 | 10 |  |
| **PC2** | Client | Fa0 | 192.168.20.10 | 20 |  |
| **PC3** | Client | Fa0 | 192.168.20.11 | 20 |  |
| **PC4** | Client | Fa0 | 192.168.20.12 | 20 |  |
| **Server0** | DNS Server | Fa0 | 192.168.10.50 | 10 | Provides DNS name resolution |
| **Server1** | Web Server (HTTP) | Fa0 | 192.168.10.60 | 10 | Hosts the `www` webpage for VLANs |

---

## Configuration Summary

### VLAN Configuration (on both switches)
```bash
Switch(config)# vlan 10
Switch(config-vlan)# name VLAN10
Switch(config)# vlan 20
Switch(config-vlan)# name VLAN20

Switch(config)# interface range fa0/1 - 4
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10

Switch(config)# interface range fa0/5 - 8
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 20
```

### Trunk Link Configuration (between Switch0 ↔ Switch1)
```bash
Switch(config)# interface g0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20
```

### Router1 Configuration
```bash
Router1(config)# interface fa0/0
Router1(config-if)# ip address 192.168.10.254 255.255.255.0
Router1(config-if)# no shutdown

Router1(config)# interface fa0/1
Router1(config-if)# ip address 203.0.113.2 255.255.255.0
Router1(config-if)# no shutdown

# NAT (example)
Router1(config)# access-list 1 permit 192.168.10.0 0.0.0.255
Router1(config)# access-list 1 permit 192.168.20.0 0.0.0.255
Router1(config)# ip nat inside source list 1 interface FastEthernet0/1 overload
```

### Internet Router (Upstream) Configuration
```bash
Internet_Router(config)# interface fa0/0
Internet_Router(config-if)# ip address 203.0.113.1 255.255.255.0
Internet_Router(config-if)# no shutdown

# Default route (in a lab the upstream may point to itself or behave as the Internet)
Internet_Router(config)# ip route 0.0.0.0 0.0.0.0 FastEthernet0/0
```

> **Note:** Router1 points its default route to the upstream router (203.0.113.1) so that internal networks can reach the simulated Internet.

---

## DNS Server Configuration

| Setting | Value |
|----------|--------|
| IP Address | 192.168.10.50 |
| Default Gateway | 192.168.10.254 |

Verification (from any PC):
```bash
C:\> nslookup www
Server:  192.168.10.50
Address: 192.168.10.50

Non-authoritative answer:
Name:    www
Address: 192.168.10.60
```

---

## 💻 Web Server Configuration

| Setting | Value |
|----------|--------|
| IP Address | 192.168.10.60 |
| Default Gateway | 192.168.10.254 |
| Web Service | Enabled (HTTP) |
| Test Page | “Welcome to My VLAN Project Web Server” |

Verification:
```bash
C:\> ping www
C:\> start www
```
→ Opens the test webpage in Cisco Packet Tracer’s built-in web browser.

---

## Testing & Verification

| Test | Expected Result |
|------|------------------|
| `ping` between VLAN 10 ↔ VLAN 20 | ✅ Successful |
| DNS lookup (`nslookup www`) | ✅ Resolves to 192.168.10.60 |
| Web access (`start www`) | ✅ Opens webpage |
| Trunk link (Switch0 ↔ Switch1) | ✅ Active |
| Router interfaces | ✅ Up and operational |
| Connectivity to Internet Router | ✅ Router1 can ping 203.0.113.1 |

---

## Project Diagram
![Network Topology](./topology_overview.png)

---

## Files Included

- [`VLAN_internet.pkt`](./VLAN_internet.pkt) → Main project file  
- [`README.md`](./README.md) → Short overview  
- [`README_detailed.md`](./README_detailed.md) → This full documentation  
- [`/screenshots/`](./screenshots) → Contains topology and verification images  

---

## Summary

A complete multi-VLAN network with static addressing, DNS and HTTP integration, and full inter-VLAN communication. Built entirely from scratch in **Cisco Packet Tracer**.  
The setup demonstrates VLAN segmentation, inter-VLAN routing, DNS-based hostname resolution, NAT, and verified end-to-end connectivity.
