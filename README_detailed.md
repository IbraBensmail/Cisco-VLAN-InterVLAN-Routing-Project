# Cisco VLAN & Inter-VLAN Routing Project (Detailed Documentation)

This project demonstrates how to design, configure, and verify a **multi-VLAN network** with **inter-VLAN routing**, **NAT for Internet access**, and **DNS + HTTP servers** in **Cisco Packet Tracer**.

---

## Network Overview

| Device | Role | IP Address | VLAN | Notes |
|---------|------|-------------|-------|-------|
| PC0 | Client | 192.168.10.10 | 10 | VLAN 10 user |
| PC1 | Client | 192.168.10.11 | 10 | VLAN 10 user |
| PC2 | Client | 192.168.20.10 | 20 | VLAN 20 user |
| PC3 | Client | 192.168.20.11 | 20 | VLAN 20 user |
| PC4 | Client | 192.168.20.12 | 20 | VLAN 20 user |
| Switch0 | L3 Switch | 10.0.0.1 / VLAN10 192.168.10.1 | — | Connects VLAN10 to Router1 |
| Switch1 | L3 Switch | 10.0.0.2 / VLAN20 192.168.20.2 | — | Connects VLAN20 to Switch0 |
| Router1 | Gateway / NAT | 192.168.10.254 / 203.0.113.2 | — | Internet gateway router |
| Internet Router | Simulated ISP | 203.0.113.1 | — | Internet simulation |
| DNS & HTTP Server | Server | 192.168.10.50 | 10 | Provides DNS + HTTP service |

---

## Step-by-Step Configuration Summary

### 1️⃣ VLAN and Interface Setup (Switch0 & Switch1)
- Created VLAN 10 and VLAN 20.
- Assigned access ports for respective PCs.
- Configured **trunk link** between the two switches on Gig0/1.
- Enabled Layer 3 routing (`ip routing`) on both switches.

### 2️⃣ Inter-VLAN Routing
- Switch0 and Switch1 both configured with SVI interfaces:
  ```bash
  interface vlan 10
   ip address 192.168.10.1 255.255.255.0
  interface vlan 20
   ip address 192.168.20.2 255.255.255.0
  ```
- Static routes ensure each switch can reach other VLANs via `10.0.0.0/24` inter-switch network.

### 3️⃣ Router Configuration (Router1)
- Router1 connects to Switch0 and the Internet router.
- Configured NAT for Internet access:
  ```bash
  interface FastEthernet0/0
   ip address 192.168.10.254 255.255.255.0
   ip nat inside

  interface FastEthernet0/1
   ip address 203.0.113.2 255.255.255.0
   ip nat outside

  ip nat inside source list 1 interface FastEthernet0/1 overload
  access-list 1 permit 192.168.0.0 0.0.255.255
  ```

### 4️⃣ DNS Server Configuration
- IP: **192.168.10.50**
- Added A record:
  | Name | Address |
  |------|----------|
  | www  | 192.168.10.50 |
- All PCs configured with **Primary DNS: 192.168.10.50**.

### 5️⃣ HTTP Server Setup
- Enabled **HTTP service** on the same server (192.168.10.50).
- Tested using `http://www` from VLAN 10 and VLAN 20 PCs → success.

---

## Testing & Verification

| Test | Source | Destination | Result |
|------|----------|-------------|--------|
| Ping 192.168.10.1 | PC0 | Switch0 VLAN10 SVI | ✅ |
| Ping 192.168.20.2 | PC2 | Switch1 VLAN20 SVI | ✅ |
| Ping 203.0.113.1 | PC4 | Internet Router | ✅ |
| nslookup www | Any PC | 192.168.10.50 | ✅ |
| Open `http://www` | Any PC | Web Server | ✅ |

---

## 📷 Screenshots

Place in `/screenshots/` folder:
- `topology_overview.png`
- `ping_test.png`
- `dns_lookup.png`
- `web_access.png`

---

## File List

```
Cisco-VLAN-InterVLAN-Routing-Project/
├── Cisco-VLAN-InterVLAN-Routing-Project.pkt
├── README.md
├── README_detailed.md
└── screenshots/
    ├── topology_overview.png
    ├── ping_test.png
    ├── dns_lookup.png
    └── web_access.png
```

---

## Notes
- Manual IP addressing used for all devices.
- Inter-switch link (`10.0.0.0/24`) connects Switch0 ↔ Switch1.
- NAT enabled only on Router1 to simulate Internet access.
- Server placed in VLAN10 (accessible across VLANs).

---

### Author
Project by **Bensmail Ibrahim**  
*Cisco Packet Tracer learning & simulation project*

