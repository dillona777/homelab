# IP Plan

This document summarizes the addressing used in the hybrid homelab: VLANs on the physical core, point-to-point links, tunnel overlay/underlay, and key GNS3 site ranges.

---

## VLANs – Physical Core

| VLAN | Purpose                    | Subnet              | Notes                                                   |
|------|----------------------------|---------------------|--------------------------------------------------------|
| 10   | Servers                    | 192.168.10.0/24     | Proxmox VMs (AAA/RADIUS, DNS, GNS3 VM, etc.)          |
| 20   | Hosts / Endpoints          | 192.168.20.0/24     | Wired clients / test machines for 802.1X, etc.        |
| 99   | Management / NTP           | 192.168.99.0/24     | Switch SVIs, Proxmox mgmt, iDRAC; **NTP source** lives here on the R720 Proxmox host. |
| 200  | IPsec/GRE Transit (Edge)   | 172.16.200.0/29     | Used on the GNS3 Edge side as part of the T1 underlay. |

> Gateways for each VLAN are provided by Catalyst 3560X SVIs on the core (see device configs for exact IPs).

---

## Point-to-Point Links (Physical)

### R1 ↔ SW2 (Core Link)

- Subnet: **192.168.100.0/30**
- R1 (Cisco 2811) interface: `192.168.100.1/30`
- SW2 (Catalyst 3560X) interface: `192.168.100.2/30`
- Used as:
  - Primary routed link into the on-prem L3 core.
  - Part of the **T1 underlay path** on the physical side.

### R1 ↔ Home Router / WAN

- Home LAN: **10.0.0.0/24**
- Home router: `10.0.0.1/24`
- R1 outside/WAN-facing interface: `10.0.0.50/24` (static PAT to home LAN)
- Used for:
  - General internet access from lab networks via R1.
  - Upstream for NTP/DNS as needed.

---

## T1 – IPsec + GRE Tunnel

The T1 tunnel connects the physical 2811 (R1) to the GNS3 Edge router.

### Overlay

- Subnet: **10.255.255.0/30**
- R1 `tun0`: `10.255.255.2/30`
- GNS3 Edge `tun0`: `10.255.255.1/30`
- Used for:
  - Routing between the physical core and the GNS3 OSPF site.

### Underlay

> The underlay is **not bound to a single VLAN**; each side uses its own routed segment.

- **Physical side (R1):**
  - Interface: `Fa0/1`
  - IP: `192.168.100.1/30`
  - Routed into the on-prem core.
- **GNS3 side (Edge):**
  - Interface: `G1/0` in **VLAN 200**
  - IP: `172.16.200.2/29`
  - Part of VLAN 200 “IPsec/GRE transit” inside the GNS3 topology.

IPsec protects the underlay traffic; GRE carries the tunnel overlay (10.255.255.0/30).

---

## GNS3 Site Addressing

The GNS3 side simulates a small routed domain with an ASA, edge router, internal routers, and an SNMPv3 NMS segment.

### OSPF Area 0

All internal routed links in the virtual site live in **OSPF Area 0** with **MD5 authentication** enabled.

- General range reserved: **10.1.0.0/16** & **10.10.0.0/16** (p2p /30s)
- Example point-to-point links:
  - `10.1.1.0/30` – INT1 ↔ INT2
  - `10.10.10.0/30` - INT2 ↔ ESW1
  - Additional /30s carved as needed between internal GNS3 routers.
- Some exceptions exist, and include, but are not limited to:
  - `172.16.50.0/24` - INT1 ↔ ASAv (inside)
 
### EIGRP AS 1

Most likely subject to expand or be entirely replaced with BGP.

  - `172.16.100.0/29` - ASAv (outside) ↔ EDGE

> Exact interface addresses are documented in the router/ASA configs under `configs/`.

### SNMPv3 / Tools Segment

- Subnet: **10.10.10.4/30**
- Minimal Ubuntu SNMPv3 tools host: e.g. `10.10.10.6`
- Hung off the EtherSwitch inside the GNS3 topology.
- Used mainly to **validate SNMPv3 auth/priv behavior** against lab devices.

---

## Management & Services

### Management (VLAN 99)

- Subnet: **192.168.99.0/24**
- Examples:
  - Core switch SVI (SW2): `192.168.99.1`
  - L2 switch SVI (SW1): `192.168.99.2`
  - Proxmox management IPs (R720/RS140): `192.168.99.100` & `192.168.99.150`
  - iDRAC: `192.168.99.200`

All NTP is sourced from the **R720 Proxmox host** on VLAN 99. Cisco routers, switches, and ASA point to this IP as their NTP server.

### Servers (VLAN 10)

- Subnet: **192.168.10.0/24**
- Key hosts:
  - AAA / RADIUS server  
  - Authoritative DNS server for `*.gootas.org`  
  - GNS3 VM (bridging into the virtual site)

---

## Summary

- **VLAN 10 / 20 / 99 / 200** segment the physical core into servers, hosts, management/NTP, and IPsec/GRE transit.
- **192.168.100.0/30** and **172.16.200.0/29** together form the **T1 underlay** path.
- **10.255.255.0/30** is the **T1 overlay** between R1 and the GNS3 Edge router.
- The **GNS3 site** uses primarily a 10.x.x.x addressing scheme for OSPF Area 0 p2p links & EIGRP AS 1 peering is only between ASAv outside int. & GNS3 Edge router.
